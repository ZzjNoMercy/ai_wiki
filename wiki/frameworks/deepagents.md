---
title: DeepAgents
type: software_framework
sources:
  - knowledge-file-4be80edb57/knowledge-imported-20260722-harness-profile-tool-description-overrides.md-9fe983cc93-cfbd16066b26.md
  - conversation-correction-codex-multi-agent-token-20260830-1c071d3fe3/multi-agent-token-wiki-scope-v2-c875e42ad4-adddccc75aa9.md
created: 2026-07-22
updated: 2026-08-31
schema_version: 0.4.0
---

# DeepAgents

DeepAgents 属于 LangChain 生态，但不等同于 LangChain。官方将其定义为一个独立的、开箱即用的 Agent Harness 库：它建立在 [[frameworks/langchain|LangChain]] 的 Agent 核心构件之上（depends_on），复用模型、工具和 Agent 循环等基础抽象；同时使用 [[frameworks/langgraph|LangGraph]] 作为运行时（uses），承载持久执行、流式输出、Human-in-the-loop 等能力。

DeepAgents implements [[concepts/harness|Harness（模型外层执行系统）]]。

## 职责定位

- [[frameworks/langchain|LangChain]] 提供 Agent 的核心构件、模型与工具抽象；
- DeepAgents 在这些构件之上提供带规划、文件系统、[[concepts/multi-agent|子代理]]和上下文管理的 Agent Harness；
- [[frameworks/langgraph|LangGraph]] 为 DeepAgents 提供持久执行、状态、流式处理和 Human-in-the-loop 运行时。

## Harness 定制接口

`HarnessProfile`、`register_harness_profile` 和 `tool_description_overrides` 是 DeepAgents 暴露的 Harness 定制接口，不是 [[frameworks/langchain|LangChain]] 本身的同名 API。

`HarnessProfile` 是 DeepAgents 在 `create_deep_agent()` 组装 Agent 时读取的运行适配配置，它不是 Middleware，也不是新的 LangGraph 节点。它适合承载：

- `tool_description_overrides`：按工具名替换模型看到的工具描述；
- `base_system_prompt` / `system_prompt_suffix`：调整 Prompt；
- `excluded_tools`：控制工具可见性；
- `extra_middleware`：注入运行时 Middleware；
- 通用子代理等 Harness 差异化设置。

## 内置工具与描述覆盖

DeepAgents 内置 `ls` 等工具，`ls` 的默认说明倾向于让模型在读取或编辑文件前先列目录。框架会在 Agent 组装阶段复制并替换受支持工具的 description，不修改 DeepAgents 源码，也不修改调用方持有的原工具对象。

作用于 DeepAgents 的工具描述覆盖实践见 [[practices/harness-profile-tool-description-overrides|HarnessProfile 工具描述覆盖实践]]（applies_to）。
