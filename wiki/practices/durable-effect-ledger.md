---
title: "EffectLedger：Agent 副作用账本"
type: engineering_practice
sources:
  - conversation-synthesis-codex-local-source-review-e6e0a886b3/effectledger-pi-maka-durable-agent-interpreter-20260825-6fb80d55eb-c745a08e4e5a.md
created: 2026-08-25
updated: 2026-08-31
schema_version: 0.4.0
---

# EffectLedger：Agent 副作用账本

EffectLedger 是一种面向长运行 Agent 的工程实现：把每次可能产生外部副作用的操作，记录成可恢复、可去重、可审计的持久化状态机。它采用 **Intent–Effect–Settlement（IES）** 协议：先持久化执行意图，再执行副作用，最后持久化结果。

上层运行时可称为 **Durable Agent Interpreter**：它解释并恢复显式程序状态，而不是仅凭对话上下文推测“上次大概执行到哪里”。

## 为什么上下文不是精确断点

有上下文的 Agent 天然拥有一种“伪断点恢复”：重新加载消息、压缩摘要或 Session Tree 后，模型可以继续推理。但上下文通常无法可靠回答：

- 某个工具调用是否真的开始执行？
- 外部 API 是否已经接受请求，但结果还没写回？
- 文件、邮件、支付或部署是否已发生一次？
- 恢复后再次调用会重试，还是造成重复副作用？

根因在于，对话记录描述的是模型看到的历史，不是外部世界的事务账本。Durability 必须跨过 function calling 的工具边界。

## 四层恢复语义

| 层级 | 能恢复什么 | 典型机制 | 仍然缺少什么 |
|---|---|---|---|
| 会话层 | 对话和推理线索 | message history、摘要 | 工具是否真正执行 |
| 控制流层 | 任务执行到哪一步 | 显式 `op.state`、程序计数器 | 外部副作用真相 |
| 副作用层 | 操作的意图与结算状态 | EffectLedger、IES | 外部系统是否支持去重或对账 |
| 世界状态层 | 外部效果的最终事实 | 幂等键、查询、对账、补偿 | 取决于外部系统能力 |

前两层可以让 Agent “继续想”；后两层才让它更可靠地“继续做”。

## IES 协议

### 1. Intent

在调用工具前，原子提交：

- 稳定的 operation / attempt identity
- 工具名和规范化参数，或其摘要
- 所属 run、step 与前序状态
- replay policy
- prepared / pending 状态

Intent commit 成功，是 effect 获得执行许可的前提。

### 2. Effect

执行不可被本地事务完全包住的外部动作，例如调用 API、写文件、发消息、更新数据库或触发部署。

### 3. Settlement

在将结果暴露给 Agent 之前，原子提交：

- success / failure / blocked 等终态
- 工具结果或错误的持久化表示
- 完成时间和必要的外部引用

Settlement commit 成功后，结果才能进入下一轮模型上下文。

## 最小不变量

1. **No effect before intent commit**：意图未提交，不得执行副作用。
2. **No result before settlement commit**：结果未结算，不得交还模型。
3. **Stable identity**：同一逻辑操作在恢复时保持相同 identity。
4. **Completed means never rerun**：已结算操作不得重复执行。
5. **Pending is explicit**：崩溃后的不确定窗口必须成为一等状态，不能被聊天记录掩盖。
6. **Replay policy is data**：safe / never 等策略必须显式保存。
7. **Replay requires current safety**：只有账本与当前工具定义都允许时才能自动重放。
8. **External truth wins**：若外部系统可查询或对账，应以它确认 effect 是否已发生。

## 恢复决策

| 账本状态 | 建议行为 |
|---|---|
| 没有 intent | 回到正常控制流，可重新决定是否执行 |
| pending + replay safe | 使用同一 identity / 幂等键重放，随后 settlement |
| pending + replay never | 禁止盲目重放；查询外部状态、生成 synthetic result、补偿或转人工 |
| completed / failed | 读取已持久化 outcome，不再执行 effect |

最难的是 Effect 已发生、Settlement 未提交的窗口。EffectLedger 不会凭空消灭它；它把这个窗口显式化，并提供 identity、策略和对账入口来处理。

## 工程组成

- Durable event store 或 operation table
- Intent 与 settlement 的原子提交接口
- 工具执行网关，保证所有副作用经过统一边界
- replay policy 与工具版本校验
- 幂等键传播到外部 API
- 恢复扫描器与 pending operation 处置器
- 审计视图、人工确认和补偿入口
- 从 ledger 和 event log 生成的上下文投影

## 本地实现与设计信号

- [[systems/maka|Apache Maka]] **implements** 本实践：本地源码已有 T1 → Effect → T2 的持久化工具边界。
- [[frameworks/pi-agent|Pi Agent]] **relates_to** 本实践：Harness v2 规范描述了 effect sandwich 与恢复策略，但主线实现仍是未完成脚手架。

这两个彼此独立的代码库同时把 durable intent / effect / settlement 推到工具边界，构成一个值得记录的**早期工程信号**。样本只有两个，而且 Pi 尚未落地，因此现在更适合称为 emerging direction，而不是已经被行业充分验证的趋势。

## 适用场景

本实践 applies_to [[concepts/harness|Harness]] 的副作用、持久状态与恢复职责。

- 长时间运行、可能跨进程或跨机器恢复的 Agent
- 会写文件、发消息、下单、部署或修改生产状态的 Agent
- 工具昂贵、不可幂等或需要审计的工作流
- 需要人机协作、审批和失败后对账的任务

## 失效条件与边界

- 如果工具绕开统一执行网关，账本无法覆盖真实副作用。
- 如果 operation identity 不稳定，恢复时无法可靠去重。
- 如果外部系统既不支持幂等，也无法查询或对账，pending 状态只能保守阻塞或人工处置。
- 如果 settlement 写入和结果发布顺序颠倒，模型可能基于未持久化结果继续执行。
- EffectLedger 提升的是副作用恢复语义，不会自动解决模型推理错误、权限设计或业务补偿逻辑。
