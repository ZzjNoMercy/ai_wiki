---
title: "Harness-Zero: Harness Distillation via Agent-as-Harness"
type: research_paper
sources:
  - manual-upload-harness-zero-paper-a648d690f9/arxiv-2609-24974-v1-20ecc4840e-0cbb660ab65d.md
created: 2026-09-29
updated: 2026-09-29
schema_version: 0.4.0
---

# Harness-Zero: Harness Distillation via Agent-as-Harness

作者：Haoran Ye、Yuxing Lu、Haonan Dong、Zhaochen Su、Guojie Song。2026 年预印本，arXiv:2609.24974v1；[论文](https://arxiv.org/abs/2609.24974)，[代码](https://github.com/metaevo-ai/harness-zero)。以下结论限于论文报告的训练、模型、Harness 与基准配置。

## 问题与方法

领域专用 Harness 可以改进 Agent，但收益通常依附于该 Harness。若源 Harness 与部署时的固定目标 Harness 在工具动作和可用信息上不同，直接模仿源 Harness 的轨迹会要求学生执行不存在的动作，或依据其当时看不到的信息行动。

论文提出 Harness-Zero：先在训练任务上演化学生侧专用 Harness `h*`，再相对于固定目标 Harness `h` 将其工具、中间件、技能和记忆改写为审查 Agent 私下使用的参考 Harness `K`。学生始终在 `h` 下提出下一步响应；审查 Agent 在响应执行前根据 `K` 选择放行或作最小且完整的替换。只有被接受的响应通过 `h` 执行，执行结果进入学生可见轨迹；审查讨论和被拒绝的提案不进入该轨迹。最后对被接受的轨迹做监督微调，部署时只保留更新后的模型和 `h`，移除 `h*`、`K` 与审查 Agent（论文第 3 节）。

这一方法的工程抽象见 [[practices/cross-harness-behavior-distillation|跨 Harness 行为蒸馏实践]]；它针对 [[concepts/harness|Harness]] 的部分可学习行为，不意味着模型可以取代外部工具、权限或真实环境状态。

## 实现与实验边界

- 专用 `h*` 在每个基准的训练任务上迭代三轮，采用 [[frameworks/deepagents|DeepAgents]] 的工具、中间件、技能和记忆组件抽象；演化 Agent 使用 Kimi K3 / Kimi Code。演化时，用挂载 `h*` 的目标学生模型执行评估，不由审查 Agent 代跑（第 3.1、4.1 节及附录 A）。
- 固定目标 `h` 是 mini-SWE-agent 风格的最小 Harness，使用固定系统提示词与一个 Bash 执行工具。蒸馏实验的基座学生模型为 Qwen3.5-9B，审查 Agent 为 GPT-5.6 Sol；训练为两轮 LoRA SFT（第 4.1 节）。
- 三个任务域是 SpreadsheetBench Verified、AppWorld 和 USPTO Retrosynthesis。论文报告，固定 `h` 下基座模型的三域宏平均成绩为 23.3%，挂载 `h*` 的基座模型为 41.7%，蒸馏后仅运行 `h` 的模型为 44.3%（表 2）。这支持该配置下部分专用 Harness 诱导行为进入模型权重，不能推广为任意 Harness 均可完整内化。
- USPTO 的监督来源对照中，直接使用更强模型或 `h*` 下的轨迹，测试结果为 3%–12%；使用 `K` 指导的逐步审查轨迹为 30%（表 3）。这是同一 SFT 配方内的对照；论文未提供与直接在 `h` 下进行强化学习后训练的比较。
- 在三域 28 种由 `h*` 诱导、基座模型缺失的行为模式上，论文报告平均恢复率为 82.3%（表 4）。

## 具体案例与局限

电子表格案例中，`h*` 的中间件可以在提交前检查预填单元格并阻止错误提交。参考 Harness `K` 将它改写为审查规则：如果学生要结束任务却没有检查证据，审查 Agent 把“结束”替换成目标 `h` 可执行的 Bash 检查动作；检查输出再进入学生可见轨迹（附录 B）。这说明蒸馏的是目标环境中可重现的检查过程，而非中间件私有动作本身。

作者指出：审查模型较弱时，纠正可能有害；逐步审查提高轨迹采集成本，USPTO 的平均延迟达到 2.4 倍；深层领域知识以及部分上下文管理机制难以靠响应级 SFT 完整内化。USPTO 上蒸馏后 30.0% 虽高于固定 `h` 的 12.0%，仍低于挂载 `h*` 的 38.0%（第 4.3、5 节）。

## 关系

- relates_to → [[concepts/harness|Harness（模型外层执行系统）]]
- relates_to → [[practices/cross-harness-behavior-distillation|跨 Harness 行为蒸馏实践]]
- uses → [[frameworks/deepagents|DeepAgents]]（专用 Harness 的组件抽象）
