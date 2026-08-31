---
title: Compaction（上下文压缩）
type: concept
sources:
  - conversation-synthesis-session-bc0c821a04d9-9bf943cf4e/query-cd31e5bdfb00-72cbb0465f-baa1ba11a8b2.md
created: 2026-08-14
updated: 2026-08-14
schema_version: 0.4.0
---

# Compaction（上下文压缩）

Compaction（上下文压缩）是一种在对话历史超过大语言模型上下文窗口限制时，把历史变成更小的表示、让当前会话得以继续的处理方式。本页依据 Pi 的 Compaction 实现介绍（基于 Earendil Engineering 官方博客文章《How Compaction Works in Pi》整理）编译。

## 为什么需要 Compaction

- 大语言模型（LLM）的上下文窗口是有限的，模型一次请求只能“看到”窗口内的输入。
- 编码 Agent（如 Pi、Claude Code、Codex）一次请求的输入包括：系统提示词、加载的文件（如 AGENTS.md）、工具定义，以及持续增长的对话历史（用户消息、助手回复、工具调用与工具结果）。
- 每次交互，Agent 都会把完整的对话历史发给 LLM。历史一旦超过上下文窗口上限，下一次请求就会返回类似 `Request exceeds the maximum size` 的错误，无法继续。

## 处理上下文溢出的两种思路

1. **直接开启新会话**：丢弃所有累积的上下文。代价是丢失先前的决策和未完成的工作；但有时也合理，因为上下文越大，LLM 的输出质量可能越差（context rot）。
2. **压缩上下文**：把对话历史变成更小的表示，继续当前会话——这就是 compaction。

## 实现方式

- 理论上，compaction 可以用确定性函数实现（保留一部分、丢弃其余部分）。
- 实践中，实现通常用一次 LLM 请求来摘要对话历史，把历史的一部分替换为压缩后的表示，为后续消息和工具调用腾出空间。

```
压缩前: [system + tools][旧轮次][最近保留消息]
压缩后: [system][tools][摘要][最近保留消息][新用户消息]
```

## 与 Prompt Caching 的关系

- LLM 提供商的 prompt caching 依赖**精确的前缀匹配**：在活动会话中，已被模型处理过的上下文可以更低价格复用。
- compaction 会改变前缀结构，从而破坏缓存：保留轮次里的 token 虽然相同，但它们的前缀变了，之前的缓存状态无法复用。
- 压缩后的新请求会重新建立 prompt cache，后续请求再次受益。

## 相关关系

- 被 [[systems/pi-coding-agent|Pi（编码 Agent）]] 实现（implemented_by）。
- 与该概念对应的可复用工程经验见 [[practices/handoff-briefing-compaction-summary|交接简报式 Compaction 摘要实践]]（relates_to）。
