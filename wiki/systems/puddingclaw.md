---
title: PuddingClaw
type: system
sources:
  - knowledge-file-4be80edb57/knowledge-imported-20260722-harness-profile-tool-description-overrides.md-9fe983cc93-cfbd16066b26.md
created: 2026-07-22
updated: 2026-08-31
schema_version: 0.4.0
---

# PuddingClaw

PuddingClaw 通过 [[frameworks/deepagents|DeepAgents]] 暴露的 Harness 定制接口（`HarnessProfile`、`register_harness_profile`、`tool_description_overrides`）覆盖工具描述（uses）。这些接口不是 LangChain 本身的同名 API。

2026-07-22，PuddingClaw 在 `modelclientchatmodel` HarnessProfile 中覆盖 `ls` 和 `glob` 的工具描述，同时补充 `TOOL_GUIDES.md`，专项测试通过。该行为实现了 [[practices/harness-profile-tool-description-overrides|HarnessProfile 工具描述覆盖实践]]（implements）。

PuddingClaw relates_to [[concepts/harness|Harness]]，其当前记录的关联点是通过 DeepAgents 配置工具暴露语义。
