---
title: "The deepseek of DeepSeek Harness: Overengineering or built for self-evolution?"
type: media
sources:
  - read-later-4445b9b74d/later_84091bcce34b4bab91a7ad57-f00ab3bff8-26d6d03086bf.md
created: 2026-08-29
updated: 2026-08-31
schema_version: 0.4.0
---

# The deepseek of DeepSeek Harness: Overengineering or built for self-evolution?

- 标题：The deepseek of DeepSeek Harness: Overengineering or built for self-evolution?
- 署名：Zhenjia
- 发布平台：Life Odyssey（zhenjia.dev），见 [[sources/life-odyssey|Life Odyssey（zhenjia.dev）]]
- 原文 URL：https://zhenjia.dev/posts/the-deepseek-of-deepseek-harness
- 写作时间：2026-08-20（37 min）；采集时间：2026-08-29
- 收录说明：对 [[frameworks/deepseek-harness|DeepSeek Harness（DSH）]] 的一次深度剖析（文中称“deepseek”）。核心问题：DSH 的“一切皆插件”设计是过度工程，还是为 Agent 自进化（Recursive Self-Improvement / RSI Agents）而建？
- 本页 sourced_from [[sources/life-odyssey|Life Odyssey（zhenjia.dev）]]。

## 主题摘要

- Agent 基本组成与 Harness 五要素（上下文管理、工具接口、约束、验证、纠正）：discusses [[concepts/harness|Harness（模型外层执行系统）]]。
- ReAct Agent Loop 及其与 Loop Engineering 的区别：见 [[media/deepseek-of-deepseek-harness|Agent Loop（ReAct 循环）]]。
- Agent 开发三种流派（Pydantic AI / Claude Agent SDK / Eve）与插件形态×组装方式：见 [[media/deepseek-of-deepseek-harness|Agent 开发三种流派]]。
- DSH“一切皆插件”与时空可组合性：底层为 [[frameworks/cordis|Cordis]] 插件框架，随附论文 [[papers/spatiotemporal-composability|A Programming Paradigm for Spatiotemporal Composability]]。
- Agent 自我改进五级、为何改进更多发生在 Harness 层而非模型层：见 [[media/deepseek-of-deepseek-harness|自我改进的五级]]，参考文献 [[media/harness-engineering-for-self-improvement|Harness Engineering for Self-Improvement]]。
- 离线/在线/近线学习与 Agent 自进化为何难以在线：见 [[media/deepseek-of-deepseek-harness|离线学习、在线学习与近线学习]]。
- 结论：对当下正在设计和开发 Agent 的团队而言，DSH 带有明显的过度工程；其过度工程是为“运行时进化 harness 并立刻生效”这件事预留的，而这件事现在还不是刚需。
