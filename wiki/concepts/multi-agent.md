---
title: Multi-Agent（多智能体）
type: concept
sources:
  - knowledge-file-4be80edb57/knowledge-imported-20260825-10-multi-agent-50-.md-c353f4a9b1-941251b63b09.md
  - conversation-correction-codex-multi-agent-token-20260830-1c071d3fe3/multi-agent-token-wiki-scope-v2-c875e42ad4-adddccc75aa9.md
created: 2026-08-30
updated: 2026-08-30
schema_version: 0.4.0
---

# Multi-Agent（多智能体）

## 定义

Multi-Agent（多智能体）是由多个 Agent 共同参与同一任务或系统的组织形式，不限定为固定工作流。它可以表现为主 Agent 调度专业子 Agent、多个 Agent 并发协作，或按角色划分的短生命周期执行单元。

## 本次材料中的组织方式

文章描述的实例由一个 TL 负责调度，后端、前端、代码审查、自动化测试、视觉验证和 Agent 评测分别交给不同 Agent。每个子 Agent 只携带本角色需要的提示词与工具，完成后销毁；没有数据依赖的任务可以并发执行，结果再汇总给上游。

## 成本特征

- 多 Agent 拆分会产生额外固定成本：多个 Agent 同时携带各自的系统提示词，拆分本身并不天然省 Token。
- 短生命周期子 Agent 可以把单一长会话的历史滚雪球切成较小片段，并允许按角色裁剪工具、模型和上下文。
- 小需求可能不值得拆分；文章采用 S/M/L 规模预判，小需求保留单 Agent，中大型需求才进入多 Agent 调度。
- 主 Agent 若直接接收 MCP 的大型原始 payload，内容会长期停留在最长生命周期 Context；专用子 Agent 返回结构化摘要可以降低后续轮次的重复输入。

## 相关页面

- [[practices/multi-agent-token-cost-optimization|Multi-Agent Token 成本优化实践]] applies_to 本概念所描述的多 Agent 组织形式。
- 本页 derived_from：[[media/multi-agent-token-cost-optimization|靠这10个优化点，我们把Multi-Agent工作流成本降了50%以上]]。
