---
title: OpenCode
type: system
sources:
  - manual-upload-785681c302/opencode-agent-compact-engineering-practice.md-1785921032328-20d84d7559-8130ff6bba3a.md
created: 2026-08-05
updated: 2026-08-31
schema_version: 0.4.0
---

# OpenCode

OpenCode 是一个 Agent 平台，其会话压缩（Compact）实现记录于本地源码 `源码合集/opencode`（证据 commit：`c387fe190bbd22e9396d264effe242d157f866d2`，分析日期 2026-08-05）。本页只描述该 commit 的源码事实，不代表其他版本保持完全相同的实现。

OpenCode 实现了（implements）[[practices/agent-context-compaction|Agent 上下文 Compact 工程实践]]。

## Compact 设计

OpenCode 没有把 Compact 实现成一次自由形式的“总结对话”。它把 Compact 设计为带固定 Markdown Schema 的滚动检查点：旧上下文生成或更新 anchored summary，近期上下文按 Token 预算原样保留，完整事件流继续持久化，而下一轮模型只消费“结构化摘要 + recent context”。

Compact 结果规整来自以下组合，而不是 UI 后处理：

- 固定章节和顺序的输出契约；
- 专用、隐藏、禁止工具调用的 compaction agent；
- 对角色、推理、工具调用和工具结果的确定性文本序列化；
- 重复压缩时更新 previous summary，而不是每次从零自由总结；
- 摘要与近期原始上下文分开保存；
- 只有完整成功的 compaction checkpoint 才能切换模型可见上下文。

## 固定输出 Schema

核心模板位于 `packages/core/src/session/compaction.ts`。模板要求模型严格输出以下结构：

```md
## Objective
- [one or two brief sentences describing what the user is trying to accomplish]

## Important Details
- [constraints/preferences, decisions and why, important facts/assumptions, exact context needed to continue, or "(none)"]

## Work State
### Completed
- [finished work, verified facts, or changes made; otherwise "(none)"]

### Active
- [current work, partial changes, or investigation state; otherwise "(none)"]

### Blocked
- [blockers, failing commands, or unknowns; otherwise "(none)"]

## Next Move
1. [immediate concrete action, or "(none)"]
2. [next action if known, or "(none)"]

## Relevant Files
- [file or directory path: why it matters, or "(none)"]
```

附加约束：所有章节必须保留（即使没有内容）；使用简短列表，不使用叙述性长段落；保留精确文件路径、符号、命令、错误文本、URL 和标识符；不得在输出中提及 summary 或 compaction 过程。

## Anchored Summary 更新语义

首次压缩时 `buildPrompt()` 要求创建新的 anchored summary。存在旧摘要时，提示词改为：

```text
Update the anchored summary below using the conversation history above.
Preserve still-true details, remove stale details, and merge in the new facts.
<previous-summary>
...
</previous-summary>
```

这是一种滚动更新：保留仍成立的事实，删除已失效内容，并合并新事实。旧摘要本身成为下一次压缩的显式输入，因此多次压缩后的结构和状态表达更稳定。

## 专用 Compaction Agent

系统提示词位于 `packages/opencode/src/agent/prompt/compaction.txt`，其职责约束包括：

- 只总结提供的历史，不回答原对话；
- 最新轮次可能在摘要外原样保留，因此摘要聚焦仍影响后续工作的旧上下文；
- 遇到 `<previous-summary>` 时执行更新，而不是重新自由总结；
- 严格遵守调用方提供的输出结构；
- 使用与原对话相同的语言；
- 不得提及正在总结、压缩或合并上下文。

该 Agent 在 `packages/opencode/src/agent/agent.ts` 中注册为 `compaction`：

- `mode: "primary"`
- `native: true`
- `hidden: true`
- 所有工具权限为 `deny`

因此 Compact 是隔离的内部模型任务，不会在压缩过程中继续执行原任务或调用工具。

## 消息规范化

`packages/core/src/session/compaction.ts` 在发送给压缩模型前，把持久化消息转换为稳定文本标签：

```text
[User]:
[Assistant]:
[Assistant reasoning]:
[Assistant tool call]: tool(args)
[Tool result]:
[Tool error]:
[System update]:
[Synthetic context]:
[Shell]:
```

媒体不会以内联二进制或 base64 进入摘要输入，而是转换为 `[Attached mime: name]`。工具结果和 Shell 输出最多保留 `2,000` 字符，超出后添加 `[truncated]`。这一层保证压缩模型看到的是与 Provider 消息协议解耦、格式稳定的语义记录，也避免把大体积工具结果和媒体再次塞进摘要请求。

## 触发与上下文拆分

默认常量：

| 配置 | 默认值 | 作用 |
|---|---:|---|
| `DEFAULT_BUFFER` | 20,000 tokens | 为上下文和输出保留安全余量 |
| `DEFAULT_KEEP_TOKENS` | 8,000 tokens | 原样保留的 recent context 预算 |
| `TOOL_OUTPUT_MAX_CHARS` | 2,000 chars | 单个工具或 Shell 输出的摘要输入上限 |
| `SUMMARY_OUTPUT_TOKENS` | 4,096 tokens | Compact 摘要最大输出 |

每次 Provider 调用前，OpenCode 估算完整模型请求 `system + messages + tools`；当估算值超过 `model_context - max(output_allowance, compaction_buffer)` 时才触发自动 Compact。Provider 已经返回 context overflow 时，也可以触发一次恢复性 Compact。

`select()` 从最新消息向前累计 Token，把上下文拆成 `head`（较旧、需要并入滚动摘要的内容）和 `recent`（按 Token 预算保留的近期原始上下文）。Recent 使用 Token 预算而不是固定消息条数，必要时允许在一条序列化消息内部拆分。

## 持久化检查点与 Context Epoch

Compact 发布两类持久化事件：`SessionEvent.Compaction.Started` 和 `SessionEvent.Compaction.Ended`。`Ended` 同时保存 `text`（结构化摘要）和 `recent`（原样保留的近期序列化上下文）。只有模型调用成功、没有 Provider error 且摘要非空时才发布 `Ended`。因此未完成或失败的压缩不会替换当前模型上下文边界。

完整 transcript 仍保留在持久化事件中；模型后续使用完成检查点投影出的“summary + recent”。完成一次 Compact 后进入新的 Context Epoch，Provider 原生的 reasoning、tool message 或签名数据不跨越边界继续复用，从而降低跨 Provider 或加密推理消息不兼容的风险。

## 架构流程

```text
完整持久化事件流
        ↓
估算 system + messages + tools
        ↓ 超过阈值或 Provider overflow
拆分 head / recent
        ↓
previous summary + head
        ↓
隐藏且无工具权限的 compaction agent
        ↓
固定 Schema 的新 anchored summary
        ↓
成功发布 Started / Ended 检查点
        ↓
下一 Context Epoch 使用 summary + recent
```

## 源码证据索引

- `packages/core/src/session/compaction.ts`：固定输出 Schema、消息序列化、Token 预算、滚动摘要、触发器和 Started/Ended 事件。
- `packages/opencode/src/agent/prompt/compaction.txt`：专用 compaction agent 的系统提示词。
- `packages/opencode/src/agent/agent.ts`：隐藏 compaction agent 注册及工具权限。
- `packages/opencode/src/session/compaction.ts`：兼容运行路径中的 recent tail、媒体剥离、插件 hook 和继续执行语义。
- `packages/opencode/src/session/message-v2.ts`：从完整持久化消息投影模型可见 compact context。
- `packages/core/test/session-compaction.test.ts`：固定章节和媒体占位符测试。
- `packages/opencode/test/session/compaction.test.ts`：重复 Compact、recent tail、媒体剥离和预算边界测试。
- `specs/v2/session.md`：durable checkpoint、Context Epoch 和 overflow recovery 的设计约束。

## 证据时间点

OpenCode **relates_to** [[concepts/harness|Harness]]：其 Compact 实现负责长会话中的上下文投影、持久检查点与 Context Epoch 边界。

- 分析日期：2026-08-05
- 本地源码 commit：`c387fe190bbd22e9396d264effe242d157f866d2`
- 本资料描述的是该 commit 的源码事实，不代表其他版本保持完全相同的实现。
