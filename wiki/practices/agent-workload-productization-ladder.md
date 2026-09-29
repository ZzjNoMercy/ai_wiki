---
title: Agent 工作负载产品化阶梯：从 Raw Session 到 Specialized Agent
type: engineering_practice
sources:
  - read-later-4445b9b74d/later_81b795f2b793454db6ae5691-a15e695237-16d194673310.md
created: 2026-09-02
updated: 2026-09-02
schema_version: 0.4.0
---

# Agent 工作负载产品化阶梯：从 Raw Session 到 Specialized Agent

## 问题

开放式 Agent Session 适合探索，但用户需要自行提供上下文和 Steering，任务边界不固定，只能以 Session 粗略计价。随着使用量增长，这种形态难以稳定 Benchmark、路由模型、约束质量，也难把每一美元映射到实际完成的工作。

## 根因

- 任务还没有被定义为可重复、可验证的过程。
- 上下文依赖个人临场提供，系统无法稳定复现输入条件。
- 执行停留在个人设备与交互式会话，平台不能完整控制 Harness、模型和运行时。
- 度量仍是 Session、Request 或 Token，而不是合并 PR、Review、Alert、Cleanup 等业务结果。

## Uber 的原始四层

Uber 把 Agent 使用分为四层。原文称其为「四层执行形态」，并未规定所有任务必须按顺序升级：

1. **Raw Sessions**：交互式、本地运行，用户提供 Prompt、Context 与 Steering，没有固定任务边界，以 Session 计价。
2. **Sessions with Skills**：仍为交互式本地会话，但把可重复过程封装为持续优化的 Skill，仍以 Session 计价。
3. **General Agents**：相同 Skill 进入 Uber Cloud 托管，由通用 Agent 执行，以 Query 计价。
4. **Specialized Agents**：最窄任务边界，Agent 驱动并保留 HITL；为具体工作负载建立真实 Benchmark、选择 Pareto 最优模型，并以完成工作的结果单位计价。

## 可操作化的产品化阶梯

基于这四层，可以把从探索到托管结果的演进提炼为四个阶段；以下「晋级门槛」是对 Uber 模型的工程化解释，不是原文逐字规定：

| 阶段 | 要沉淀的资产 | 建议晋级门槛 | 不应急于晋级的信号 |
|---|---|---|---|
| 1. 探索：Raw Session | 有效 Prompt、所需上下文、人工 Steering 记录 | 同类任务反复出现，成功路径开始可描述 | 目标频繁变化、主要价值仍来自人的探索 |
| 2. 固化：Session with Skill | 可复用 Skill、输入输出契约、失败处理 | Skill 能在多个 Session 稳定复用，关键步骤可验证 | Skill 仍依赖大量临场判断或隐性上下文 |
| 3. 托管：General Agent | 云端运行、认证、策略、统一 Harness、按需工具 | 无需本地环境即可完成同一 Skill，运行可观测、可升级 | 数据权限或执行环境不能安全托管 |
| 4. 产品化：Specialized Agent | 窄任务边界、真实 Benchmark、结果指标、HITL/升级路径 | 可以按完成结果计价，并在模型迁移时证明质量不退化 | 结果不可客观判定、样本不足或错误代价过高 |

## 方案

1. 先让任务在 Raw Session 中被真实完成，保留成功路径与失败轨迹。
2. 当步骤开始重复时，把过程封装为 Skill，而不是立即造独立 Agent 产品。
3. 当 Skill 的输入、权限和环境稳定后，再放入统一云端 Harness，以平台方式提供认证、策略、成本和上下文治理。
4. 只有当任务可被收窄并拥有真实评测集时，才建立 Specialized Agent；同时定义结果成本、质量指标、工作量和人工升级机制。
5. 每次模型迁移都重新跑真实任务 Benchmark，并在成本、质量和可靠性之间选择 Pareto 前沿上的配置。

## 验证

Uber 的组合数据为这条路线提供了规模化证据：超过 70% 的 PR 归因于本地或云 Agent，3,600 多个 Skill 每日执行超过 30,000 次；在周活用户增长 7 倍、请求量增长 9.4 倍的同时，总 AI 支出自 2026 年 4 月起相对稳定。固定模型口径下，每千次请求成本下降近 34%，每 Session 成本下降 52%。这些数字说明多种形态可以共同运营，但不能单独证明四阶段之间存在严格因果关系。

## 适用范围

- 已经出现重复 Agent 工作负载，希望从个人效率工具走向平台服务的组织。
- 能建设统一 Harness、云端执行、真实任务评测和结果观测的团队。
- 代码生成、审查、部署、告警与维护等能够定义任务边界和验收结果的流程。

## 失效条件与边界

- 四层是共存的产品组合，不是所有任务必经的线性成熟度模型。
- 一次性、低频或高度探索性的工作留在 Raw Session 往往更经济。
- 没有真实 Benchmark 时，专用 Agent 只是固定工作流，无法证明模型选择或质量改进。
- 无法把结果与 Agent 行为可靠归因时，cost / outcome 会产生误导。
- 高风险任务即使完成度量清晰，也可能必须保留人工审批或升级路径。
- Uber 的成本降幅依赖其代码库、团队规模与工作负载，不能作为普遍承诺。

## 关系

- derived_from → [[media/running-a-software-factory-efficiently-at-uber-scale|Running a Software Factory Efficiently at Uber Scale]]
- applies_to → [[concepts/harness|Harness（模型外层执行系统）]]
- implemented_by → [[systems/uber-software-factory|Uber Software Factory]]
- relates_to → [[practices/outcome-based-agent-economics|面向结果的 Agent 单位经济性]]
- relates_to → [[practices/benchmark-driven-agent-model-routing|基于真实任务 Benchmark 的 Agent 模型路由]]