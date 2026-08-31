---
title: 靠这10个优化点，我们把Multi-Agent工作流成本降了50%以上
type: media
sources:
  - knowledge-file-4be80edb57/knowledge-imported-20260825-10-multi-agent-50-.md-c353f4a9b1-941251b63b09.md
  - conversation-correction-codex-multi-agent-token-20260830-1c071d3fe3/multi-agent-token-wiki-scope-v2-c875e42ad4-adddccc75aa9.md
created: 2026-08-30
updated: 2026-08-30
schema_version: 0.4.0
---

# 靠这10个优化点，我们把Multi-Agent工作流成本降了50%以上

- 发布平台：微信公众平台。
- 发布渠道：[[sources/tencent-programmer|腾讯技术工程（微信公众号）]]（sourced_from）。
- Raw 元数据的 `author` 字段为“腾讯程序员”；正文署名为 lemonye。发布渠道、元数据作者字段和正文署名分别保留，不互换身份。
- 原文 URL：https://mp.weixin.qq.com/s/TIdXNlrcAOUZWVW1oWnnKQ
- 采集时间：2026-08-25。

## 内容概述

文章记录了一套面向 [[concepts/multi-agent|Multi-Agent（多智能体）]] 开发流程的 Token 成本治理实践：一个 TL 调度后端、前端、审查、测试、视觉验证和评测 Agent，完整中型需求包含 5–6 个 Wave、20 次以上子 Agent 调用和数百轮工具调用。作者将主要消耗归纳为系统提示词、工具返回、文件读取、长期记忆、历史消息和用户提示词六类，其中历史消息具有随轮次滚动累积的特点。

文章提出三个原则：让 AI 只看到当前需要的上下文、减少无关上下文、减少重复上下文。具体措施包括渐进式披露、确定性操作脚本化和 CLI 化、MCP 数据获取子 Agent 化、长期记忆索引、按任务规模选择单 Agent 或多 Agent、角色专属工具和模型配置、代码图谱、稳定前缀、避免重复加载 Skill、压缩 CLI 输出，以及无依赖工具调用并行化。

## 收录关系

- introduces：[[practices/multi-agent-token-cost-optimization|Multi-Agent Token 成本优化实践]]。
- discusses：[[concepts/multi-agent|Multi-Agent（多智能体）]]。
- mentions：[[frameworks/graphify|Graphify]]、[[frameworks/rtk|rtk]]。
- sourced_from：[[sources/tencent-programmer|腾讯技术工程（微信公众号）]]。
