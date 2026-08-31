---
title: Model Context Protocol (MCP)
type: concept
sources:
  - conversation-synthesis-session-0943218c7a57-e3f54c6100/query-188aaa0b1421-5c71d45755-602786d6ca0c.md
created: 2026-08-19
updated: 2026-08-19
schema_version: 0.4.0
---

# Model Context Protocol（MCP）

本页只记录本次 raw 直接支持的事实——MCP 在 [[concepts/agent-client-protocol|ACP]] 生态中的角色与集成方式；不补写外部定义。

## MCP 在 ACP 生态中的角色

- [[concepts/agent-client-protocol|ACP]] 基于 JSON-RPC 构建，并尽量复用 MCP 的既有类型（MCP 友好原则），集成方无需为常见数据类型再造一套表示。
- ACP 复用 MCP 的 JSON 表示，并补充用于智能体编码 UX 的自定义类型（如 diff 展示）。

## 编辑器的 MCP 集成方式（ACP 语境）

- 编辑器通常带有用户配置好的 MCP 服务器。转发用户 prompt 给智能体时，编辑器会把 MCP 配置一并传给智能体，让智能体直接连接到对应的 MCP 服务器。
- 如果编辑器自己想把某些能力导出为 MCP 工具：不在同一 socket 上同时跑 MCP 和 ACP，而是由编辑器提供一个自己的 MCP server 作为配置。
- 由于部分智能体只支持 stdio 方式的 MCP，编辑器可以提供一个小的代理（proxy），把请求隧道转发回自己。

## 与可信模型原则的关系

ACP 的“可信模型（Trusted）”设计原则描述：编辑器仍然控制智能体的工具调用，同时把本地文件与 MCP 服务器的访问权限交给智能体。

## 关系

- **relates_to**：[[concepts/agent-client-protocol|ACP]]（ACP 复用 MCP 类型并定义了围绕 MCP 的集成方式）。
