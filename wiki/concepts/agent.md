---
title: Agent（智能体）
type: concept
sources:
  - read-later-4445b9b74d/later_abadf6c614f140da9b1d1fb5-0fce80db03-bcc916448a53.md
  - read-later-4445b9b74d/later_84091bcce34b4bab91a7ad57-f00ab3bff8-26d6d03086bf.md
  - web-capture-cs329z-stanford-20c64e618d/cs329z-engineering-ai-agents-course-homepage-20260914-ddc1edf11e-50d0e0774ed8.md
created: 2026-09-10
updated: 2026-09-14
schema_version: 0.4.0
---

# Agent（智能体）

Agent 是由模型与其外层执行系统共同构成、能够在上下文中规划并通过工具采取行动的运行系统。模型提供核心智能；[[concepts/harness|Harness]] 围绕模型编排执行，管理上下文与持久状态，连接工具和现实环境，并承担约束、验证与纠正。

## 两种互补的组成公式

### 能力组成视角

在 [[media/harness-engineering-for-self-improvement|《Harness Engineering for Self-Improvement》]] 中，Lilian Weng 回顾早期 Agent 框架时将其概括为：

```text
Agent = LLM + memory + tools + planning + action
```

这个公式回答“Agent 需要哪些能力”：LLM 负责生成与推理，memory 保存可复用信息，tools 扩展可执行能力，planning 组织步骤，action 让系统作用于外部环境。

### 系统架构视角

[[media/deepseek-of-deepseek-harness|Life Odyssey 的 DeepSeek Harness 分析文章]]对 2026 年 Agent 架构的概括是：

```text
Agent = Model + Harness
```

这个公式回答“这些能力由谁承载”。其中 Model 是核心智能；Harness 是模型与现实环境之间的执行层，组织上下文、工具、行动与运行过程。生产环境中的 Harness 还需要加入工作流设计、评价、权限控制、持久状态、验证与纠正。

两种公式并不冲突，可以近似映射为：

| 早期能力项 | 在 Model + Harness 架构中的主要位置 |
|---|---|
| LLM | Model |
| memory | Harness 的上下文管理与持久状态 |
| tools、action | Harness 的工具接口与执行管线；Model 负责提出调用 |
| planning | Model 的规划能力与 Harness 的工作流编排共同实现 |
| constraints、evaluation、correction | 生产级 Harness 增加的系统职责 |

因此，Agent 不等于单独的模型。模型可以生成计划或工具调用，但由 Harness 决定模型看到什么、工具如何执行、状态如何保存，以及结果是否足以判定任务完成。

## 与相邻概念的边界

- **Harness**：[[concepts/harness|Harness]] 是 Agent 中围绕模型的执行系统，不是 Agent 的同义词。
- **Agent Loop**：Agent Loop 是模型生成、工具执行、结果回填并再次调用模型的迭代核心；生产级 Harness 的范围还包括权限、持久化、验证、纠正与恢复。
- **Loop Engineering**：[[concepts/loop-engineering|Loop Engineering]] 用外部执行和验收条件工程化长任务闭环，不等同于由模型自己判断何时结束的 Agent Loop。
- **Multi-Agent**：[[concepts/multi-agent|Multi-Agent]] 是多个 Agent 共同参与任务或系统的组织形式；每个参与者仍可按 Model + Harness 分析。
- **Agent Self-Improvement**：[[concepts/agent-self-improvement|Agent Self-Improvement]] 描述 Agent 根据任务结果、评估或反馈修改可被后续运行复用的组成部分；可修改对象可以位于 Model，也可以位于 Harness。

## 相关课程

- [[media/cs329z-engineering-ai-agents|CS 329Z: Engineering AI Agents]]：Stanford 2026 年秋季的 Agent 工程课程，教学范围从 LLM pipeline、compound AI system 到 autonomous agent，覆盖核心组件的从零构建、框架抽象、数据、优化、评测、安全以及课程项目。

## 工程含义

在短任务中，模型能力常常决定单轮表现；任务持续时间增加、工具增多或副作用风险上升后，Agent 的可靠性越来越取决于 Harness 如何管理状态、权限、验证和失败恢复。评价一个 Agent 时，需要同时观察 Model 与 Harness，不能只用基础模型能力替代对完整系统的评价。

## 关系

- derived_from → [[media/harness-engineering-for-self-improvement|Harness Engineering for Self-Improvement]]
- derived_from → [[media/deepseek-of-deepseek-harness|The deepseek of DeepSeek Harness]]
- relates_to → [[concepts/harness|Harness（模型外层执行系统）]]
- relates_to → [[concepts/loop-engineering|Loop Engineering（循环工程）]]
- relates_to → [[concepts/multi-agent|Multi-Agent（多智能体）]]
- relates_to → [[concepts/agent-self-improvement|Agent Self-Improvement]]
