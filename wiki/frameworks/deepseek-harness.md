---
title: DeepSeek Harness（DSH）
type: software_framework
sources:
  - read-later-4445b9b74d/later_84091bcce34b4bab91a7ad57-f00ab3bff8-26d6d03086bf.md
created: 2026-08-29
updated: 2026-08-31
schema_version: 0.4.0
---

# DeepSeek Harness（DSH）

DeepSeek 于 2026 年 8 月发布的 Agent Harness 框架（文中基于 0.1.0 版本分析）。其设计口号是“一切皆插件”（Everything is Plugin）：整个 DeepSeek Harness 没有任何一个地方是核心，所有的地方都是插件。

DeepSeek Harness implements [[concepts/harness|Harness（模型外层执行系统）]]，并把 Agent Loop、持久化、压缩等运行时组件也开放为插件。

## 组成与清单

- 一个完整项目是一个 npm 包，包含：`agent/shop-support.cordis.yml`（完整组合清单，`*.cordis.yml` 是官方约定名，底层框架叫 [[frameworks/cordis|Cordis]]，路径传给驱动）、`AGENTS.md`（客服工作指令）、`package.json`（依赖官方包 + 自研插件包）、`service.ts`（TS SDK 驱动）。
- yml 清单里的每一项都是一个插件：`id` 是接口（interface），`name` 是当下实现该能力的包（implementation）；整份 yml 是一份随时更新的“花名册”，写的是这个软件此刻需要哪些能力、各自由哪个包负责。
- 文中出现的官方包：`@deepseek-ai/dsh-sdk-jsonrpc-server`（对外 JSON-RPC 服务）、`@deepseek-ai/dsh-llm-deepseek`、`@deepseek-ai/dsh-agent-spine-demo`（一行装进 agent loop、会话日志、提示词组装、工具执行管线）、`@deepseek-ai/dsh-session-persistence-jsonl`、`@deepseek-ai/dsh-fs-local`、`@deepseek-ai/dsh-agent-loop`（连最核心的 agent loop 都是独立 npm 包，改 `name` 即可换成自己写的 loop）、`@deepseek-ai/dsh-tools`、`@deepseek-ai/dsh-sdk-client`（官方 TS SDK 驱动）。

## 插件接口（hook）

- `ctx.tools.register(defineTool(...))`：注册工具。
- `tools/pre-execute`：执行前的检查，可返回 `{ kind: 'deny', reason: ... }` 拒绝。
- `tools/post-execute`：执行后的验证，可返回 `{ kind: 'block', feedback: [...] }`，把这次调用的结果直接变成带纠正反馈的失败——模型自己会看到失败原因。
- `agent/pre-step`：对这一轮即将发出去的消息进行修改（脱敏、本地小模型压缩等）；为维持缓存稳定，只允许修改这一轮新增的消息。

## 与其他流派的关系

- 与 [[media/deepseek-of-deepseek-harness|Eve]] 相似（命令式插件 + 声明式组装），但更开放：Eve 的文件树里只能声明 agent 的内容物（工具、指令、审批），循环、持久化、压缩是平台的；DSH 的清单里这些全是插件。“DSH 是 Eve 的形态，库的开放度。”详见 [[media/deepseek-of-deepseek-harness|Agent 开发三种流派]]。

## 设计动机：时空可组合性

- 为“时空可组合性”（spatiotemporal composability）而设计：时间可组合性——软件还在跑、请求还在进的时候能换掉某个组件（进程不重启）；空间可组合性——组件被换掉后软件需要知道新组件是什么、在哪里，因此需要 `id`/`name` 抽象，Agent 不再写死对官方包的依赖。见论文 [[papers/spatiotemporal-composability|A Programming Paradigm for Spatiotemporal Composability]]。
- 指向 Agent Self-Evolution（自进化，也称 Recursive Self-Improving / RSI Agents）：让 Agent 在运行时最大限度修改自身 harness 代码并实时生效。相关：[[concepts/recursive-self-improvement|递归自我改进（RSI）]]、[[media/deepseek-of-deepseek-harness|自我改进的五级]]。

## 是否过度工程

- 文中结论：对现在正在设计和开发 Agent 的团队而言，DSH 带有明显的过度工程。其过度工程是为“运行时进化 harness、并且立刻生效”这件事预留的，而这件事现在还不是刚需（见 [[media/deepseek-of-deepseek-harness|离线学习、在线学习与近线学习]]）。

## 参考

- 相关概念背景见 [[media/harness-engineering-for-self-improvement|Harness Engineering for Self-Improvement]]（Lilian Weng，2026-07-04）。
