---
title: Loop Engineering（循环工程）
type: concept
sources:
  - conversation-synthesis-session-76a1c4236a85-bb4891cbfe/query-152b4c99cc6b-0791a30d01-b37848b8a83a.md
created: 2026-08-23
updated: 2026-08-31
schema_version: 0.4.0
---

# Loop Engineering（循环工程）

Loop Engineering（循环工程）是一个关于如何构建长时程（long-horizon）AI Agent 执行体系的概念：它强调工程化 **Agent 外部的执行、验证、纠错与恢复闭环**，而不是只优化单轮 Prompt。

## 概念要点

核心思想是：模型能力决定 Agent「一轮」能做什么，而 Loop Engineering 要解决的是让多轮持续衔接，支撑数小时甚至数十小时的长时间任务。其执行闭环可概括为：

规划 → 执行 → 验证 → 保存/恢复 → 重复

- 每一步都有明确边界（下一步做什么）
- 在真实电脑环境中验证（而不是模拟环境）
- 只保存通过验收的进度（可信进展）
- 失败或上下文刷新后带着证据继续

## 各文献定义

本页用于汇总不同文献、项目对 Loop Engineering 的定义。当前收录 LongHorizon-Harness（AMAP-ML）项目的定义，后续可继续补充其他文献。

### LongHorizon-Harness（AMAP-ML）

**来源**：GitHub 仓库 [AMAP-ML/LongHorizon-Harness](https://github.com/AMAP-ML/LongHorizon-Harness)，中文说明 `README.zh-CN.md`。

**定义原文**：

> 这就是 Loop Engineering：工程化 Agent 外部的执行、验证、纠错与恢复闭环，而不只是优化单轮 Prompt。

**配套解释**（README 引言）：

> 模型决定 Agent 一轮能做什么。LongHorizon-Harness 负责工程化模型外部的执行闭环：下一步做什么、如何在真实电脑中验证、哪些进度可以保存，以及在失败或上下文刷新后如何继续。

**要点解读**：该项目将 Loop Engineering 定义为围绕 Agent 外部搭建「规划 → 执行 → 验证 → 保存/恢复 → 重复」的完整闭环，而非单轮 Prompt 优化。项目口号为「操作整台计算机、保存可信进展、持续工作直到任务真正完成」。

### 其他文献（待补充）

- （待补充）

## 关系

- implemented_by → [[frameworks/long-horizon-harness|LongHorizon-Harness]]（该项目的 README 给出了本概念的上述定义）
- relates_to → [[concepts/harness|Harness]]（Loop Engineering 描述 Harness 在长任务中的外部执行、验证、纠错与恢复闭环）
