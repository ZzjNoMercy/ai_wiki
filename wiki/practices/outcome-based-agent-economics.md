---
title: 面向结果的 Agent 单位经济性
type: engineering_practice
sources:
  - read-later-4445b9b74d/later_81b795f2b793454db6ae5691-a15e695237-16d194673310.md
created: 2026-09-02
updated: 2026-09-02
schema_version: 0.4.0
---

# 面向结果的 Agent 单位经济性

## 问题

只看总支出、Token 单价或每 Session 成本，会把采用增长、交互深度、模型变化和真实产出混在一起。成本下降可能只是使用结构变化，成本上升也可能来自完成了更多高价值工作。

## 根因

- 总支出是多个乘数共同作用的结果，单一指标不能解释变化来源。
- 交互式会话缺少稳定任务边界，难以和业务结果一一对应。
- 模型升级会同时改变价格、Token 使用、完成率和质量，前后数字不可直接比较。
- 只优化便宜模型可能牺牲质量、可靠性或制造返工。

## 方案

先把总支出拆为：

```text
用户数 × 每用户 Session 数 × 每 Session Turn 数
× 每 Turn Request 数 × 每 Request Token 数 × 每 Token 价格
```

前两项代表希望增长的采用与参与度；中间三项反映 Agent 为完成用户请求而产生的内部工作，是计划、减少错误、控制轮次和压缩输入的主要优化面；最后一项由模型选择和供应商定价共同决定。

在 Portfolio、工具、模型和成本驱动项之外，为每个 Managed Agent 增加三组结果指标：

- 结果成本：cost / merged PR、review、alert、cleanup 等。
- 质量：revert rate、F1、MTTR 等与任务匹配的信号。
- 工作量：落地 Diff、发布 Review、分诊 Alert 等。

模型或 Harness 变化时，既比较单位结果成本，也检查质量与工作量，避免把少做、失败或降质误判为优化。

## 验证

Uber 在固定模型口径下报告每千次请求成本较峰值下降近 34%，每 Session 成本较 6 月峰值下降 52%；2026 年 2 月至 8 月周活用户增长 7 倍、请求量增长 9.4 倍，而总 AI 支出自 4 月起相对稳定。文章还以 uReview 的 cost / review 与 F1 联合评估为例，说明成本和质量必须同时进入模型选择。

## 适用范围

- 同时运营多个 Agent 产品或多种模型、需要解释预算变化的团队。
- 能够定义稳定完成结果，并把 Agent 成本归因到该结果的托管工作负载。
- 希望在采用增长的同时控制单位成本，而不是简单限制使用量的组织。

## 失效条件与边界

- 开放探索任务没有稳定结果单位时，只能暂用 Session 或活跃小时等代理指标。
- 归因链不完整会让 cost / outcome 失真，例如 PR 由人和多个 Agent 共同完成。
- 结果数量不能替代质量；低质量自动化可能通过返工把成本转移到下游。
- 固定模型只能隔离部分变量，工作负载组合、缓存与用户行为仍可能变化。
- Uber 的具体降幅是其环境数据，不应直接当作其他团队的预算基线。

## 关系

- derived_from → [[media/running-a-software-factory-efficiently-at-uber-scale|Running a Software Factory Efficiently at Uber Scale]]
- implemented_by → [[systems/uber-software-factory|Uber Software Factory]]
- relates_to → [[practices/agent-workload-productization-ladder|Agent 工作负载产品化阶梯]]
- depends_on → [[practices/benchmark-driven-agent-model-routing|基于真实任务 Benchmark 的 Agent 模型路由]]

