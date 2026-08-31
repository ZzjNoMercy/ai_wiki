---
title: Pi Agent
type: software_framework
sources:
  - conversation-correction-session-6afa50dc1ea8-10d3fb8d76/query-18f0f64be0c2-pi-only-7374e46112-8cbd01cdcc8e.md
  - conversation-synthesis-codex-local-source-review-e6e0a886b3/effectledger-pi-maka-durable-agent-interpreter-20260825-6fb80d55eb-c745a08e4e5a.md
created: 2026-08-13
updated: 2026-08-31
schema_version: 0.4.0
---

# Pi Agent

Pi Agent 是一个覆盖模型适配、Agent loop 与 coding-agent 使用层的 Agent SDK 框架。本页同时区分它当前实际运行的 agent loop，以及仓库中仍处于实验性脚手架阶段的 Harness v2。

## 框架定位

- 三层架构：**pi-ai / agent-core / coding-agent**。
- 生产级 pi 规模为“上万行”，另有从它提炼的 600 行教学版 nanopi。
- 能力包括 Agent Loop、多供应商模型抽象、工具系统、消息与事件系统、上下文压缩和 Session Tree。
- 使用层支持交互 / Print / JSON / RPC 模式、Skills、扩展、包管理、CLI、Provider 配置与多平台部署。

## 当前 agent loop：易扩展，但不是 durable interpreter

当前实际执行链路位于 `packages/agent/src/agent-loop.ts`，核心顺序是：

1. 发出 `tool_execution_start`
2. 执行 `beforeToolCall` hook
3. 调用 `tool.execute`
4. 执行 `afterToolCall` hook
5. 将工具结果交回循环

这很像在 function calling 周围增加 hook，也确实提供了观测、拦截和扩展点。但这些事件和 hook 本身不是 durable commit；进程崩溃后，单靠上下文无法证明工具是否已经产生外部副作用。因此，当前 agent loop 仍主要依赖会话记录做“语义续跑”，不具备副作用级的精确断点恢复。

## Harness v2：设计目标

`packages/agent/docs/harness.md` 描述了一个更强的运行时边界：

> durable intent commit → external effect → durable settlement

也就是 [[practices/durable-effect-ledger|EffectLedger：Agent 副作用账本]] 所称的 Intent–Effect–Settlement（IES）协议。Harness v2 还要求：

- `op.state` 成为显式、完备的程序计数器，而不是只依赖聊天上下文猜测进度。
- 在 effect 前提交稳定的 operation identity 和 intent。
- 在 effect 后提交 success / failure settlement。
- 恢复时由工具的 replay policy 决定：safe 工具可重放，never 工具不得盲目重放。
- 对已结算 operation 去重，避免重复执行。

这是一种面向 Durable Agent Interpreter 的设计：恢复的是程序状态和 effect ledger，而不只是把旧消息重新喂给模型。

## Harness v2 的实际状态

截至本页核对的 `origin/main` 提交 `a79b37334`（最新 tag 为 `v0.84.3`）：

- `AgentHarness` 已公开导出，并存在类型、文档和总体协议。
- `prompt`、`resume`、`executeAction`、`runToCompletion` 等核心方法仍抛出 `HarnessNotImplemented`。
- 当前 agent loop 尚未被 Harness v2 替换，也没有把 intent / settlement 的 durable protocol 融入现行工具执行路径。
- 本地分支当时比 `origin/main` 少 15 个提交，但这些提交没有修改 Harness，因此上述判断不受这 15 个提交影响。

所以更准确的表述是：**Pi 已经写出了 durable harness 的设计和公开脚手架，但当前官方主线还没有完成实现，也没有融合进现行 agent loop。**

## 与 EffectLedger 的关系

Pi Agent **relates_to** [[practices/durable-effect-ledger|EffectLedger：Agent 副作用账本]]。Harness v2 文档与该实践同向，但目前属于设计信号，不应表述为 Pi 已经实现 durable effect boundary。

Pi Agent **relates_to** [[concepts/harness|Harness]]：Harness v2 给出了 durable runtime 的正式设计方向，但主执行器在本页证据时间点仍未完成。

## 相关实现

- **nanopi**：从生产级 pi 提炼的 600 行教学版，PI from Scratch 以五个 TypeScript 文件逐行手写。
- **pi-agent-core**：pi 的核心库，也是 π-agent book 的逐行源码导读对象；它不等于整个 pi。

## 学习资源（relates_to）

- [[media/pi-agent-runbook-tutorial|菜鸟教程 · Pi Agent 教程]] —— 使用手册，零门槛
- [[media/pi-from-scratch|PI from Scratch]] —— 600 行 nanopi 教学版
- [[media/pi-agent-book|π-agent book（books.antinomie.org）]] —— pi-agent-core 逐行源码导读（进行中）
- [[media/dg-zhuya-pi-reading-notes|冬瓜 · 源码精读笔记]] —— 生产级全景精读
- 综合评估见 [[analysis/pi-agent-learning-resources|Pi Agent 学习资源评估]]

## 知识缺口

- 早期 raw 未提供 pi 的官方仓库地址、许可证与出品方等稳定事实。
- Harness v2 的文档和脚手架可能继续演进；“未实现”是上述提交时点的源码状态，不是永久判断。
- 上下文和 Session Tree 能帮助会话续跑，但不能替代外部副作用的 durable identity、settlement 与幂等策略。
