# FFF 在编码 Agent 中的使用、收益与隔离实践

## 背景

编码 Agent 经常需要在大型 Workspace 中定位文件和符号。传统做法通常按次启动 `rg` 或 `fd`，把扫描顺序中的大量命中直接塞入模型 Context。高频关键词（如 TODO、FIXME）可能一次返回几十条甚至更多结果；若噪声文件排在前面，真正相关的文件会被挤到结果末尾或被输出上限截断。

搜索层的第一性目标不是“返回尽可能多的文本”，而是用尽可能少的上下文，把 Agent 带到最可能相关的文件，再按需读取证据。搜索结果是定位线索，不是上下文本身。

## FFF 是什么

FFF 是 Rust 原生、SIMD 加速的文件查找与内容搜索库。`@ff-labs/pi-fff` 把 FFF 接入 Pi，通过 Node binding 在进程内调用，不需要为每次搜索启动一个新的 `rg` 子进程，并在 Session 启动后为工作目录建立后台索引。上游包页面为 https://pi.dev/packages/@ff-labs/pi-fff 。

FFF 在普通扫描之上增加了以下能力：

- 预索引：搜索主要查询已经建立的索引，不必每次重新遍历整个目录。
- Frecency 排序：综合文件的访问频率与最近使用时间，把常用文件提前。
- Git 感知：修改、暂存和未跟踪文件获得更高优先级。
- 查询历史：记录查询与被选文件之间的排序信号，让后续相似搜索更容易命中正确位置。
- 模糊文件查找：可以按概念、路径片段和近似名称定位文件。
- 多关键词搜索：一次调用搜索多个字面量。
- 游标分页：首屏结果保持有界，需要更多结果时沿 cursor 继续。

## 为什么对 Agent 有价值

假设仓库有 33 行 TODO，其中 30 行在一个近期频繁修改但与当前任务无关的噪声文件，另外 3 行在真正相关的实现文件。无排序的全量返回会同时浪费搜索输出 token，并增加关键文件被截断的概率。

FFF 先按 Git 状态和 frecency 对文件排序，再返回有限首屏并提供分页游标。正确工作流变成：

1. 用模糊查找定位候选文件。
2. 在较小范围内执行 grep 或多关键词 grep。
3. 读取少量关键文件。
4. 只有需要更多证据时才翻页。

它的收益不是“搜索不消耗 token”，而是把 token 用在更可能影响结论的文件上。价值在大仓库、重复探索、热点文件明显、TODO/FIXME 噪声多、或需要同时定位多个关联符号时最明显。小仓库中的一次精确搜索，内置 grep/find 往往已经足够。

## 工具选择

### find

用于按文件名、路径片段或概念模糊定位。适合不知道准确文件名时先缩小范围。拿到候选文件后再读取或 grep，不要先遍历整个目录树。

### grep

用于搜索一个符号、字符串或正则。已知大致目录时同时提供相对 `path`，减少噪声。

### multi_grep

用于一次搜索多个字面量；多个 pattern 是 OR 关系。适合同时检查一组关联标识符，避免模型连续调用多次搜索。文件约束只负责过滤文件，不把 pattern 变成正则。首屏未覆盖全部结果时，应使用返回的 cursor 继续，不能根据首屏宣称“仓库里没有其他命中”。

## PuddingTeams Harness 集成方式

PuddingTeams 将 FFF 作为受控 Harness 能力装配，而不是要求用户把 `pi-fff` 手工安装为 Pi 全局 Extension。受控装配用于保证配置、Workspace 和索引边界一致，并过滤可能绕过平台边界的全局 `pi-fff` 实例。

配置分三层：

- Harness 提供所有 Pi Worker 的全局默认：`builtin` 或 `fff`。
- 单个 Pi Worker 可选择 `inherit`、`builtin` 或 `fff`；其中 `inherit` 继承 Harness 的全局 Worker 默认。
- Manager 独立配置 `off`、`builtin` 或 `fff`，默认关闭。Manager 搜索只在 Solo 模式生效；Direct 是纯 Worker 通道，Group 使用协作 relay。

PuddingTeams 使用 override 模式时，Agent 看到的仍是标准工具名 `grep`、`find` 和 `multi_grep`，不需要学习 `ffgrep` 或 `fffind` 等第二套名字。

## 必须分开的三种状态

FFF 的搜索状态、Agent 的模型会话和搜索分页游标不是同一件事：

- FFF 索引与排序数据库按 `workspaceId + canonicalPath` 隔离，保存文件索引、frecency 和查询选择历史。
- Worker 模型上下文按 `roomSessionId + workerId` 隔离，由 Worker Session Handle 指向真正的 Agent Session。
- FFF 分页游标受 Workspace 和当前 FFF Session 约束，只表示某次搜索的下一页位置。

因此，FFF history 只影响搜索排序，不保存用户或 assistant 的聊天正文。若一个新建房间 Session 能复述上一段对话，根因应优先检查旧 Worker Session Handle 是否被错误复用，或模型输入是否注入了旧 transcript，而不是把问题归因于 frecency。

正确的房间 Session 语义是：同一 Workspace 中新建房间 Session，也必须为每个 Worker 建立新的模型会话；切回旧房间 Session 时，才恢复该房间自己的 Worker Session Handle。房间 Session 若只隔离 UI 消息、却继续使用 Window 级“最新 Worker Handle”，就不是真正的会话隔离。

## Workspace 安全边界

受控 FFF 应当：

- 把搜索根固定为已登记且可信的 canonical Workspace。
- 按 Workspace 使用独立索引目录和排序数据库。
- 禁止绝对路径、`~/`、`../`、root 或 home 扫描。
- 给 cursor 增加 Workspace/Session 作用域，拒绝跨范围复用。
- 在调用 FFF 前检查 path 和文件约束的现存路径前缀，拒绝通过 Workspace 内符号链接跳到外部目录。
- Workspace 未登记、未信任或 canonical path 漂移时 fail closed。

用户把 Workspace 外的单个绝对文件路径粘到聊天窗口时，平台应先把文件冻结为当前 Session 的附件，再让 Agent 读取冻结副本；该文件不进入 FFF Workspace 索引。外部目录不能作为普通附件隐式扩大搜索边界，必须登记为 Workspace，或配置为明确且有生命周期的临时挂载范围。

## 验证方法

可以准备两个或三个只存在于当前 Workspace 的关键词，让 Agent 只调用一次 `multi_grep`，并要求报告实际工具名和命中文件。验收应检查：

1. 执行记录中的实际工具名是 `multi_grep`。
2. 命中文件全部位于当前 Workspace。
3. 首屏结果有界，更多结果通过 cursor 表达。
4. 新建房间 Session 后再次搜索时，Worker 使用新的 Session Handle。
5. 切回旧房间 Session 时，才恢复旧 Worker 上下文。
6. Workspace 内指向外部目录的符号链接作为 path 或 constraint 时被拒绝。

不要只看开发服务器终端日志判断工具是否被调用。工具调用与结果属于 Session 事件，默认不一定逐条打印到标准输出；应以执行时间线、Worker Session Handle 和实际返回结果为准。

## 常见误区与失效条件

- 首屏没有某个关键词，不代表仓库不存在。FFF 会排序并分页，应增加精确 path/constraints 或沿 cursor 继续。
- 搜索回答提到“上一轮”，不等于 FFF 泄漏聊天历史。先审计 Worker Session Handle 与 transcript 注入。
- PuddingTeams 已受控集成 FFF 时，不应再手工安装全局 pi-fff，否则会产生两套配置和索引来源。只有直接使用原生 Pi、不经过 PuddingTeams 时，才按上游说明执行 `pi install npm:@ff-labs/pi-fff`。
- FFF 是更好的检索入口，不是穷举证明工具。要证明整个仓库绝无某模式，需要精确约束、完整翻页，必要时使用确定性审计命令。
- FFF 不替代文件读取、代码理解、测试和验收。
