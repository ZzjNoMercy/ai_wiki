# EffectLedger：从上下文续聊到 Agent 可恢复执行——Maka 与 Pi Harness v2 源码对照

## 记录目的

这份材料记录 2026-08-25 对本地 Maka 与 Pi 源码的核查结果，主题是 Agent Harness 正在出现的一种工程方向：把模型请求和工具调用从“进程内函数调用”提升为可持久化、可判定恢复策略的执行协议。

这里暂不把两个项目样本直接宣布为行业共识。更准确的判断是：Maka 的已实现 Runtime v2 与 Pi Harness v2 的正式规格出现了明显趋同，形成了一个可信的 emerging signal。共同方向是把 Agent 从“依赖上下文重新理解任务”推进到“由 Runtime 根据持久化程序计数器恢复执行义务”。

## 工程命名

建议把这一工程实现命名为 **EffectLedger（副作用账本）**。

它负责把一次不可随意重放的外部动作记录成三段式协议：

```text
Intent：原子提交即将执行什么，以及结果将使用什么身份
Effect：执行模型请求、工具调用或其他外部副作用
Settlement：原子提交结果、用量和下一状态
```

协议名建议使用 **Intent–Effect–Settlement（IES）**，中文可称“意图—副作用—结算协议”。

EffectLedger 比“Checkpoint”更准确，因为它并不保存 CPU 调用栈或模型流；比“Exactly-once Runtime”更诚实，因为外部副作用仍可能落在结果未知区间；比“Tool Hook”更完整，因为它最终覆盖模型请求、工具、Hook、队列、取消和恢复状态。

EffectLedger 所服务的上层运行时可以称为 **Durable Agent Interpreter**。两者关系是：Durable Agent Interpreter 负责解释持久化状态机，EffectLedger 负责记录并封闭外部副作用边界。

## 需要区分的四层恢复

1. **上下文续聊**：重新加载消息或摘要，让模型知道“之前聊到哪里”。
2. **Agent 重新规划**：模型根据历史重新决定下一步；这是语义恢复，不是原执行位置恢复。
3. **Durable Interpreter 恢复**：Runtime 根据持久化 phase、operation id、参数、结果 id 和重放策略确定下一动作。
4. **Exactly-once 外部副作用**：只有外部系统提供幂等键、事务、状态查询或 reconciliation 时才可能接近；EffectLedger 本身不作这一承诺。

普通有上下文的 Agent 天然具有前两层，因此看起来像“伪断点恢复”。真正的困难出现在以下窗口：工具已经产生副作用，但进程在 tool result 持久化之前崩溃。仅凭聊天记录无法判断工具未开始、执行中，还是已经完成。

## Page 1：Apache Maka（系统页）

### 页面定位

建议建立 `systems/maka`，类型为 `system`。Maka 是可运行的 local-first Agent workspace，不只是 SDK 或抽象框架。Desktop、TUI、CLI、Bot 和 Eval 统一经过一个 Runtime Host。

### 核心架构

Maka 的 Runtime Event Log 是模型消息、工具调用、工具结果和终止事实的权威记录。UI、下一次模型上下文、Session 展示与恢复状态都是这份记录的 projection。上下文裁剪和 compaction 只改变 provider 输入，不删除已保存的历史证据。

Maka 当前源码已经把工具副作用接入正式的 T1/T2 边界：

```text
模型产生 function call
→ 参数、可用性、循环限制、权限、Runtime ownership 等 preflight
→ T1 commitToolPrepared
   - 原子提交 canonical function_call
   - 原子提交模型不可见的 actions.toolDispatch
   - 保存 operationId、providerToolCallId、canonicalArgsHash、recoveryMode
→ tool.impl(original args)
→ T2 commitToolOutcome
   - 原子提交 function_response
   - 更新 journal 与 operation projection
→ 才允许 tool result 进入下一模型步骤
```

T1 的准确含义不是“副作用已经成功”，而是“所有执行前 guard 已通过，此后不能再安全断言 implementation 没有运行”。如果 T1 提交失败，`tool.impl` 必须执行零次。如果工具已经返回但 T2 提交失败，未提交结果也不能发送给模型。

Maka 没有假装用一个长 SQLite transaction 包住文件、Shell、网络 API 或子 Agent。真实结构是：短 T1 transaction → 外部副作用不确定窗口 → 短 T2 transaction。

### 当前恢复策略边界

Maka 能区分：completed、definitely_not_dispatched、indeterminate、parked、corruption。新协议下只有存在 T1 dispatch 才表示“可能已经执行”；缺少 dispatch 且存在协议 marker 时才可证明 definitely_not_dispatched。对于“存在 dispatch、缺少 response”的未知副作用，当前自动 continuation 保守阻塞，并不擅自重跑。源码已定义 replay_safe、idempotent、reconcile、reattach、outcome_unknown、never_auto_retry 等 recoveryMode，但生产级通用 reconciler 仍是后续层次。

### Maka 源码证据（核查时 HEAD `3a9824a7e`）

- `maka/README.md`：local-first、记录保留、context projection、单 Runtime Host。
- `maka/ARCHITECTURE.md`：Runtime Host 是唯一执行权威，Runtime Event Log 是 canonical source。
- `maka/packages/core/src/runtime-event.ts`：`TOOL_BOUNDARY_PROTOCOL_V1`、`RuntimeEventToolDispatch`、`ToolRecoveryMode`。
- `maka/packages/runtime/src/tool-runtime.ts`：`prepareDurableToolAttempt()` 在 `tool.impl()` 前提交 T1，在工具返回后提交 T2。
- `maka/packages/storage/src/sqlite-runtime-store.ts`：`commitToolPrepared()` 和 `commitToolOutcome()` 的原子 SQLite transaction。
- `maka/packages/runtime-host/src/server/execution-composition.ts`：正式 hosted execution 将 `runtimeEventStore` 注入 `runtimeCommitSink`。
- `maka/docs/architecture/runtime-resume-architecture.md`：T1/T2、crash window 与 RecoveryResolver 的完整设计。

## Page 2：Pi Agent / Harness v2（框架页更新）

### 页面定位

更新已有 `frameworks/pi-agent`，保持 `software_framework` 定位。页面需要明确区分两套东西：

1. 当前实际工作的 `Agent` / `agentLoop`；
2. 已经默认导出、但仍未完成的 `AgentHarness` v2 durable runtime。

不要把 Harness v2 规格描述成当前 Pi 已经具备的运行语义。

### 当前 agentLoop

当前可运行的 `agent-loop.ts` 采用进程内工具管线：

```text
emit tool_execution_start
→ prepareArguments / validate / beforeToolCall
→ tool.execute()
→ afterToolCall
→ emit tool_execution_end 和 toolResult message
```

`beforeToolCall` 与 `afterToolCall` 是有用的 Hook，但 `tool_execution_start` 本身不是原子持久化的副作用 intent。该循环不能仅凭自身状态在进程重启后证明工具是否已经运行。

### Harness v2 规格

Pi 的 `packages/agent/docs/harness.md` 将下一代运行时定义为 durable runtime，并明确提出 “effect sandwich”：

```text
commit intent
→ execute uncertain effect
→ commit settlement
```

它不只包工具 function calling，还计划接管模型请求、工具、Hook、timer、队列、steering、abort、compaction、navigation 与 crash recovery。

Pi 的状态模型与 Maka 不同。它使用 immutable entries、mutable registers 和 append-only usage ledger；`op.state/{operationId}` 保存完整、可替换的 total state，充当 durable program counter。工具在真正执行前提交：

```text
op.tool_args/{operationId}:{stepId}:{index} = effective args
op.state call.status = effect_pending
replay = safe | never
```

工具结算时再原子插入 result entry、usage，并把 call 标成 completed。崩溃恢复时，只有持久化声明和当前工具声明都为 safe 才允许重放；否则在预留 result id 下写入 synthetic interrupted error，而不是让模型猜测或重跑危险工具。

### 当前实现状态

截至 2026-08-25，Pi 远端 `origin/main` 为 `a79b37334`，最新发布 tag 为 `v0.84.3`。Harness v2 已从 experimental 子路径提升到默认 package export，但核心驱动仍是 scaffold：

- `AgentHarness.prompt()` 抛出 `HarnessNotImplemented`；
- `resume()`、`executeAction()`、`runToCompletion()` 同样未实现；
- 测试套件明确命名为 `AgentHarness v2 scaffold`，并验证未完成入口必须显式拒绝；
- 规格计划把旧私有 `executeToolCalls` 重构为 `executeToolBatch`，由 Harness 注入 durable callbacks，并保留旧 loop compatibility wrapper；该整合尚未落入源码主执行路径。

因此它不是隐藏的 experimental API，而是公开导出的 work-in-progress API 与正式设计方向。Session/storage、类型、工具等基础设施已经存在，但 Durable Interpreter 主循环尚未完成。

### Pi 源码证据

- `pi/packages/agent/src/agent-loop.ts`：当前工作的进程内工具执行管线。
- `pi/packages/agent/docs/harness.md`：Effect Sandwich、total state、tools phase、Effects boundary、crash recovery policy。
- `pi/packages/agent/src/harness/agent-harness.ts`：公开 API scaffold 与 `HarnessNotImplemented`。
- `pi/packages/agent/test/harness/agent-harness-scaffold.test.ts`：未完成路径的显式测试。
- `pi/packages/agent/src/index.ts`：`AgentHarness` 已由默认 package export 暴露。
- `pi/packages/agent/CHANGELOG.md`：v0.84.0 将 durable harness API 从 experimental 提升到默认导出，同时说明 v2 仍是 compile-complete scaffold。

## Maka 与 Pi 的共同方向和差异

| 维度 | Maka Runtime v2 | Pi Harness v2 |
|---|---|---|
| 当前状态 | 已接入正式 ToolRuntime 与 SQLite store | 规格完整、API 已导出，主执行器仍是 scaffold |
| 权威状态 | Immutable RuntimeEvent ledger | Entries + registers + usage ledger |
| 程序位置 | 从 canonical events 与 operation projection 判断 | `op.state` total state 直接充当程序计数器 |
| 执行前边界 | T1 `toolDispatch` | `effect_pending` + persisted args |
| 执行后边界 | T2 `function_response` | result entry + completed state |
| 未知危险工具 | 当前 continuation 保守阻塞，等待 reconcile | 默认 synthetic interrupted，不自动重跑 |
| 安全重放 | recoveryMode 为后续策略提供依据 | stored 与 current 均为 safe 才重放 |
| 覆盖范围 | 当前工具边界已经落地，并延伸到 Runtime resume | 设计覆盖 provider、tool、hook、timer 和完整 lane state machine |

这两个实现并非简单互相复制，但都在把责任从 Prompt 和模型判断下沉到 Runtime 与 Storage：模型负责推理和规划，Runtime 负责“发生过什么、能否重放、还欠什么结果”的事实判断。

## EffectLedger 工程实践页建议

建议后续编译出 `practices/durable-effect-ledger`，类型为 `engineering_practice`，标题为“EffectLedger：Agent 副作用账本”。

### 要解决的问题

Agent 上下文只能恢复语义，无法可靠恢复处于 tool call 与 tool result 之间的外部副作用。直接重放可能重复删文件、发消息、支付或创建远程资源；直接宣称成功则会制造虚假历史。

### 最小实现不变量

1. preflight、参数规范化和权限判断发生在 intent 之前；拒绝调用不得伪装为已跨越副作用边界。
2. Intent transaction 必须保存稳定 operation id、有效参数或参数 hash、预留结果 id、replay/recovery policy。
3. Intent 未成功提交时，外部 effect 必须执行零次。
4. Settlement 未成功提交时，结果不得进入下一模型步骤。
5. 重启后只读取 durable state，不根据缺失记录乐观推断“肯定没执行”。
6. completed effect 永不重跑；unknown effect 依据 safe replay、idempotency、reattach 或 reconcile 策略处理。
7. blocked/invalid 调用可以提交 synthetic result，但不能写成 effect_pending/T1 dispatched。
8. 不宣称 exactly-once；对支付、消息、远程资源创建等动作，应把 operation id 传给外部系统作为 idempotency key，或提供查询/reconcile 接口。

### 适用范围

- 长时间运行、可跨进程重启的 Agent；
- 文件修改、Shell、浏览器操作、消息发送、支付、工单和远程 API；
- 并行工具、后台任务、子 Agent、队列和 steering；
- 需要审计、故障恢复或人工接管的生产级 Agent。

### 失效条件

- 外部系统既不支持幂等，也无法查询或 reconcile，而业务又要求 exactly-once；
- 工具错误地声明 replay safe；
- intent 记录没有包含实际执行参数或稳定身份；
- effect 在 Runtime 边界之外偷偷发生，例如 effectful `before_tool` Hook 未进入账本；
- 多个 writer 可无 fencing 地同时驱动同一 operation。

## 趋势判断

证据支持的结论是“正在形成的方向”，不是已经完成的行业趋势：

- Maka 提供了已落地的 T1/T2、operation identity、SQLite 原子提交与保守恢复；
- Pi 提供了更广义的 durable interpreter 规格、total-state program counter 与 safe/never replay policy，但主循环尚未实现；
- 两者共同把 Agent Harness 的核心从“循环调用模型和工具”推进到“解释持久化状态、管理副作用不确定窗口”。

后续若在更多独立 Harness 中持续看到 write-ahead intent、effect_pending、result settlement、idempotency/reconciliation 和 total-state recovery，才适合升级为高置信度趋势。

## 一句话定义

**EffectLedger 是 Agent Runtime 中围绕外部副作用建立的持久化意图—执行—结算协议；它让 Agent 从依赖上下文重新规划的伪断点恢复，演进为能够诚实识别未知结果并执行确定恢复策略的 Durable Agent Interpreter。**
