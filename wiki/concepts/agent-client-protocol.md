---
title: Agent Client Protocol (ACP)
type: concept
sources:
  - conversation-synthesis-session-0943218c7a57-e3f54c6100/query-188aaa0b1421-5c71d45755-602786d6ca0c.md
created: 2026-08-19
updated: 2026-08-19
schema_version: 0.4.0
---

# Agent Client Protocol（ACP）

## 概念概述

Agent Client Protocol（ACP）是一个标准化的通信协议，用于 **AI 智能体（agent）与客户端应用（client，如代码编辑器/IDE）之间的通信**。它由 Zed Industries、JetBrains 等推动，定位类似于 LSP（Language Server Protocol）标准化语言服务器集成那样，为“编辑器 ↔ 编码智能体”这一组合提供统一的交互标准，同时适用于本地和远程场景。

### 为什么需要 ACP

AI 编码智能体与编辑器之间原本高度耦合，但互操作并非默认能力：

- **集成成本高**：每新增一个“智能体 × 编辑器”组合都需要定制集成工作。
- **兼容性受限**：智能体只能用于一小部分编辑器。
- **开发者锁定**：选择某个智能体往往意味着接受它提供的固定接口。

ACP 通过提供标准化协议解决这些问题：实现 ACP 的智能体可以在任何兼容的编辑器中使用；支持 ACP 的编辑器可以接入整个 ACP 智能体生态。这种解耦让双方可以独立创新，开发者也能自由选择最适合自己工作流的工具。

### 协议要点

- 基于 **JSON-RPC** 构建，并尽量 **复用 [[concepts/model-context-protocol|MCP]] 的既有类型**，避免为常见数据类型重复造轮子。
- 包含用于智能体编码 UX 的自定义类型（例如展示 diff）。
- 用户可读文本的 **默认格式为 Markdown**，既能表达丰富格式，又不需要编辑器具备 HTML 渲染能力。
- 本地智能体作为代码编辑器的子进程运行，通过 stdio 上的 JSON-RPC 通信；远程智能体可托管在云端或独立基础设施上，通过 HTTP 或 WebSocket 通信（远程完整支持仍在推进中）。

## 架构设计哲学

官方架构文档总结三条核心设计原则：

1. **MCP 友好（MCP-friendly）**：协议建立在 JSON-RPC 之上，并尽可能复用 MCP 的类型，集成方无需为常见数据类型再造一套表示。
2. **UX 优先（UX-first）**：专为“与 AI 智能体交互的用户体验问题”设计，有足够灵活性来清晰呈现智能体的意图，但抽象程度恰到好处、不过度。
3. **可信模型（Trusted）**：ACP 适用于“在代码编辑器里与一个你信任的模型对话”的场景。编辑器仍然控制智能体的工具调用，同时把本地文件与 MCP 服务器的访问权限交给智能体。

### 连接方式（Setup）

- 用户尝试连接智能体时，编辑器按需启动智能体子进程，所有通信通过 stdin/stdout 进行。
- 每个连接可支持多个并发会话，可以同时进行多条“思路线”（trains of thought）。
- 大量使用 JSON-RPC 通知（notifications），让智能体实时向 UI 流式推送更新。
- 同时利用 JSON-RPC 的双向请求，让智能体可以反向向编辑器发起请求，例如请求某个工具调用的权限。

### MCP 的集成方式

- 编辑器通常带有用户配置好的 MCP 服务器。转发用户 prompt 给智能体时，编辑器会把 MCP 配置一并传给智能体，让智能体直接连接到对应的 MCP 服务器。
- 如果编辑器自己想把某些能力导出为 MCP 工具：不在同一 socket 上同时跑 MCP 和 ACP，而是由编辑器提供一个自己的 MCP server 作为配置。
- 由于部分智能体只支持 stdio 方式的 MCP，编辑器可以提供一个小的代理（proxy），把请求隧道转发回自己。

## 官方教程：Get Started

官网入门教程分为 Introduction、Architecture（见上文）、Agents、Clients、ACP Registry 五个部分，另有官方语言库（Libraries）章节。

### 1. Introduction（简介）

- ACP 标准化了代码编辑器/IDE 与编码智能体之间的通信，适用于本地和远程场景。
- 本地智能体作为编辑器的子进程运行（stdio + JSON-RPC）；远程智能体托管在云端或独立基础设施（HTTP/WebSocket），完整远程支持仍在开发中，官方正与智能体平台协作。
- 复用 MCP 的 JSON 表示，并补充用于智能体编码 UX 的自定义类型（如 diff 展示）；用户可读文本默认使用 Markdown。

### 2. Agents（支持 ACP 的智能体，官方列表）

以下智能体可与 ACP 客户端配合使用：AgentPool、Augment Code、AutoDev、Blackbox AI、Bub（经 bub-acp-server）、Claude Agent（经 Zed 的 SDK adapter）、Cline、Codex CLI（经 Zed 的 adapter）、Code Assistant、Construct、crow-cli、Cursor、Docker 的 cagent、fast-agent、Factory Droid、fount、Gemini CLI、GitHub Copilot（公开预览中）、Goose、[[systems/hermes-agent|Hermes Agent]]、Junie by JetBrains、Kaagum、Kimi CLI、Kiro CLI、localharness、Minion Code、Mistral Vibe、OpenClaw、[[systems/opencode|OpenCode]]、OpenHands、[[systems/pi-coding-agent|Pi]]（经 pi-acp adapter）、Poolside、Qoder CLI、Qwen Code、Raxol（`raxol acp`）、siGit Code、Stakpak、stdio Bus、VT Code。

### 3. Clients（客户端、框架、连接器与周边工具，官方列表）

以下项目直接实现 ACP、把 ACP 智能体接入其他环境，或支持相邻的编码智能体工作流：

- **编辑器与 IDE**：Anycode（Web IDE）、Chrome ACP（浏览器扩展/PWA）、Emacs（agent-shell.el）、JetBrains（AI Assistant ACP 支持）、neovim（CodeCompanion / agentic.nvim / avante.nvim / hermes.nvim 插件）、Obsidian（Agent Client / Agent Console / Obsidian Harness 插件）、Pulsar（pulsar-acp-agent）、Qt Creator（ACP Client Plugin）、Unity（UnityACPClient / Unity Agent Client）、Visual Studio（Poolside Assistant）、Visual Studio Code（ACP Client、ACP Patchbay、ACP Pro、Multicoder、Poolside Assistant 等扩展）、Zed（官方外部智能体支持）。
- **CLI 与 TUI**：acpx、Hash（shell）、Hydra、Nori CLI、pool、Toad。
- **桌面与 Web**：ACP UI、Agent Studio、AgentRQ、AionUi、aizen、Braide、Casper、Codeg、CompozyOS、DeepChat、Devin Desktop、fabriqa.ai、gemini-cli-desktop、Gold Band、Harnss、Jockey、Kepler（GitKraken）、Lody、Minion Mind、Mitto、Ngent、Poolside Desktop Assistant、qwen-audio-agent、RayClaw、RLM Code、Shellular、Sidequery（即将推出）、Web Browser with AI SDK、Kangaroo（数据库 IDE）、ACP Components（前端组件库）、ACP Inspector、Newio。
- **Notebook 与数据工具**：agent-client-kernel（Jupyter）、DuckDB（duckdb-acp 扩展）、marimo notebook。
- **移动端**：Agmente（iOS）、Ferngeist（Android）、Happy（iOS/Android/Web）、Mobvibe、Shellular、VACP（Android，语音控制）。
- **消息平台**：ACP Discord、duckdb-claude-slack（Slack）、Juan（Slack）、OpenACP（Telegram/Discord/Slack）、Telegram ACP Bot、Telegram-ACP、ACP Router（Telegram）、WeChat ACP（微信）、qq-ai-bot（QQ/OneBot 11）、Sniptail（Discord/Slack）、Lark ACP（飞书）、Zooid（Matrix）、Pomerium AgentOps（Slack）。
- **框架**：AgentPool、fast-agent（fast-agent-acp）、ACP Kit（适配 Pydantic AI / [[frameworks/langchain|LangChain]]）、Koog（agents-features-acp）、[[frameworks/langchain|LangChain]]/[[frameworks/langgraph|LangGraph]]（Deep Agents ACP）、LlamaIndex（workflows-acp 适配器）、LLMling-Agent、Mastra（@mastra/acp）。
- **连接器**：acp_rpc_bridge（stdio 桥接 HTTP）、ACP to AG-UI（桥接 AG-UI/SSE）、AgentRQ acp-gateway（MCP 桥接）、Aptove Bridge（WebSocket 桥接移动端）、ACP Remote（远程 WebSocket 传输）、OpenClaw acp（OpenClaw Gateway 桥接）、stdio Bus（传输层路由）、acpdbg（LLDB 崩溃调试桥接）。

### 4. ACP Registry（注册表）

- **定位**：开发者分发 ACP 兼容智能体的最简方式，是经过人工审核的智能体集合，仅收录支持认证的智能体。仓库见 GitHub `agentclientprotocol/registry`。
- **收录的智能体（节选）**：Agoragentic、Amp、Auggie CLI、Autohand Code、Claude Agent、Cline、Codebuddy Code、Codex、Cortex Code、Corust Agent、crow-cli、Cursor、[[frameworks/deepagents|DeepAgents]]（LangChain）、Devin、DimCode、Dirac、Factory Droid、fast-agent、Gemini CLI、GitHub Copilot、GLM Agent（智谱）、goose、Grok Build、Harn、Junie（JetBrains）、Kilo、Kimi CLI（月之暗面）、Minion Code、Mistral Vibe、Nova、[[systems/opencode|OpenCode]]、pi ACP、Poolside、Qoder CLI、Qwen Code（阿里）、siGit Code、Stakpak、VT Code。
- **使用注册表**：客户端可以编程方式拉取：`curl https://cdn.agentclientprotocol.com/registry/v1/latest/registry.json`，JSON 中包含所有智能体元数据及自动安装所需的分发信息。
- **提交智能体**：1）fork registry 仓库；2）以智能体 ID 创建文件夹（小写、允许连字符）；3）按 `agent.schema.json` 模式添加 `agent.json`；4）可选添加 `icon.svg`（建议 16x16）；5）提交 PR。

### 5. 官方语言库

官方提供 Kotlin、Java、Python、Rust、TypeScript 等语言的协议库，另有社区库列表，详见官网 Libraries 章节。

## 参考资料

- 官网首页：https://agentclientprotocol.com/
- 入门教程：/get-started/introduction、/get-started/architecture、/get-started/agents、/get-started/clients、/get-started/registry
- 协议规范：/protocol/v1（最新）、/protocol/v2（草案）
- GitHub：https://github.com/agentclientprotocol/agent-client-protocol
- 推动方：[[companies/zed-industries|Zed Industries]]（https://zed.dev）、[[companies/jetbrains|JetBrains]]（https://jetbrains.com）

## 关系

- **[[companies/zed-industries|Zed Industries]]、[[companies/jetbrains|JetBrains]]（supports）**：推动 ACP 标准化。
- **[[systems/opencode|OpenCode]]、[[systems/pi-coding-agent|Pi]]、[[systems/hermes-agent|Hermes Agent]]（supports）**：在官方 Agents 列表中被列为可与 ACP 客户端配合使用的智能体。
- **[[concepts/model-context-protocol|MCP]]（relates_to）**：ACP 复用 MCP 类型，并定义了围绕 MCP 的集成方式。
- **[[frameworks/deepagents|DeepAgents]]（relates_to）**：在 ACP Registry 中被收录（LangChain）。
- **[[frameworks/langchain|LangChain]]、[[frameworks/langgraph|LangGraph]]（relates_to）**：官方 Clients 框架类中列为提供 Deep Agents ACP。
- **[[media/agent-client-protocol-official-tutorial|Agent Client Protocol 官方教程]]（derived_from）**：本页内容编译自该官方教程。
