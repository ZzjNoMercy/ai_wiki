## 用户

https://www.dgzhuya.com/ https://books.antinomie.org/pi/ https://pi-from-scratch.vercel.app/#chapter1 https://www.runoob.com/pi-agent/pi-agent-concepts.html 这几个都是pi agent的学习网站，你评估下从agent小白到有基础的，分别适用哪个

## Agent

我来逐个抓取这四个网站的内容，评估它们的内容深度和适用人群。

四个网站抓到了三个，`books.antinomie.org/pi/` 返回空内容，我再试一次确认。

两次都返回空内容，可能是纯 JS 渲染的站点。我换个方式确认它的内容。

四个站都确认完了。`books.antinomie.org` 直接抓取是空的（纯 JS 渲染），通过搜索确认了它的真实内容：一本还在写作中的源码导读。以下是完整评估。

## 四个站各是什么

### 1. 菜鸟教程 · Pi Agent 教程 — 纯"使用手册" [^src_603213ad785bc06c]

完整教程系列：简介、核心概念、安装、认证与模型配置（含 DeepSeek 接入）、第一次对话、交互/非交互模式、上下文与会话管理、Skills、扩展开发、事件系统、包管理、多平台部署，外加 CLI/配置/Provider 参考手册。**完全不碰源码**，只教"怎么用"：怎么装、怎么配、四种运行模式、内置工具、怎么扩展。零门槛。

### 2. PI from Scratch — 从零手写 600 行教学版 [^src_7b49bff74f236ad7]

基于生产级 pi（上万行）提炼的 **nanopi（600 行）**，用一篇文章跟着数据流把五个 TS 文件（`llm.ts` / `agent.ts` / `tools.ts` / `tui.ts` / `cli.ts`）从头写出来。交互式阅读：右侧编辑器随文章滚动浮现代码，带"语法扫盲块"打消 TS 顾虑。作者明说是给初学者写的。核心价值是把 Agent Loop、工具与事件流、模块单向依赖这些原理用最少的代码讲透。

### 3. π-agent book（books.antinomie.org）— pi-agent-core 逐行源码导读 [^src_601872ed4f99cda2] [^src_c8fb71b1516bcd5b]

主角是 **pi-agent-core 这个库本身**（不是整个 pi）。目前只有 2 章：第 1 章"它是什么"，第 2 章跟着一次 `prompt()` 把控制流从 `prompt()` 走到 `agent_end` 走完全程。交互式设计很讲究：每个论断都链回仓库里它出生的那一行，代码块可"钉"在屏幕角落对照。注意它是**进行中的书**，每周只更一章，目前是预览版。

### 4. 冬瓜 · 源码精读笔记 — 生产级 Pi Agent 全景精读 [^src_efe843bf731893fd]

10 章完整精读，约 5 万字，覆盖整个 pi 的三层架构（pi-ai / agent-core / coding-agent）：Agent Loop、30+ 供应商模型调用抽象、工具系统五步管道、7 种消息系统、事件驱动、上下文工程与压缩、Session Tree 会话管理。**每章都有 TypeScript 和 Python 两个版本**（Python 为转写）。自我定位就是"想搞懂一个生产级 Agent SDK 是怎么造出来的"。

## 难度与适用人群对比

| 站点 | 内容类型 | 门槛 | 适合人群 |
|---|---|---|---|
| 菜鸟教程 | 使用手册 | ★ 最低 | 完全没接触过 Pi 的小白 |
| PI from Scratch | 600 行教学版手写 | ★★ 低 | 会用一点、想懂原理的初学者 |
| π-agent book | pi-agent-core 源码导读 | ★★★ 中 | 有基础、想逐行啃一个库源码的人（适合追更） |
| 冬瓜精读笔记 | 生产级全景精读 | ★★★★ 中高 | 有基础、想系统掌握生产级设计全貌的开发者 |

## 从小白到有基础的路线

1. **纯小白** → 从**菜鸟教程**开始 [^src_603213ad785bc06c]。先装上、跑起来、知道交互/Print/JSON/RPC 四种模式、会用内置工具和 Skills，建立"agent 是什么"的直觉。不要一上来碰源码。
2. **会用之后想懂原理** → **PI from Scratch** [^src_7b49bff74f236ad7]。跟着手写一遍 nanopi，Agent Loop、工具、事件流、层与层之间为什么这样拆，600 行里全讲明白了。这是原理理解的最短路径。
3. **想深入具体库源码** → **π-agent book** [^src_601872ed4f99cda2]。但它只写 pi-agent-core、目前仅 2 章且每周更新，适合作为长期跟读，不适合一次性系统学习。
4. **想掌握生产级全貌** → **冬瓜精读笔记** [^src_efe843bf731893fd]。覆盖面最完整，且有 Python 版，对你教 Python/LangChain 的背景来说阅读成本最低——可以直接用它的 Python 版做教学素材。

两点提醒：π-agent book 和冬瓜笔记都聚焦源码，阅读前最好先把 Pi 跑起来用过一轮；另外这四个站的主题是同一个 pi 项目，但层级不同——菜鸟教使用，PI from Scratch 讲最小实现，π-agent book 精读 pi-agent-core 一个库，冬瓜精读整个三层架构，四者不冲突，可以按上面顺序串起来读。

## 用户

把这个整理到wiki，pi适合作为framework
