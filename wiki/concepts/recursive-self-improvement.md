---
title: 递归自我改进（Recursive Self-Improvement, RSI）
type: concept
sources:
  - read-later-4445b9b74d/later_abadf6c614f140da9b1d1fb5-0fce80db03-bcc916448a53.md
created: 2026-08-29
updated: 2026-08-31
schema_version: 0.4.0
---

# 递归自我改进（Recursive Self-Improvement, RSI）

## 定义与历史

- RSI 概念可追溯至 I. J. Good（1965）的"超智能机器"（ultraintelligent machine）：一个能在所有智力活动中超越人类、并能设计出更好的机器来改进自身的系统。
- Yudkowsky（2008）用"recursive self-improvement"指代一个特定反馈回路：AI 用当前智能来改进产生其智能的认知机制（cognitive machinery）。

## 现代反馈回路

- 现代 AI 中的该反馈回路可能意味着模型直接改写自身权重，或更广义地：模型改进**训练管线（training pipeline）**与**部署系统（deployment system）**，从而产生在经济上有价值任务上表现更优的后继模型。
- 原文称前沿实验室（Anthropic、OpenAI）的 AI 研究发展速度已显著加快（引用两家机构相关表述）。

## 近期可行路径预测（Lilian Weng）

- 原文预测：近期 RSI 不太可能从模型直接改写自身权重开始。
  1. Harness engineering 将朝**元方法论（meta-methodology）**方向演化——改进的是"获得更好答案的机制"，而不只是答案本身；harness 系统本身成为优化目标，启发式规则更少、通用机制更多。
  2. 成熟的 harness 使模型自我改进循环的自动研究（auto-research）成为可能，而更聪明的模型又能防止 harness 过度工程化，保持系统可持续。
- 最终，许多 harness 改进可能被**内化（internalized）**进核心模型行为，但与外部上下文和工具的接口应保留。类比：提示工程中手工 prompt 技巧随指令微调与模型推理提升而不再核心，但"指定目标、约束、上下文与评估"的需求没有消失。

## 相关

- 本文主题即 harness engineering 如何贡献于 RSI（relates_to [[concepts/harness|Harness]]）；论述来源见 [[media/harness-engineering-for-self-improvement|Harness Engineering for Self-Improvement]]。
