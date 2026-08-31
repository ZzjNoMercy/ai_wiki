---
title: "定期全局对抗式审查"
type: engineering_practice
sources:
  - "read-later-4445b9b74d/later_809e9deef2b14e7d97a162b4-b9f587c39f-042741626d7c.md"
  - conversation-correction-codex-multi-agent-token-20260830-1c071d3fe3/multi-agent-token-wiki-scope-v2-c875e42ad4-adddccc75aa9.md
created: "2026-08-04"
updated: 2026-08-30
schema_version: "0.4.0"
---

# 定期全局对抗式审查

## 问题

Vibe Coding 产出的代码漏洞多；问题如果不主动去找，就会一直潜伏，直到某天突然爆发。同时，表层修复往往治标不治本：例如 AIHOT 的 OpenAI 抓取故障，只把 OpenAI 的抓取单独修好，底层流量路由隐患仍在，未来其他信源还会出问题，"缝缝补补，最后堆成一座屎山"。

## 根因

AI 默认做类比推理，开发过程容易跳过"这个问题真的应该这么解吗"；日常测试缺少对抗性视角和底层视角，极端输入（如 50MB / 100M 的 HTML、未来时间戳）与深层机制（如流量路由）问题难以被发现。

## 方案

- 每 2 到 3 周，定期对整个项目做一次全局性的、从第一性原理出发的对抗式审查：让 Agent 从最底层原理出发，并发审查架构、依赖关系、代码质量、文档对应等。
- 日常开发后的测试阶段使用对抗式审查 prompt（"如果我是一个恶意用户……"），并尽量采用 [[concepts/multi-agent|多 Agent]]（Claude Code 的 Ultracode 动态工作流、Codex 多 Agent）。
- 解决问题、修 BUG、设计架构时，在 Prompt 末尾加"从第一性原理出发"，先找到最本质的根因再动手。

## 验证

- 作者称每次都能挑出之前没注意到的技术债和潜在风险。
- 6 月初一次近 40 个 Agent 的对抗式审查找出了 OOM 死循环、未来时间污染、HTML 清洗模块性能炸弹、翻译模块同类隐患、部署探活缓存穿透假阳性等风险。
- 追加第一性原理 prompt 后找到流量路由层面的深层隐患，作者重构后从机制上看"未来大概率就可以安心了"。
- AIHOT 在"偶尔出一些小BUG"的情况下仍能稳定服务（最近一周请求量超过千万），作者将之归功于这两个 Prompt。

## 适用范围

作者自述适用于 Vibe Coding 项目（本人是"纯粹的不懂代码的小白"，完全依赖 AI）；作者认为两个思维的应用范围不止 Vibe Coding，也可用于文章审查、商业方案审视和人生决策。本实践在文中直接应用于作者的项目 [[systems/aihot|AIHOT]]（applies_to）。

## 与相关概念的关系

- 本实践组合使用 [[concepts/first-principles|第一性原理]]（uses）与 [[concepts/adversarial-review|对抗式审查]]（uses）两种 Prompt 技巧。

## 失效条件与知识缺口

- Raw 未明确给出该实践的失效条件、不适用场景或量化效果对比；以上内容均为文中作者自述，缺少独立验证数据。
