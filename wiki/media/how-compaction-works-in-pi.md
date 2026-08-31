---
title: How Compaction Works in Pi
type: media
sources:
  - conversation-synthesis-session-bc0c821a04d9-9bf943cf4e/query-cd31e5bdfb00-72cbb0465f-baa1ba11a8b2.md
created: 2026-08-14
updated: 2026-08-14
schema_version: 0.4.0
---

# How Compaction Works in Pi

- 标题：How Compaction Works in Pi
- 署名/发布方：Earendil Engineering 官方博客
- 平台：Earendil Engineering 官方博客（earendil.com）
- 原文 URL：https://earendil.com/posts/compaction-in-pi/
- 收录说明：本页依据 conversation-synthesis 会话快照编译，该快照声明本文基于 Earendil Engineering 官方博客上述文章整理，是一篇入门向的 Compaction 实现介绍。
- sourced_from：[[sources/earendil-engineering|Earendil Engineering 官方博客]]

## 文章讨论的内容

- 讨论 [[systems/pi-coding-agent|Pi（编码 Agent）]]（discusses）：Pi 在上下文接近窗口上限或用户执行 `/compact` 时压缩对话历史；保留最近约 20K tokens（约 5–20 轮）消息，截断点前内容交给摘要模型；压缩用独立系统提示词（“上下文摘要助手”）与独立请求生成 goal / progress / key decisions 结构化摘要，并以纯文本 compaction entry 存储；turn 结束后触发以保留 prompt cache，mid-turn 溢出时兜底压缩。
- 讨论 [[concepts/context-compaction|Compaction（上下文压缩）]]（discusses）：上下文窗口有限、历史超限导致 `Request exceeds the maximum size` 错误；compaction 在理论上可用确定性函数、实践中用 LLM 摘要实现；压缩会破坏依赖精确前缀匹配的 prompt cache，压缩后的首次请求重新建立缓存。
