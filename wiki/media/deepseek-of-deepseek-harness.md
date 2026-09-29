---
title: "The deepseek of DeepSeek Harness: Overengineering or built for self-evolution?"
type: media
sources:
  - read-later-4445b9b74d/later_84091bcce34b4bab91a7ad57-f00ab3bff8-26d6d03086bf.md
created: 2026-08-29
updated: 2026-09-10
schema_version: 0.4.0
---

# The deepseek of DeepSeek Harness: Overengineering or built for self-evolution?

- 标题：The deepseek of DeepSeek Harness: Overengineering or built for self-evolution?
- 署名：Zhenjia
- 发布平台：Life Odyssey（zhenjia.dev），见 [[sources/life-odyssey|Life Odyssey（zhenjia.dev）]]。
- 原文 URL：https://zhenjia.dev/posts/the-deepseek-of-deepseek-harness
- 写作时间：2026-08-20（37 min）；采集时间：2026-08-29。
- 收录说明：对 [[frameworks/deepseek-harness|DeepSeek Harness（DSH）]] 的一次深度剖析。核心问题：DSH 的“一切皆插件”设计是过度工程，还是为 Agent 自进化而建？
- 本页 sourced_from [[sources/life-odyssey|Life Odyssey（zhenjia.dev）]]。

## 主题摘要

- Agent 基本组成与 `Agent = Model + Harness` 架构视角：discusses [[concepts/agent|Agent（智能体）]]。Harness 五要素（上下文管理、工具接口、约束、验证、纠正）：discusses [[concepts/harness|Harness（模型外层执行系统）]]。
- ReAct Agent Loop 及其与 Loop Engineering 的区别。
- Agent 开发三种流派（Pydantic AI / Claude Agent SDK / Eve）与插件形态 × 组装方式。
- DSH“一切皆插件”与时空可组合性：底层为 [[frameworks/cordis|Cordis]] 插件框架，随附论文 [[papers/spatiotemporal-composability|A Programming Paradigm for Spatiotemporal Composability]]。
- discusses → [[concepts/agent-self-improvement|Agent Self-Improvement]]：文章把自我改进对象分为提示词、结构化上下文、工作流、Harness 代码与优化器代码五级，并说明为何近期改进更可能发生在 Harness 层而非模型权重层。
- relates_to → [[concepts/recursive-self-improvement|递归自我改进（RSI）]]：文章以 Agent Self-Evolution / Recursive Self-Improving 描述 DSH 的远期设计指向。
- 离线、在线、近线学习与 Agent 自进化为何难以在线：Agent 修改 Harness 后的效果信号昂贵、低频且常需要人工判断。
- 结论：对当下正在设计和开发 Agent 的团队而言，DSH 带有明显的过度工程；其过度工程是为“运行时进化 Harness 并立刻生效”预留的，而这件事现在还不是刚需。
