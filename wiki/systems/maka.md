---
title: Apache Maka
type: system
sources:
  - conversation-synthesis-codex-local-source-review-e6e0a886b3/effectledger-pi-maka-durable-agent-interpreter-20260825-6fb80d55eb-c745a08e4e5a.md
created: 2026-08-25
updated: 2026-08-31
schema_version: 0.4.0
---

# Apache Maka

Apache Maka 是一个本地优先的 Agent 工作空间。它的关键差异不只是“支持工具调用”，而是把工具调用的执行边界纳入持久化 Runtime：先提交执行意图，再允许外部副作用发生，最后提交执行结果。

## 核心定位

- 一个 Runtime Host 负责 Agent 运行，而不是让 UI 或普通 function-calling 循环直接掌控副作用。
- Runtime Event Log 是权威状态；UI、上下文和其他视图是从事件生成的投影。
- 工具执行被拆成两个持久化事务和一个外部副作用区间，即 T1 → Effect → T2。
- 这使“模型已经决定调用工具，但进程恰好崩溃”从模糊的上下文问题，变成可以枚举和处理的运行时状态。

## T1 / Effect / T2

### T1：提交执行意图

Runtime 为工具调用创建稳定的 attempt identity，并持久化 prepared / pending 状态。只有 T1 成功提交后，工具实现才获得执行许可。

### Effect：执行外部副作用

Runtime 调用真实的 `tool.impl`。这一段可能访问文件系统、网络、数据库或其他外部系统，也可能超时、抛错或导致进程退出。

### T2：提交执行结果

工具成功或失败后，Runtime 将 outcome 持久化，再把结果发布给 Agent loop。由此，给模型看到的工具结果不再早于其 durable settlement。

本地源码中的关键链路是：

- `packages/runtime/src/tool-runtime.ts`：`prepareDurableToolAttempt` → `commitToolPrepared` → `tool.impl` → `commitToolOutcome`
- `packages/storage/src/sqlite-runtime-store.ts`：以 SQLite 事务实现 T1 / T2
- Runtime Host 将 `runtimeEventStore` 注入为 `runtimeCommitSink`

本页核对的本地 Maka 提交为 `3a9824a7e`。

## 恢复语义

| 崩溃位置 | Durable 状态 | 恢复时的含义 |
|---|---|---|
| T1 之前 | 没有 attempt | 可以按正常控制流重新决定 |
| T1 之后、Effect 之前 | prepared / pending | 已获执行许可，但尚无结果 |
| Effect 期间或之后、T2 之前 | 仍是 prepared / pending | 外部副作用是否发生可能未知 |
| T2 之后 | completed / failed | 已结算，不应再次执行 |

当前实现对 pending attempt 采取保守策略：它建立了可靠的 durable 边界，但并不自动证明所有外部副作用都可安全重放。对于不可幂等工具，恢复仍需要幂等键、外部系统对账或人工处置。

## 与 EffectLedger 的关系

Apache Maka **implements** [[practices/durable-effect-ledger|EffectLedger：Agent 副作用账本]]：它已经把 Intent–Effect–Settlement（IES）协议落到 Runtime Host、事件存储和 SQLite 事务中。

Apache Maka **relates_to** [[concepts/harness|Harness]]：其 Runtime 把工具副作用、持久状态和恢复策略纳入模型外层执行边界。

这不是简单 hook function calling。hook 可以在调用前后观察或拦截；Maka 的边界要求“调用前事件已提交”和“返回模型前结果已提交”，因此改变了副作用发生的授权条件和崩溃恢复语义。

## 边界与知识缺口

- 本页结论来自本地源码核对，不等同于对所有分支、部署方式或未来版本的保证。
- durable intent 能消除“完全不知道 Agent 想做什么”的状态，但不能单独消除 Effect 已发生而 T2 未提交的歧义窗口。
- 要实现真正自动化的断点恢复，工具还需声明重放策略，并与外部系统的幂等或对账能力配合。
