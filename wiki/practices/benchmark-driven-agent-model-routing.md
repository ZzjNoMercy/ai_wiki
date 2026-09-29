---
title: 基于真实任务 Benchmark 的 Agent 模型路由
type: engineering_practice
sources:
  - read-later-4445b9b74d/later_81b795f2b793454db6ae5691-a15e695237-16d194673310.md
created: 2026-09-02
updated: 2026-09-02
schema_version: 0.4.0
---

# 基于真实任务 Benchmark 的 Agent 模型路由

## 问题

模型排行榜或通用编码分数不能说明某个 Agent 在特定代码库、Harness 和任务边界中的真实完成率。默认使用最强模型会提高成本，默认使用最便宜模型则可能降低质量、增加超时和重试。

## 根因

- 模型能力随语言、仓库、任务形态和工具链显著变化。
- Agent 的结果由模型与 Harness 共同产生，脱离实际执行环境的评测会漏掉超时、噪声和工具失败。
- 模型能力和价格频繁变化，静态路由很快脱离当前 Pareto Frontier。
- 主 Agent 与 Sub-Agent 的职责和生命周期不同，不应共享同一默认模型与缓存策略。

## 方案

1. 从 Agent 的真实工作构建 Benchmark，而不是仅采用通用榜单。
2. 用与生产一致的 Harness 运行所有候选模型，使任意模型可以在同一执行框架下被替换和比较。
3. 同时记录完成任务成本、输出质量和可靠性；只选择 Pareto Frontier 上的配置。
4. 定期重跑评测，因为新的模型和价格会每隔数周移动前沿。
5. 为主 Agent 与 Sub-Agent 分开选择默认模型：主 Agent 负责拆解、委派与结果判断，短生命周期 Sub-Agent 可优先使用更便宜、较弱但足够完成局部任务的模型。

Uber 的 uReview Benchmark 来自带已知 Bug 的真实 PR，并按 Easy、Medium、Hard 分级；评测 Precision、Recall、F1、cost / review、延迟、超时和噪声。Uber SWE Benchmark 则使用数千个真实 PR 评估编码 Agent。二者都把真实工作负载而非抽象模型能力作为路由依据。

## 验证

文章报告，uReview 切换模型后 F1 提高，同时显著降低 cost / PR；图中用 Pareto Frontier 标识没有被更便宜或更优配置支配的模型。Uber 还表示，其 Managed Agent 会为每个窄任务建立真实 Benchmark，并持续迁移到当时的 Pareto 最优模型。

## 适用范围

- 同一任务可由多个模型在统一 Harness 中执行。
- 有足够真实样本构建代表性评测集，并能定义质量、成本和可靠性指标。
- 模型价格与能力变化快，需要持续路由而非一次性选型的 Agent 平台。

## 失效条件与边界

- Benchmark 样本过少、过旧或只覆盖单一仓库时，Pareto 结论无法代表生产分布。
- 若 Harness、工具权限或上下文配置不一致，评测结果不能归因于模型。
- 低成本局部模型可能产生主 Agent 难以发现的错误；委派结果仍需验证。
- Pareto 最优不是单一全局模型，不同任务、语言和风险级别可能拥有不同前沿。
- 价格与缓存条款具有时间性，路由结论需要定期刷新。

## 关系

- derived_from → [[media/running-a-software-factory-efficiently-at-uber-scale|Running a Software Factory Efficiently at Uber Scale]]
- implemented_by → [[systems/uber-software-factory|Uber Software Factory]]
- applies_to → [[concepts/harness|Harness（模型外层执行系统）]]
- supports → [[practices/outcome-based-agent-economics|面向结果的 Agent 单位经济性]]
- relates_to → [[practices/agent-workload-productization-ladder|Agent 工作负载产品化阶梯]]

