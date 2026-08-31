---
title: Pi（编码 Agent）
type: system
sources:
  - conversation-synthesis-session-bc0c821a04d9-9bf943cf4e/query-cd31e5bdfb00-72cbb0465f-baa1ba11a8b2.md
created: 2026-08-14
updated: 2026-08-14
schema_version: 0.4.0
---

# Pi（编码 Agent）

Pi 是一种编码 Agent，与 Claude Code、Codex 同类：每次请求都会把系统提示词、加载的文件（如 AGENTS.md）、工具定义以及持续增长的对话历史一起发送给大语言模型。本页依据 Pi 的 Compaction（上下文压缩）实现介绍编译，该介绍基于 Earendil Engineering 官方博客文章《How Compaction Works in Pi》（https://earendil.com/posts/compaction-in-pi/）整理。

## 上下文溢出

- 每次交互，Pi 都会把完整的对话历史发给 LLM。
- 随着工作推进历史不断增长，一旦超过上下文窗口上限，下一次请求就会返回类似 `Request exceeds the maximum size` 的错误，无法继续。

## Compaction 触发时机

- **自动触发**：当上下文总量接近上下文窗口上限时。
- **手动触发**：用户可随时用 `/compact` 命令触发。
- 自动压缩的检查发生在**一轮对话结束之后**（turn 结束后）；在此之前每次请求只是扩展已有 prompt，可以复用已缓存的公共前缀（prompt caching）。
- 如果在一轮**进行中**遇到上下文溢出错误，Pi 也可能在轮中（mid-turn）执行压缩作为兜底。

## 保留最近消息

- 压缩时 Pi 会保留最近的一定数量消息不做改动，保留量由**可配置的 token 预算**决定，当前默认约 20K tokens，大约相当于 5 到 20 轮对话。
- 截断点之前的所有消息会被提取、序列化并交给摘要模型。

## 摘要请求的设计

Pi 的压缩请求与普通对话请求不同：

1. **独立的系统提示词**：不再说“你是一名资深编码助手”，而是“你是一个上下文摘要助手”（context summarization assistant）。
2. **独立的用户消息**：要求生成“此对话分支的结构化摘要，供稍后回来时使用”，并明确指定章节 goal（目标）、progress（进展）、key decisions（关键决策）。
3. **独立请求**：不依赖已有对话历史，因此**可以使用不同的 LLM 模型**，而且不会带来多余成本。

## 摘要的存储

- 压缩结果作为一条 compaction entry 追加到会话中，会话得以继续。
- Pi 把摘要以**纯文本**形式保存在会话里，使压缩后的上下文保持可读、可移植（例如在 Pi 中切换模型后，仍可继续使用这份摘要）。

## 与 Prompt Caching 的关系

- LLM 提供商的 prompt caching 依赖**精确的前缀匹配**。compaction 会改变前缀结构，从而破坏缓存：保留轮次里的 token 虽然相同，但它们的前缀变了，之前的缓存状态无法复用。
- 压缩后的新请求会重新建立 prompt cache，后续请求再次受益。

## 实现的关系

- Pi 实现了 [[concepts/context-compaction|Compaction（上下文压缩）]]（implements）。

## 知识缺口

- 本页依据的 raw 未给出 Pi 的出品方、官方仓库地址、版本号或许可证等信息。raw 将 Pi 与 Claude Code、Codex 并列描述为编码 Agent；未提供其与名称相近的其他“Pi”实体为同一项目的证据，本页不据此作合并判断。
