---
title: Pi Agent 学习资源评估
type: analysis
sources:
  - conversation-correction-session-6afa50dc1ea8-10d3fb8d76/query-18f0f64be0c2-pi-only-7374e46112-8cbd01cdcc8e.md
created: 2026-08-13
updated: 2026-08-13
schema_version: 0.4.0
---

# Pi Agent 学习资源评估

评估四个 Pi Agent 学习网站，回答“从 agent 小白到有基础，分别适用哪个”。四个站的主题是同一个 pi 项目，但层级不同。

## 评估对象（evaluates）

- [[media/pi-agent-runbook-tutorial|菜鸟教程 · Pi Agent 教程]] —— 使用手册，完全不碰源码
- [[media/pi-from-scratch|PI from Scratch]] —— 600 行 nanopi 手写教学版
- [[media/pi-agent-book|π-agent book（books.antinomie.org）]] —— pi-agent-core 逐行源码导读（进行中，每周一章）
- [[media/dg-zhuya-pi-reading-notes|冬瓜 · 源码精读笔记]] —— 生产级全景精读（10 章，约 5 万字）

## 难度与适用人群对比

| 站点 | 内容类型 | 门槛 | 适合人群 |
|---|---|---|---|
| 菜鸟教程 | 使用手册 | ★ 最低 | 完全没接触过 Pi 的小白 |
| PI from Scratch | 600 行教学版手写 | ★★ 低 | 会用一点、想懂原理的初学者 |
| π-agent book | pi-agent-core 源码导读 | ★★★ 中 | 有基础、想逐行啃一个库源码的人（适合追更） |
| 冬瓜精读笔记 | 生产级全景精读 | ★★★★ 中高 | 有基础、想系统掌握生产级设计全貌的开发者 |

## 从小白到有基础的路线

1. 纯小白 → 菜鸟教程：先装上、跑起来、知道交互/Print/JSON/RPC 四种模式、会用内置工具和 Skills；不要一上来碰源码。
2. 会用之后想懂原理 → PI from Scratch：跟着手写一遍 nanopi，Agent Loop、工具、事件流、层与层之间为什么这样拆，600 行里讲明白。
3. 想深入具体库源码 → π-agent book：只写 pi-agent-core、目前仅 2 章且每周更新，适合长期跟读。
4. 想掌握生产级全貌 → 冬瓜精读笔记：覆盖面最完整，且有 Python 版，可用作教学素材。

## 两点提醒

- π-agent book 和冬瓜笔记都聚焦源码，阅读前最好先把 Pi 跑起来用过一轮。
- 四个站层级不同：菜鸟教使用、PI from Scratch 讲最小实现、π-agent book 精读 pi-agent-core 一个库、冬瓜精读整个三层架构；四者不冲突，可以按上面顺序串起来读。

## 主题对象

- 四个资源围绕（discusses）：[[frameworks/pi-agent|Pi Agent]]
