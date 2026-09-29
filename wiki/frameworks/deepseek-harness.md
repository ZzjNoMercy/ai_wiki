---
title: DeepSeek Harness（DSH）
type: software_framework
sources:
  - read-later-4445b9b74d/later_84091bcce34b4bab91a7ad57-f00ab3bff8-26d6d03086bf.md
created: 2026-08-29
updated: 2026-09-01
schema_version: 0.4.0
---

# DeepSeek Harness（DSH）

DeepSeek 于 2026 年 8 月发布的 Agent Harness 框架（文中基于 0.1.0 版本分析）。其设计口号是“一切皆插件”（Everything is Plugin）：整个 DeepSeek Harness 没有任何一个地方是核心，所有的地方都是插件。

DeepSeek Harness implements [[concepts/harness|Harness（模型外层执行系统）]]，并把 Agent Loop、持久化、压缩等运行时组件也开放为插件。

## 组成与清单

- 一个完整项目是一个 npm 包，包含：`shop-support.cordis.yml`（完整组合清单，`*.cordis.yml` 是官方约定名，底层框架叫 [[frameworks/cordis|Cordis]]）、`AGENTS.md`（工作指令）、`package.json`（依赖官方包与自研插件包）、`service.ts`（TS SDK 驱动）。
- yml 清单里的每一项都是一个插件：`id` 是接口，`name` 是当下实现该能力的包；整份 yml 是一份可更新的能力与实现“花名册”。
- 文中出现的官方包包括 `@deepseek-ai/dsh-sdk-jsonrpc-server`、`@deepseek-ai/dsh-llm-deepseek`、`@deepseek-ai/dsh-agent-spine-demo`、`@deepseek-ai/dsh-session-persistence-jsonl`、`@deepseek-ai/dsh-fs-local`、`@deepseek-ai/dsh-agent-loop`、`@deepseek-ai/dsh-tools` 与 `@deepseek-ai/dsh-sdk-client`。

## 插件接口

- `ctx.tools.register(defineTool(...))`：注册工具。
- `tools/pre-execute`：执行前检查，可拒绝执行。
- `tools/post-execute`：执行后验证，可把结果变成带纠正反馈的失败，使模型看到失败原因。
- `agent/pre-step`：修改这一轮将要发出的新增消息，可用于脱敏或压缩。

## 与其他流派的关系

- 与 Eve 相似，采用命令式插件与声明式组装，但开放范围更深：Eve 只声明 Agent 的内容物，DSH 的清单则把循环、持久化和压缩也作为插件。文章将其概括为“DSH 是 Eve 的形态，库的开放度”。详见 [[media/deepseek-of-deepseek-harness|The deepseek of DeepSeek Harness]]。

## 设计动机：时空可组合性

- 为“时空可组合性”而设计：时间可组合性允许软件运行期间替换组件；空间可组合性通过 `id`/`name` 抽象，让依赖方在实现替换后重新定位能力。见 [[papers/spatiotemporal-composability|A Programming Paradigm for Spatiotemporal Composability]]。
- applies_to → [[concepts/agent-self-improvement|Agent Self-Improvement]]：开放深层组件使 Agent 能把自我改进对象从提示词、Skill 与工作流推进到 Harness 代码。
- relates_to → [[concepts/recursive-self-improvement|递归自我改进（RSI）]]：设计远期指向 Agent 在运行时修改自身 Harness 并让修改实时生效。

## 是否过度工程

- 文中结论：对当前 Agent 团队而言，DSH 带有明显的过度工程。运行时修改深层 Harness 并立即生效仍主要是研究性或少数特定场景需求；评价改动是否真正改善任务通常需要昂贵评测或人工判断。

## 参考

- 相关概念背景见 [[media/harness-engineering-for-self-improvement|Harness Engineering for Self-Improvement]]。
