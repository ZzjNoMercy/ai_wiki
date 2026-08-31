---
title: "对抗式审查"
type: concept
sources:
  - "read-later-4445b9b74d/later_809e9deef2b14e7d97a162b4-b9f587c39f-042741626d7c.md"
  - conversation-correction-codex-multi-agent-token-20260830-1c071d3fe3/multi-agent-token-wiki-scope-v2-c875e42ad4-adddccc75aa9.md
created: "2026-08-04"
updated: 2026-08-30
schema_version: "0.4.0"
---

# 对抗式审查

## 定义

Vibe Coding 中"管验证"的审查 Prompt 技巧：让 AI 站在"如果我是一个恶意用户……"的角度，以攻防方式对系统/代码找漏洞，用于开发完成后的测试/验证阶段。作者称自己现在做开发，最后的测试流程几乎必然是对抗式审查。

## 典型用法

- 示例视角："如果我是一个恶意用户，我会提交一个50MB的HTML来搞崩你的worker"——从入口到崩溃把整条路径走一遍，找出缺口，避免后续风险。作者提到信源加多后真的看到过 100M 的 HTML。
- 作者强烈建议采用 [[concepts/multi-agent|多 Agent]] 进行对抗式审查：例如对 Claude Code 说"开启 Ultracode（也就是动态工作流，会有N个Agent进行并发）来对之前开发的功能进行对抗式审查"；Codex 也可以，直接说开启多 Agent 做对抗性审查即可。

## 例证（AIHOT，文中所述）

6 月初（Claude Opus 4.8 和动态工作流上线之后），作者对 AIHOT 做了一次较大规模的对抗式审查（纯找 BUG），开启近 40 个 Agent，找出多个可能的风险：

- **OOM 死循环**：后台 worker 处理特别大的任务时内存爆掉被系统杀掉，随后自动重试，结果必然再爆再被杀，无限循环。
- **未来时间污染**：信源文章因时区错误显示未来时间戳，会被排到精选信息流最前面，还可能被推送给用户、进入飞书群 PUSH、RSS 订阅，日报也会把它排在最前，一篇"来自未来的文章"污染整个信息流。
- **其他**：HTML 清洗模块的性能炸弹、翻译模块的同类隐患、部署探活的缓存穿透假阳性等。

## 核心一句话

你永远需要一个站在你对面的力量来告诉你，你可能是错的。

## 与其他概念的关系

- 与 [[concepts/first-principles|第一性原理]]（relates_to）共同构成作者心目中 Vibe Coding 的两大基石：第一性原理保证找到好的方案和 BUG 的最本质解法，对抗式审查保证开发完成后能稳定上线；前者管生成、后者管验证。
- 属于 [[concepts/vibe-coding|Vibe Coding]]（relates_to）领域的核心验证技巧。

## 应用范围（文中所述）

作者认为不止代码：写完文章可以让 AI 对抗式审查（从逻辑漏洞、事实准确性、论证力度多个维度挑毛病）；商业方案可以让 AI 找盲点；人生决策（如是否换工作）可以让 AI 专门找思考中的盲点和下意识回避的风险。
