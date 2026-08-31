---
title: LangChain
type: software_framework
sources:
  - knowledge-file-4be80edb57/knowledge-imported-20260722-harness-profile-tool-description-overrides.md-9fe983cc93-cfbd16066b26.md
created: 2026-07-22
updated: 2026-07-31
schema_version: 0.4.0
---

# LangChain

LangChain 为 Agent 提供核心构件、模型与工具抽象。[[frameworks/deepagents|DeepAgents]] 建立在 LangChain 的 Agent 核心构件之上（depends_on），复用模型、工具和 Agent 循环等基础抽象。

DeepAgents 属于 LangChain 生态，但不等同于 LangChain：DeepAgents 是独立的、开箱即用的 Agent Harness 库。`HarnessProfile`、`register_harness_profile` 和 `tool_description_overrides` 是 DeepAgents 暴露的 Harness 定制接口，不是 LangChain 本身的同名 API。
