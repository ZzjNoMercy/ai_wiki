---
title: Browser Use
type: software_framework
sources:
  - conversation-session-c0cc35d048c0-58aa7931a2/query-dcaa72953343-1a913f89cf-5218339fe6af.md
created: 2026-08-12
updated: 2026-08-12
schema_version: 0.4.0
---

# Browser Use

Browser Use 是独立的**开源浏览器自动化框架**（Browser Use CLI 3.0）。它本身是一个 Python 库/CLI，可以单独用来驱动本地 Chrome 或 Browser Use 云浏览器，拥有自己的 GitHub 项目和文档，不依赖任何特定 Agent 环境。

## 独立性

Browser Use 是独立产品，[[systems/hermes-agent|Hermes Agent]] 只是它的客户端/集成方之一（uses 反向：Hermes Agent 使用 Browser Use）——是 Hermes 依赖 Browser Use，而不是 Browser Use 依赖 Hermes。

## 在 Hermes Agent 中的集成方式

[[systems/hermes-agent|Hermes Agent]] 的浏览器自动化将其作为**默认后端模式**（uses）：

- **Browser Use 模式（默认）**：基于 Browser Use CLI 3.0，智能体编写并执行 Python 操作网页；无 CLI 时 Hermes 自动回退内置工具
- **Browser Use 云模式**：作为 Hermes 的备选云浏览器提供商

注意区分：Hermes Agent 内部的 `browser_*` 工具（如 `browser_navigate`、`browser_snapshot`、`browser_cdp`）是 Hermes 自身的封装，不是 Browser Use 的产品；Browser Use 只作为其后端之一被集成。

## 来源说明

- 本文事实来自会话对 Hermes Agent 浏览器自动化文档（https://hermes-agent.nousresearch.com/docs/user-guide/features/browser）的整理。
