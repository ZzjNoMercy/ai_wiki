---
title: 证据驱动的受约束 Harness 演化闭环
type: engineering_practice
sources:
  - read-later-4445b9b74d/later_abadf6c614f140da9b1d1fb5-0fce80db03-bcc916448a53.md
created: 2026-09-27
updated: 2026-09-27
schema_version: 0.4.0
---

# 证据驱动的受约束 Harness 演化闭环

这是一套把 Self-Harness 与 Agentic Harness Engineering（AHE）抽象成工程流程的方法：Agent 可以根据运行证据修改 [[concepts/harness|Harness]]，但修改必须发生在声明的可编辑面内，并经过独立验证、受控晋级和可逆部署。它 derived_from [[media/harness-engineering-for-self-improvement|Harness Engineering for Self-Improvement]]，applies_to [[concepts/agent-self-improvement|Agent Self-Improvement]]，并与 [[practices/harness-self-improvement-challenge-checklist|Harness 自我改进七项挑战检查框架]] 互补。

## 问题

让 Agent 根据失败轨迹直接修改 Prompt、Skill、工具或工作流，很容易产生三种假改进：针对单个案例打补丁；通过修改评估器、预算或权限制造分数提升；修复当前失败的同时破坏其他任务。只要修改、评价和发布混在同一权限域，一次 Benchmark 上升就无法证明 Harness 真实改善。

## 根因

- 失败日志常只记录 `timeout`、`missing artifact` 等表面结果，没有把 verifier 结果、Agent 行为和抽象机制串成因果证据。
- Harness 的可编辑组件没有显式表示，提案者可能顺手修改运行环境、模型配置或评价标准。
- 成功行为与历史失败修改没有进入提案上下文，导致修一个问题、重新引入另一个问题。
- 候选只在发现问题的数据上测试，容易把 held-in 改善误认为可泛化提升。
- 通过测试的改动直接覆盖活动版本，缺少版本、晋级门、回滚点和责任主体。

## 核心协议

```text
Evidence
  → Bounded Edit
  → Independent Validation
  → Controlled Promotion
  → Reversible Deployment
```

### 1. Evidence：先形成可追溯的失败证据

从当前 Harness 执行任务并保存原始轨迹。对每个失败至少区分：

- **终端结果**：verifier 观察到的失败，如超时、测试失败或工件缺失；
- **行为原因**：Agent 做了什么或没有做什么，导致该结果；
- **机制原因**：暴露的是上下文、工具、工作流、记忆、权限还是其他 Harness 组件的问题；
- **重复性**：这是单个任务特例，还是多个任务中反复出现、可由 Harness 解决的模式。

原始轨迹不能只被压缩成一句模型总结。推荐同时保存逐任务分析和跨任务聚合，使提案者能先看模式，必要时下钻到原始证据。

### 2. Bounded Edit：只在声明的可编辑面提出窄改动

把 Harness 组件表示为可检查的文件或配置，例如：system prompt、工具描述、工具实现、中间件、Skill、子 Agent 配置和长期记忆。每个候选只修改与失败模式相对应的组件，并满足：

- 修改目标能由证据解释，不以“全面优化”为理由扩大范围；
- 保留当前通过任务中的关键行为；
- 参考历史候选及其失败原因，避免重复无效修改；
- 候选之间具有实质差异，而不只是措辞变体；
- 模型、预算、verifier、tracer、权限系统和运行记录不属于候选编辑面。

### 3. Independent Validation：由循环外的评价系统验证

候选至少经过两类数据：

- **held-in**：确认被定位的弱点是否真的修复；
- **held-out**：检查修改是否破坏未参与提案的任务和行为。

评价应读取环境事实、测试结果和原始产物，而不是只接受提案 Agent 的自评。评估器、数据划分、模型版本和推理预算必须在该轮开始前固定；否则不能把收益归因于 Harness 修改。

### 4. Controlled Promotion：验证通过不等于立即上线

为每个候选生成 change manifest，至少记录：

- 证据与失败模式；
- 推断的根因；
- 修改的文件和 Harness 组件；
- 预期修复；
- 可能回归；
- held-in 与 held-out 结果；
- 未覆盖风险和人工判断事项。

只有满足预先声明的门槛，候选才能从实验版本晋级。高权限、高副作用或难量化目标的修改需要人工复核；低风险且有确定性 verifier 的修改可以更自动化。

### 5. Reversible Deployment：让新 Harness 可撤回

活动 Harness 应指向版本化产物，而不是被候选原地覆盖。部署后继续观察真实任务、成本和长期回归；一旦出现未预期行为，可以恢复上一版本，同时保留失败候选和回滚原因供后续学习。

回滚不是“删除失败”。失败候选、原始轨迹和评价结果应成为负结果资产，帮助缩小未来搜索空间。

## 不可由演化循环修改的边界

最低限度应把以下控制面放在候选 Harness 之外：

- verifier、评分规则与 held-out 数据；
- 身份、授权、工具权限和 Workspace 边界；
- 基础模型、模型路由、推理预算和成本上限；
- tracer、审计日志和原始运行记录；
- 晋级、生产部署和回滚权限。

如果任务本身就是研究其中某项控制面，应使用更外层的独立评估与权限域，而不是取消边界。

## 最小变更记录

```yaml
evidence:
  failures: [failure-id]
  preserved_successes: [trace-id]
root_cause:
  component: tool_description
  claim: "参数约束没有暴露，导致重复调用失败"
edit:
  files: [path]
  bounded_surface: tool_description
prediction:
  expected_fix: "相关参数错误下降"
  at_risk: "工具选择可能变得过于保守"
validation:
  held_in: pass
  held_out: no_regression
promotion:
  decision: accepted
  rollback_to: previous-version
```

字段名称可以变化，但证据、根因、编辑面、预测、验证、晋级决定和回滚点不应缺失。

## 验证清单

- 能否从聚合失败模式下钻到原始轨迹和 verifier 结果？
- 每项修改是否只触及声明的 Harness 组件？
- 候选是否通过 held-in 修复验证和 held-out 非回归验证？
- 模型、预算、评估器、权限和日志是否在循环外保持固定？
- 提升是否来自真实任务结果，而非关闭检查、增加预算或更换模型？
- 活动版本、候选版本、被拒绝版本和回滚目标是否可区分？
- 无法自动量化的长期价值是否保留人工判断，而非伪造总分？

## 适用范围

- 根据运行轨迹更新 Prompt、Skill、工具说明、工作流或 Harness 代码的系统；
- 使用 propose–evaluate–accept、进化搜索或其他候选搜索机制的 Agent；
- 有重复任务、可保留轨迹、能够构造 held-out 验证集的编码与研究工作负载；
- 需要把 Agent self-improvement 变成可审计工程流程，而不是一次性 Prompt 调优的团队。

## 失效条件与边界

- verifier 无法代表真实目标时，本流程只能证明代理指标改善，不能证明真实价值提高。
- 任务样本太少或 held-out 与生产分布差异过大时，非回归结论不可靠。
- 基础模型不具备诊断或编辑能力时，递归循环可能稳定地产生更差候选。
- 对研究品味、长期维护性与社会影响等模糊目标，自动门控不能替代领域专家和责任主体。
- 本流程约束的是 Harness 演化，不证明模型权重能够安全、开放式地递归自我改进。

## 与七项挑战检查框架的分工

本页回答“**一次 Harness 改动如何安全发生**”；[[practices/harness-self-improvement-challenge-checklist|七项挑战检查框架]]回答“**即使闭环运行正常，还可能遗漏哪些长期问题**”。前者是变更协议，后者是架构与风险审查，两者应同时使用。

## 关系

- derived_from → [[media/harness-engineering-for-self-improvement|Harness Engineering for Self-Improvement]]
- applies_to → [[concepts/harness|Harness（模型外层执行系统）]]
- applies_to → [[concepts/agent-self-improvement|Agent Self-Improvement]]
- relates_to → [[concepts/recursive-self-improvement|递归自我改进（RSI）]]
- relates_to → [[concepts/loop-engineering|Loop Engineering]]
- relates_to → [[practices/harness-self-improvement-challenge-checklist|Harness 自我改进七项挑战检查框架]]
