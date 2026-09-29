---
title: Agent Self-Improvement
type: concept
sources:
  - knowledge-file-4be80edb57/knowledge-imported-20260830-how-warp-builds-self-improving-agents-on-claude---claude-by--d0eb9dd10a-d24d9bd248b7.md
  - read-later-4445b9b74d/later_abadf6c614f140da9b1d1fb5-0fce80db03-bcc916448a53.md
  - read-later-4445b9b74d/later_84091bcce34b4bab91a7ad57-f00ab3bff8-26d6d03086bf.md
created: 2026-09-01
updated: 2026-09-22
schema_version: 0.4.0
---

# Agent Self-Improvement

Agent Self-Improvement（Agent 自我改进）是一个宽概念：[[concepts/agent|Agent]] 根据任务结果、评估或人类反馈，修改能被后续运行复用的组成部分，使后续行为有机会改善。可修改对象不只包括模型权重，也包括提示词、结构化上下文、Skill、工作流、Harness 代码，乃至负责改进这些对象的优化器代码。

## 最小闭环

一个可持续的自我改进闭环至少包含：

1. **持久改进信号**：保留人类反馈、任务结果、评估、运行轨迹或失败记录，避免信号随单次会话消失。
2. **可编辑的复用对象**：明确改动落在 prompt、Skill、上下文结构、workflow、Harness 或优化器中的哪一层。
3. **改进器**：分析信号并提出小而可审查的候选修改；改进器可以是另一套 Skill、Agent 或自动研究流程。
4. **评价与门控**：用人类审核、任务评测、verification harness、golden outputs 或其他可验证信号判断候选是否保留。
5. **跨运行继承**：被接受的修改进入后续任务运行，而不是只改善当前对话。

## 算法结构的阶段判断

**阶段性判断：LLM 把经典优化算法从结构化参数空间，扩展到了自然语言、代码和 Agent 架构等开放的语义空间。**

这不表示外层优化算法都被重新发明。从现有材料中的 MCE、ADAS、AFlow、STOP 与进化式程序搜索看，许多方法仍沿用双层优化、蒙特卡洛树搜索、进化搜索、bandit、模拟退火、beam/tree search 等既有框架；主要变化在候选表示与提案算子。LLM 能根据任务说明、代码、历史轨迹和评价反馈，生成或修改 prompt、上下文函数、Skill、workflow、Harness 乃至 optimizer code，使搜索从数值、超参数和固定结构，进入过去很难用随机扰动或人工模板有效探索的语义化可执行空间。

这里的收益也不应简单理解为计算效率提升。多层 LLM 调用与候选评估可能更加昂贵；更直接的提升是候选的语义有效性、可搜索设计空间的范围，以及把部分人工 Harness 设计转化为可评估的自动提案过程。其上限仍取决于评价信号是否可信、候选是否可以低成本验证，以及权限、预算和评估器能否保持在可编辑循环之外。

## 三种材料中的形态

- **Skill 层、有人类审核的改进**：Warp 将内层任务 Skill、明确的人类反馈和外层 improver Skill 组成闭环；候选修改经 PR、code review 与合并后由下一次运行继承。见 [[practices/skill-based-agent-self-improvement-loop|基于 Skill 的 Agent 自我改进闭环]] 与 [[media/how-warp-builds-self-improving-agents-on-claude|How Warp builds self-improving agents on Claude]]。
- **Harness 与工作流层的改进**：Lilian Weng 将优化对象概括为从 prompt、structured context、workflow、harness code 到 optimizer code 的递进，并讨论 propose-evaluate-accept 型自改进 Harness。见 [[media/harness-engineering-for-self-improvement|Harness Engineering for Self-Improvement]]。
- **运行时可替换的 Harness**：DeepSeek Harness 把 Agent Loop 等组件插件化，为 Agent 在运行时修改更深层 Harness 组件并实时生效预留结构；文章同时指出，这在当前仍偏研究性，并非通用产品刚需。见 [[frameworks/deepseek-harness|DeepSeek Harness（DSH）]] 与 [[media/deepseek-of-deepseek-harness|The deepseek of DeepSeek Harness]]。

## 与 Memory 的边界

Warp 的文章把两者区分为：Skill 保存程序性、跨运行且有意维护的知识；memory 通常在推理期间自动写入并持续变化。写入记忆可以成为改进信号或较浅层的更新，但“有记忆”本身不自动构成一套经过评价和继承的自我改进机制。

## 与 RSI 的边界

[[concepts/recursive-self-improvement|递归自我改进（Recursive Self-Improvement, RSI）]] 是更严格的范围：系统使用当前能力去改进产生其能力的认知机制或改进机制，并由此形成递归反馈。Agent Self-Improvement 不要求每次改动都具有这种递归性；例如 Warp 的方案依赖人类反馈与 PR 审核，修改的是任务 Skill，因此属于 Agent 自我改进，但不能仅凭该材料称为严格 RSI。

## 风险与失效条件

- 反馈可能错误、稀疏或互相冲突，未经筛选会把错误固化进复用对象。
- 评估器较弱或目标模糊时，容易出现过拟合、奖励黑客、短期指标改善但长期效果下降。
- 修改范围越深，验证、回滚、权限与安全边界越重要；运行时自动替换 Harness 的风险高于经人工审核、下次运行生效的 Skill 更新。
- 缺少多样性与负结果记录会让搜索反复走入相同失败路径。

## 关系

- applies_to → [[concepts/agent|Agent（智能体）]]
- relates_to → [[concepts/recursive-self-improvement|递归自我改进（RSI）]]
- applies_to → [[concepts/harness|Harness（模型外层执行系统）]]
- relates_to → [[practices/evidence-driven-bounded-harness-evolution|证据驱动的受约束 Harness 演化闭环]]
