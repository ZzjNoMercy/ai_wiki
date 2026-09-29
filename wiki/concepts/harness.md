---
title: Harness（模型外层执行系统）
type: concept
sources:
  - read-later-4445b9b74d/later_abadf6c614f140da9b1d1fb5-0fce80db03-bcc916448a53.md
  - read-later-4445b9b74d/later_84091bcce34b4bab91a7ad57-f00ab3bff8-26d6d03086bf.md
  - conversation-synthesis-session-76a1c4236a85-bb4891cbfe/query-152b4c99cc6b-0791a30d01-b37848b8a83a.md
  - conversation-synthesis-codex-local-source-review-e6e0a886b3/effectledger-pi-maka-durable-agent-interpreter-20260825-6fb80d55eb-c745a08e4e5a.md
  - manual-upload-785681c302/opencode-agent-compact-engineering-practice.md-1785921032328-20d84d7559-8130ff6bba3a.md
  - knowledge-file-4be80edb57/knowledge-imported-20260722-harness-profile-tool-description-overrides.md-9fe983cc93-cfbd16066b26.md
  - conversation-synthesis-fff-puddingteams-20260828-9bafcf50d8/fff-agent-code-search-practice-b33c55560c-3388215c0fa6.md
  - read-later-4445b9b74d/later_81b795f2b793454db6ae5691-a15e695237-16d194673310.md
created: 2026-08-31
updated: 2026-09-27
schema_version: 0.4.0
---

# Harness（模型外层执行系统）

## 定义

Harness 是围绕基础模型的模型外层执行系统。它编排执行，并决定模型如何思考与规划、如何调用工具与行动、如何感知和管理上下文、如何保存工件，以及如何评价结果。

从系统架构视角看，[[concepts/agent|Agent]] 可以概括为 `Model + Harness`：Model 提供核心智能，Harness 负责让这种智能在上下文和现实环境中稳定运行。

它不等同于 Agent Loop。Agent Loop 主要描述模型与工具之间的迭代；生产级 Harness 还需要覆盖工作流、权限控制、持久状态、验证、纠错与恢复。可将其理解为模型与现实环境之间的运行时边界：模型负责提出推理和行动，Harness 负责约束行动如何发生、保存发生过什么，并判断任务能否继续或结束。

## 核心职责

- **上下文与状态**：本轮模型看到什么；哪些状态只属于模型投影，哪些状态需要长期保存。
  - 相关页面：[[practices/agent-context-compaction|Agent 上下文 Compact 工程实践]]、[[frameworks/langgraph|LangGraph]]
- **工具与权限**：模型能调用什么；调用前如何校验身份、参数、权限与工作区边界。
  - 相关页面：[[practices/harness-profile-tool-description-overrides|HarnessProfile 工具描述覆盖实践]]、[[frameworks/deepagents|DeepAgents]]
- **执行闭环**：如何规划、执行、验证、保存可信进展，并在失败后继续。
  - 相关页面：[[concepts/loop-engineering|Loop Engineering]]、[[frameworks/long-horizon-harness|LongHorizon-Harness]]
- **副作用与恢复**：外部动作是否发生；是否允许重放；未知结果如何结算、对账或交给人处理。
  - 相关页面：[[practices/durable-effect-ledger|EffectLedger：Agent 副作用账本]]、[[systems/maka|Apache Maka]]、[[frameworks/pi-agent|Pi Agent]]
- **验证与纠正**：如何用环境事实而不是模型自报确认完成；失败后如何重试、阻塞或纠正。
  - 相关页面：[[media/deepseek-of-deepseek-harness|DeepSeek Harness 深度剖析]]、[[media/harness-engineering-for-self-improvement|Harness Engineering for Self-Improvement]]

## Harness 的演进趋势与阶段判断

### 方向预测：元方法论、双向促进与能力内化

Harness 也是 [[concepts/recursive-self-improvement|递归自我改进]] 的近期可编辑层：优化对象可以从 Prompt、结构化上下文和工作流，继续推进到 Harness 代码与优化器代码。不过，允许 Agent 修改 Harness 会打破类似操作系统的抽象边界；可编辑面、权限与安全层、评估器及审计记录需要位于自我修改循环之外。

Lilian Weng 判断，近期实用的 RSI 路径不太可能从模型直接重写自身权重开始，而会先体现在 Harness 层。她提出两条相互衔接的趋势预测：

1. **从解题方法走向元方法论。** Harness engineering 优化的是“获得更好答案的机制”，而不只是某次答案；Harness 自身成为优化对象，逐渐减少任务特定的启发式规则，转向更通用的机制。文章给出的优化对象演进顺序是：instruction prompt → structured context → workflow → harness code → optimizer code。
2. **Harness 与模型能力双向促进。** 成熟 Harness 可以支持模型自我改进的自动化研究闭环；更强模型则能承担更多规划和推理，使 Harness 不必持续堆叠补偿模型弱点的规则，从而减少过度工程并保持系统可持续。

文章用 Prompt engineering 说明“内化”的含义：随着 instruction tuning 与模型推理能力提高，一些原本依赖人工提示模板和启发式措辞触发的行为，会逐渐成为模型的基础能力。这里的 `manual prompt tricks` 不是所有人工编写的 Prompt，也不表示任务说明将消失；它特指为了诱导模型表现出某种能力而设计、且往往依赖具体模板或措辞的人工技巧。

因此，需要区分三类内容：

- **可能被模型内化的能力**：原先需要 Prompt 或 Harness 显式脚手架才能稳定触发的部分规划、推理、反思和自检行为。模型变强后，相关人工规则可能简化。
- **仍需外部明确的任务契约**：目标、约束、上下文和评价标准来自当前任务，不会因为模型变聪明而自动确定。
- **仍需保留的现实接口**：模型与外部上下文和工具的接口仍然存在；权限、持久状态、工具执行与环境验证等 Harness 职责也不能仅靠模型在文本中声称完成。

这形成一条长期边界：模型进步会改变 Model 与 Harness 之间的职责分配，却不会消除 Harness。可内化的是部分认知策略和补偿性启发式；不可凭空内化的是每次任务的真实目标、外部状态、行动权限与完成证据。

[[frameworks/deepseek-harness|DeepSeek Harness]] 展示了把运行时组件全面插件化的方向，但现有证据同时给出反例：深层 Harness 在线自进化仍主要是研究性需求，真实产品通常仍依赖人工评测、审核或下一次运行后生效。

### 2026-08 工程阶段：从功能堆叠转向稳定执行协议

阶段性判断：**下一阶段 Harness 的竞争点不会只是“模型更强、工具更多”，而是谁能把长任务中的状态、权限、副作用、恢复和验证做成稳定协议。**

这里的“协议”不只指网络协议，而是跨模型调用、工具执行、进程重启和人工介入仍保持一致的执行约定：什么是权威状态、何时跨过副作用边界、谁有权批准、哪些结果可以重放、失败后从哪里继续，以及用什么证据判定完成。

这项判断来自以下几类相互补强的信号：

1. **状态从聊天记录转向持久化事实。** 长任务不能只靠模型重新阅读历史来恢复。OpenCode 把完整事件流与模型可见的 Compact 投影分开；Maka 将 Runtime Event Log 作为权威记录；Pi Harness v2 规格则让持久化 total state 充当程序计数器。共同方向是把“发生过什么”从模型推断下沉到 Runtime 与 Storage。
2. **权限从提示词要求转向外部执行边界。** Harness engineering 已把 permission controls 视为独立于 Prompt 的系统职责。DeepAgents 的 HarnessProfile、PuddingClaw 的工具说明覆盖，以及 [[systems/puddingteams|PuddingTeams]] 对 [[frameworks/fff|FFF]] 的受控装配，都体现了权限、工具暴露和 Workspace 边界需要由模型外层统一实施。
3. **副作用从函数调用转向可审计状态机。** [[practices/durable-effect-ledger|EffectLedger]] 的 Intent–Effect–Settlement 协议要求先提交意图，再执行不确定副作用，最后提交结果；未知结果不能被乐观解释成“未执行”，也不能无条件重放。
4. **恢复从“重新规划”转向确定性恢复策略。** [[concepts/loop-engineering|Loop Engineering]] 强调验证、保存可信进展和失败后继续；EffectLedger 进一步区分上下文续聊、模型重新规划、Durable Interpreter 恢复和外部 exactly-once。Compact 负责语义连续性，但不能替代副作用账本。
5. **验证从模型自报转向环境证据。** Harness 需要检查工具和环境的实际结果，而不是相信模型声称“已经完成”。对于 Harness 自我改进，评估器、权限控制、held-out 测试、轨迹审计和关键决策的人类复核还应位于可编辑循环之外。

因此，Harness 的工程重心正在发生责任迁移：

```text
Prompt / 模型自行判断
        ↓
Middleware / Tool policy
        ↓
Runtime / Durable state
        ↓
Storage / Effect ledger
        ↓
可验证、可恢复、可审计的执行协议
```

这不表示模型能力或工具数量不再重要，而是它们越来越像上层能力供给；当任务跨越更长时间、更多工具和更多失败窗口时，系统差异会集中暴露在状态所有权、权限边界、副作用诚实性、恢复策略和验证质量上。

## 自我改进的七项长期挑战

Lilian Weng 在讨论迈向完整 RSI 的瓶颈时列出七项未来挑战：弱且模糊的评估器、上下文与记忆生命周期、负结果、多样性坍缩、奖励黑客、长期成功，以及人类角色。本 Wiki 将它们进一步整理为 [[practices/harness-self-improvement-challenge-checklist|Harness 自我改进七项挑战检查框架]]，用于 Agent 方案设计、架构评审、学习复盘与面试表达。

从 Harness 工程角度看，它们分别要求：

1. 把任务目标转化为可验证信号，同时承认研究品味、新颖性与长期价值难以快速量化；
2. 管理不断增长的上下文与记忆，使长任务能保留关键事实而不被历史淹没；
3. 保存失败尝试并支持从负结果缩小搜索空间；
4. 在进化或强化学习循环中维持探索，避免候选收敛为同一高奖励模板的变体；
5. 将评估器与权限控制置于 Harness 演化循环之外，并配合 held-out 测试、轨迹审计和关键节点的人类复核；
6. 把可维护性、所有权边界、迁移成本、向后兼容与未来调试负担纳入长期评价；
7. 让人类上移到恰当的抽象层，在高影响决策点提供监督与方向校正。

**工程边界：**这七项并非都能由 Harness 单独解决。领域智能、科学品味与价值判断仍依赖模型能力和人类判断；Harness 的职责是把证据、状态、搜索、权限、验证和人工触点组织成可检查的协议，而不是把模糊目标伪装成已经解决的指标。

## 框架与系统实例

- [[frameworks/deepagents|DeepAgents]] 是带规划、文件系统、子 Agent 与上下文管理能力的 Agent Harness；它依赖 LangChain 的 Agent 构件，并使用 LangGraph 运行时。
- [[frameworks/deepseek-harness|DeepSeek Harness]] 以“一切皆插件”扩大可编辑边界，连 Agent Loop、持久化和压缩都可替换；其底层插件框架为 [[frameworks/cordis|Cordis]]。这种开放度面向运行时 Harness 进化，但当前仍可能构成过度工程。
- [[frameworks/long-horizon-harness|LongHorizon-Harness]] 将重点放在 Agent 外部的规划、执行、验证、保存与恢复闭环。
- [[frameworks/pi-agent|Pi Agent]] 已公开 Harness v2 的 durable runtime 规格与脚手架；现有 Raw 证据同时明确其主执行器当时尚未完成，不能表述为已经落地。
- [[systems/maka|Apache Maka]] 已把工具调用的持久化意图、执行和结果结算接入 Runtime，实现了更具体的副作用边界。
- [[systems/opencode|OpenCode]] 通过结构化滚动摘要、完整事件流和 Context Epoch 管理长会话的模型投影与恢复边界。
- [[systems/puddingclaw|PuddingClaw]] 通过 DeepAgents HarnessProfile 调整工具暴露语义；[[systems/puddingteams|PuddingTeams]] 则把搜索能力作为受控 Harness 能力装配并约束 Workspace 边界。

## 规模化运营：Uber Software Factory（2026-08）

Uber 的实践补充了 Harness 的「规模化经济控制面」：同一组织内的交互式 Harness 通过统一 Wrapper 对齐安装、配置、认证与成本观测；模型上下文即使支持 1M Token，也在 400K 触发自动压缩，并默认使用 Medium Reasoning，以平衡表现、缓存突增和重复输入成本。

工具暴露也属于 Harness 的上下文治理。Uber 将 1,000 多个 MCP Server 收口到统一 Gateway；预装 100 多个工具曾给初始 Prompt 增加约 50K–70K Token Schema，并在后续 Turn 重复发送。其替代方案是 Tool Search 与 CLI 动态解析，只在调用时装入所需工具；Code Mode 则把多次轮询和批处理放进子进程，只把最终摘要送回模型上下文。

更高层的变化是把 Harness 从「每个用户 Session 的执行壳」提升为 Agent 产品组合的统一运行与治理平面：[[practices/agent-workload-productization-ladder|Agent 工作负载产品化阶梯]]描述任务如何从 Raw Session、Skill、本地/云端通用 Agent 走向 Specialized Agent；[[practices/outcome-based-agent-economics|面向结果的 Agent 单位经济性]]和[[practices/benchmark-driven-agent-model-routing|基于真实任务 Benchmark 的模型路由]]则把运行成本映射到完成工作、质量与可靠性。

## 边界与反例

- EffectLedger 不等于 exactly-once。外部系统缺少幂等键、事务、状态查询或 reconciliation 时，Harness 只能诚实记录结果未知并阻塞或请求人工处理。
- Compact 解决模型上下文连续性，不证明工具副作用是否已经发生；语义恢复和执行位置恢复不能混为一谈。
- 固定 Schema、测试与验证器都可能遗漏事实或被奖励黑客利用；评估信号弱、慢或含糊时，自我改进结论必须保持保守。
- 小型、短时、低副作用任务不一定需要完整 Durable Runtime。协议化的收益随任务时长、并发、失败概率、审计要求和外部副作用风险上升。
- “更多项目采用某个设计”不自动等于标准已经形成；两个一致实现只能视为 emerging signal，稳定趋势应等待更多独立实现和跨时间证据。

## 趋势刷新规则

本页同时作为 Harness 的长期概念页和阶段性趋势记录。后续刷新时：

1. 保留稳定定义，只在概念边界变化时修改；
2. 趋势按时间窗口增加或更新，明确区分源码事实、综合判断和反向信号；
3. 单一项目的一次迁移不升级为趋势；两个一致观察标记为 emerging signal，优先以三个以上跨时间或跨项目观察确认趋势；
4. 每次刷新补充对应 Raw 不可变快照，并记录观察覆盖范围与证据缺口；
5. 源码追踪日报必须先成为 `raw/manifest.jsonl` 中的不可变快照，才能列入本页正式 `sources`；未摄取的日报只能作为分析背景。

## 关系

- relates_to → [[concepts/agent|Agent（智能体）]]
- derived_from → [[media/harness-engineering-for-self-improvement|Harness Engineering for Self-Improvement]]
- relates_to → [[media/deepseek-of-deepseek-harness|The deepseek of DeepSeek Harness]]
- relates_to → [[concepts/loop-engineering|Loop Engineering]]
- relates_to → [[concepts/recursive-self-improvement|递归自我改进]]
- relates_to → [[practices/evidence-driven-bounded-harness-evolution|证据驱动的受约束 Harness 演化闭环]]
- relates_to → [[practices/harness-self-improvement-challenge-checklist|Harness 自我改进七项挑战检查框架]]
- relates_to → [[practices/agent-context-compaction|Agent 上下文 Compact 工程实践]]
- relates_to → [[practices/durable-effect-ledger|EffectLedger：Agent 副作用账本]]
- relates_to → [[practices/harness-profile-tool-description-overrides|HarnessProfile 工具描述覆盖实践]]
- relates_to → [[frameworks/deepagents|DeepAgents]]
- relates_to → [[frameworks/langgraph|LangGraph]]
- relates_to → [[frameworks/deepseek-harness|DeepSeek Harness]]
- relates_to → [[frameworks/cordis|Cordis]]
- relates_to → [[frameworks/long-horizon-harness|LongHorizon-Harness]]
- relates_to → [[frameworks/pi-agent|Pi Agent]]
- relates_to → [[frameworks/fff|FFF]]
- relates_to → [[systems/maka|Apache Maka]]
- relates_to → [[systems/opencode|OpenCode]]
- relates_to → [[systems/puddingclaw|PuddingClaw]]
- relates_to → [[systems/puddingteams|PuddingTeams]]
