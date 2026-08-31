---
title: Hermes Agent
type: system
sources:
  - conversation-session-c0cc35d048c0-58aa7931a2/query-dcaa72953343-1a913f89cf-5218339fe6af.md
created: 2026-08-12
updated: 2026-08-12
schema_version: 0.4.0
---

# Hermes Agent

Hermes Agent 是 Nous Research 出品的 AI 智能体，内置完整的浏览器自动化工具集，支持多种后端模式。本页依据 Hermes Agent 浏览器自动化功能文档（https://hermes-agent.nousresearch.com/docs/user-guide/features/browser）的会话整理编译。

## 总体定位

Hermes Agent 内置完整的浏览器自动化工具集，支持多种后端模式。所有模式下，智能体都能浏览网页、点击页面元素、填写表单和提取信息。页面以**可访问性树（accessibility tree）** 的文本快照形式呈现，交互元素带 `@e1`、`@e2` 这样的引用 ID 供智能体点击和输入。

会话中用户将本次编译的核心概括为：有开源的框架把这些第三方浏览器服务封装成内置的浏览器工具集。文档证据确认各后端产品本身是独立产品、Hermes 是客户端/集成方（见下文）；raw 未明确说明 Hermes Agent 自身是否开源。

## 支持的浏览器后端（Setup）

| 模式 | 说明 |
|---|---|
| **Browserbase 云模式** | 托管云浏览器，自带反机器人能力，需要 API Key |
| **Browser Use 云模式** | 备选云浏览器提供商 |
| **Browser Use 模式（默认）** | 基于 Browser Use CLI 3.0，智能体编写并执行 Python 操作网页；无 CLI 时自动回退内置工具 |
| **Firecrawl 云模式** | 云浏览器 + 内置网页抓取 |
| **Camofox 本地模式** | Firefox 分支，本地反检测浏览（指纹伪装），支持 Docker 部署、持久会话、VNC 实时观看 |
| **本地 Chromium 系 CDP（`/browser connect`）** | 通过 CDP 连接到用户自己的 Chrome/Brave/Chromium/Edge，可实时观看操作、复用登录态 |
| **本地浏览器模式** | 用 `agent-browser` CLI 驱动本地 Chromium，无需云凭据 |

**混合路由**：配置了云提供商后，遇到 `localhost`、`192.168.x.x` 等私网地址会自动启动本地 Chromium 边车处理（防止云服务商看到私网 URL），公网地址仍走云，SSRF 防护不失效。

## 可用工具集

文档列出一整套 `browser_*` 工具：

- **browser_navigate**：导航到 URL（必须先调用）
- **browser_snapshot**：获取页面可访问性树快照（超过 15,000 字符会自动截断/LLM 摘要）
- **browser_click / browser_type / browser_scroll / browser_press / browser_back**：点击、输入、滚动、按键、后退
- **browser_vision**：截图并用视觉 AI 分析（用于验证码、复杂布局等）
- **browser_console**：读取控制台日志/JS 错误，也可直接执行 JavaScript 表达式
- **browser_cdp**：原始 Chrome DevTools Protocol 透传（处理原生对话框、iframe 内求值、cookie/网络控制等）
- **browser_dialog**：显式响应 `alert`/`confirm`/`prompt` 原生对话框（支持 `must_respond`/`auto_dismiss`/`auto_accept` 三种策略）
- **browser_get_images**：列出页面所有图片及 alt 文本

## 其他特性

- **会话录制**：可自动录制浏览器会话为 WebM 视频
- **有头模式（Headed Mode）**：显示可见浏览器窗口，回合之间保持窗口打开，便于人工干预验证码和保持登录态
- **隐身特性**：Browserbase 自带随机指纹、验证码破解、住宅代理等；免费计划会自动降级
- **会话管理**：每个任务独立会话、闲置自动清理、进程退出时紧急清理
- **导航建议**：简单信息检索优先用 `web_search`/`web_extract`（更快更省），浏览器工具用于需要交互的场景

## 已知限制

- 基于可访问性树的文本交互，不用像素坐标
- 大页面快照可能被截断
- 云会话消耗配额且有过期时间；本地 `/browser connect` 免费
- **不支持从浏览器下载文件**

## 与后端产品的关系

Hermes Agent 是文档中这些后端产品的**客户端/集成方**，反向依赖关系——是 Hermes 依赖它们，而不是它们依赖 Hermes：

- **Browserbase**：独立云浏览器 SaaS，有自己完整的 API 和 Python/JS SDK，可以直接用代码创建会话、连 CDP、控制浏览器；Hermes 只是它众多客户端之一
- **Browser Use**：独立开源框架（Browser Use CLI 3.0），本身是 Python 库/CLI，可单独驱动本地 Chrome 或 Browser Use 云浏览器，有自己的 GitHub 项目和文档
- **Firecrawl**：独立网页抓取平台，独立 SaaS，自带爬虫 API，Hermes 只是接入它的云抓取能力
- **Camofox**：独立本地浏览器服务（Firefox 分支，反检测）；文档专节「Externally managed Camofox sessions」提供 `GET /tabs?userId=<user_id>` 这类 HTTP API，可被另一个桌面助手、自定义集成、另一个 agent 直接驱动——文档中最明确的产品级独立证据
- **agent-browser CLI**：独立命令行工具，单独安装后即可驱动本地 Chromium，不经过 Hermes；Hermes 的 local browser mode 只是调用它
- **CDP（`/browser connect`）**：标准协议，不是产品；本地任何 Chrome/Brave/Edge 都自带 CDP 端口，任何工具（Playwright、Puppeteer、自写脚本）都能连，与 Hermes 无绑定关系

**不能脱离 Hermes 的部分**：真正绑定在 Hermes 内部的是工具层（`browser_navigate`、`browser_snapshot`、`browser_dialog`、`browser_cdp` 这一整套 `browser_*` 工具）以及 Hermes 的会话管理、隐身配置、SSRF 防护、录制等编排逻辑；它们是 Hermes 的封装，不是独立产品。

默认的 Browser Use 模式基于 [[frameworks/browser-use|Browser Use]] CLI 3.0（uses）：智能体编写并执行 Python 操作网页；无 CLI 时自动回退内置工具。

## 来源说明

- 本文事实来自会话对文档 https://hermes-agent.nousresearch.com/docs/user-guide/features/browser 的整理；"开源的框架"表述来自会话用户的概括，raw 仅明确 Browser Use 为开源框架，未说明 Hermes Agent 是否开源。
