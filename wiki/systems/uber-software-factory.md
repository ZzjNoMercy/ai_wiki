---
title: Uber Software Factory
type: system
sources:
  - read-later-4445b9b74d/later_81b795f2b793454db6ae5691-a15e695237-16d194673310.md
created: 2026-09-02
updated: 2026-09-02
schema_version: 0.4.0
---

# Uber Software Factory

## 定义

Uber Software Factory 是 Uber 围绕软件开发生命周期组织 Agent 工具、Skill、通用 Agent 与专用托管 Agent 的工程体系。它覆盖 Code Gen、Validate、Deploy、Observe、Maintain，并用统一的成本、质量、模型和上下文治理支持本地及云端执行。

## 四类执行形态

| 形态 | 交互与运行位置 | 上下文或任务边界 | 计量单位 |
|---|---|---|---|
| Raw Sessions | 交互式、工程师笔记本 | 用户提供 Prompt、Context 与 Steering；没有固定任务边界 | cost / session |
| Sessions with Skills | 交互式、工程师笔记本 | 同一 Skill 在本地执行；使用 3,600 多个沉淀 Skill | cost / session |
| General Agents | 交互式、Uber Cloud | 同一 Skill 托管执行；Cortana 提供零配置的通用任务执行 | cost / query |
| Specialized Agents | Agent 驱动并带 HITL、Uber Cloud | 最窄任务边界、真实 Benchmark、按工作结果运营 | cost / merged PR、review、XP readout、alert 或 cleanup |

四层从 Specialized 到 General 排列时，越靠 Specialized，系统对成本、质量和模型选择的控制越强。它们不是互斥替代关系：开放探索仍适合 Raw Session，可重复过程适合 Skill，跨环境执行适合托管通用 Agent，稳定且高频的闭环任务才适合专用 Agent。

## 专用 Agent 组合

- Minion：将意图推进为 PR，以合并 PR 的成本计量。
- uReview：对 PR 执行代码审查，以每次 Review 的成本计量。
- Agentic XP：从 XP 完成推进到 Readout，以每份 XP Readout 的成本计量。
- Conan AI：从告警生成 AI RCA，以每个 Alert 的成本计量。
- Fawkes：由 Cron 触发维护，以每次 Cleanup 的成本计量。

## 规模化控制面

- 成本方程把总支出拆为用户数、每用户 Session、每 Session Turn、每 Turn Request、每 Request Token 与 Token 单价六项。
- Managed Agent 同时追踪结果成本、质量信号与工作量，避免仅凭 Token 或请求数判断价值。
- 真实任务 Benchmark 与 Pareto Frontier 控制不同工作负载的模型选择。
- MCP Gateway 统一认证和策略；CLI/Tool Search 按需解析工具；Code Mode 在模型上下文外完成轮询与批处理。
- AI Context Graph 连接服务、团队、事故、PR、设计文档、部署、数据集与历史查询使用等上下文。

## 验证与边界

Uber 报告了高采用量与单位成本下降，但同时指出具体收益依赖其环境。该体系证明这些机制已在 Uber 规模运行，不等于每个组织都应直接复制同样层级、工具数量或阈值。

## 关系

- introduced_by → [[media/running-a-software-factory-efficiently-at-uber-scale|Running a Software Factory Efficiently at Uber Scale]]
- implements → [[practices/agent-workload-productization-ladder|Agent 工作负载产品化阶梯]]
- implements → [[practices/outcome-based-agent-economics|面向结果的 Agent 单位经济性]]
- implements → [[practices/benchmark-driven-agent-model-routing|基于真实任务 Benchmark 的 Agent 模型路由]]
- uses → [[concepts/harness|Harness（模型外层执行系统）]]

