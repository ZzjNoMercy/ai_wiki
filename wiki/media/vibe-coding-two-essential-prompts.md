---
title: "分享2个Vibe Coding必备的超实用Prompt。"
type: media
sources:
  - "read-later-4445b9b74d/later_809e9deef2b14e7d97a162b4-b9f587c39f-042741626d7c.md"
  - conversation-correction-codex-multi-agent-token-20260830-1c071d3fe3/multi-agent-token-wiki-scope-v2-c875e42ad4-adddccc75aa9.md
created: "2026-08-04"
updated: 2026-08-30
schema_version: "0.4.0"
---

# 分享2个Vibe Coding必备的超实用Prompt。

- 标题：分享2个Vibe Coding必备的超实用Prompt。
- 发布平台：微信公众平台
- 账号：数字生命卡兹克
- 署名：卡兹克
- 原文 URL：https://mp.weixin.qq.com/s/umPqTD_-IubbhXIgiS47eQ
- 采集时间：2026-08-04
- 文末投稿/爆料邮箱：wzglyay@virxact.com
- sourced_from：[[sources/digital-life-kazik]]（sourced_from）

## 内容概要

作者（署名卡兹克）在周末与朋友吃饭时被问及"Vibe Coding 中最实用的小技巧"，给出了两个他称为"神级 Prompt"的技巧，并称这两个词是自己在近 1 年 Vibe Coding 时间里每天跟 AI 说的最高频词汇：前者管生成，后者管验证，合在一起构成完整闭环，是作者心目中 Vibe Coding 的两大基石。

1. **第一性原理**：在平时的说法后面加一句"从第一性原理出发"（[[concepts/first-principles|第一性原理]]，discusses）。
2. **对抗式审查**：让 AI 站在"如果我是一个恶意用户……"的角度对系统/代码找漏洞，用于开发完成后的测试/验证（[[concepts/adversarial-review|对抗式审查]]，discusses）。

文章以作者自研产品 [[systems/aihot|AIHOT]]（discusses）的实际事故为例证，通篇围绕 [[concepts/vibe-coding|Vibe Coding]]（discusses）展开：作者自称"纯粹的不懂代码的小白"，完全依赖 AI 进行 Vibe Coding；身边朋友（基金经理、设计师、老师、产品经理、媒体人等）几乎都不是专业程序员，也都在使用 Vibe Coding。

## 关键内容

- 作者认为其他技巧（约束先行、洁癖 skill 做文档迭代等）也有用，但"如果只能选两个"就选第一性原理和对抗式审查。
- 第一性原理例证：AIHOT 周五精选消息飞书推送出 BUG，OpenAI 发布 GPT-5.6 这类大新闻在周六凌晨未被推送，周六上午作者收到二十多条用户反馈；Agent 初步发现是之前测试国产模型时 OpenAI 抓取被改坏、断抓约三天；追加"根据第一性原理来找一下原因"后，找到海外信源抓取规则中流量路由层面的深层隐患，作者花半天时间重构底层路由（治本 vs 治表）。
- 对抗式审查例证：6 月初（Claude Opus 4.8 和动态工作流上线之后）作者对 AIHOT 开启近 40 个 Agent 做对抗式审查，找出 OOM 死循环、未来时间污染、HTML 清洗模块性能炸弹、翻译模块同类隐患、部署探活缓存穿透假阳性等风险。
- 作者建议采用 [[concepts/multi-agent|多 Agent]] 进行对抗式审查，例如对 Claude Code 说"开启 Ultracode（也就是动态工作流，会有N个Agent进行并发）来对之前开发的功能进行对抗式审查"；Codex 也可以直接说开启多 Agent 做对抗性审查。
- 收尾部分：作者现在每 2 到 3 周定期对整个项目做一次"从第一性原理出发的对抗式审查"（[[practices/periodic-adversarial-review|定期全局对抗式审查]]，discusses）；AIHOT 最近一周请求量超过千万，Skill 调用量是网页端的 10 倍以上；作者认为两个思维的应用范围不止 Vibe Coding（文章审查、商业方案、人生决策皆可）。
