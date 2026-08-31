---
title: 交接简报式 Compaction 摘要实践
type: engineering_practice
sources:
  - conversation-synthesis-session-bc0c821a04d9-9bf943cf4e/query-cd31e5bdfb00-72cbb0465f-baa1ba11a8b2.md
created: 2026-08-14
updated: 2026-08-14
schema_version: 0.4.0
---

# 交接简报式 Compaction 摘要实践

## 问题

编码 Agent（如 Pi、Claude Code、Codex）的对话历史随工作推进持续增长。每次交互 Agent 都会把完整对话历史发给 LLM，一旦超过上下文窗口上限，下一次请求就会返回类似 `Request exceeds the maximum size` 的错误，无法继续。直接开启新会话会丢失先前的决策和未完成的工作。

## 根因

- 对话历史无界增长，而 LLM 的上下文窗口有限。
- LLM 提供商的 prompt caching 依赖精确的前缀匹配；任何改变前缀结构的操作（如压缩）都会让已缓存前缀失效，从第一个变化的 token 起之后的内容都必须重新计算。

## 方案

1. **交接简报式结构化摘要**：好的编码 Agent 摘要效果应像换班交接简报（handoff briefing）——丢掉现有上下文里大量不再相关的内容，只保留对继续工作仍然重要的上下文；指定结构化章节 goal（目标）、progress（进展）、key decisions（关键决策）。
2. **独立摘要请求**：压缩请求与普通对话请求分离——
   - 独立系统提示词：说“你是一个上下文摘要助手”（context summarization assistant），而非“你是一名资深编码助手”；
   - 独立用户消息：要求生成“此对话分支的结构化摘要，供稍后回来时使用”；
   - 不依赖已有对话历史，因此可以使用不同的 LLM 模型，且不会带来多余成本。
3. **保留最近消息**：保留最近的一定数量消息不做改动，保留量由可配置的 token 预算决定（Pi 当前默认约 20K tokens，约 5 到 20 轮对话）；截断点之前的消息才被提取、序列化并交给摘要模型。
4. **纯文本存储**：摘要以纯文本形式作为 compaction entry 追加到会话，保持可读、可移植（例如在 Pi 中切换模型后仍可继续使用这份摘要）。
5. **触发时机**：自动压缩检查放在一轮对话结束之后（turn 结束后）、且仅在上下文接近窗口上限时触发，以最大程度保留 prompt cache 命中率；一轮进行中遇到溢出错误时提供 mid-turn 兜底压缩。

## 验证

该实践提炼自 Pi 的 compaction 实现（依据 Earendil Engineering 官方博客文章《How Compaction Works in Pi》整理，derived_from [[media/how-compaction-works-in-pi|How Compaction Works in Pi]]）。raw 的参考价值小结指出：摘要 prompt 采用“交接简报”式结构化输出（目标 / 进展 / 关键决策）比自由叙述更实用；压缩使用独立请求 + 独立模型，成本可控；摘要存为纯文本，保持可移植性；触发时机放在 turn 结束且仅在逼近上限时触发，可最大程度保留 prompt cache 命中率；mid-turn 溢出时也要有兜底压缩。

## 适用范围与失效条件

- 适用范围：需要长会话延续的编码 Agent（Pi、Claude Code、Codex 一类）的 compaction 实现，本实践 applies_to [[systems/pi-coding-agent|Pi（编码 Agent）]]。
- 失效条件：mid-turn 溢出仍必须有兜底压缩，否则请求依然会失败；compaction 会破坏既有 prompt cache，压缩后的首次请求必须重新计算（后续请求才会再次受益）；若在上下文远未接近上限时频繁压缩，会引入不必要的摘要成本与缓存重建开销。
