---
title: Running a Software Factory Efficiently at Uber Scale
type: media
sources:
  - read-later-4445b9b74d/later_81b795f2b793454db6ae5691-a15e695237-16d194673310.md
created: 2026-09-02
updated: 2026-09-02
schema_version: 0.4.0
---

# Running a Software Factory Efficiently at Uber Scale

## 来源信息

- 原始署名：Uday Kiran Medisetty，Distinguished Engineer
- 发布平台：Uber
- 发布日期：2026-08-27
- 原文：https://www.uber.com/us/en/blog/efficient-software-factory/

## 核心内容

文章介绍 Uber 如何把覆盖软件开发生命周期的 Agent 能力作为一座 Software Factory 经营。其重点不是单次会话能否完成任务，而是如何让不同执行形态在统一的成本、质量、模型和工具治理下规模化运行。

Uber 报告，超过 70% 的 Pull Request 归因于本地或云端 Agent；工程师构建了 3,600 多个 Agent Skill，每日执行超过 30,000 次。2026 年 2 月至 8 月，所有 Agent 产品的周活用户增长 7 倍、周请求量增长 9.4 倍，而总 AI 支出自 4 月起相对稳定。在固定同一模型以隔离优化效果后，2 月至 7 月每千次请求成本较峰值下降近 34%，每 Session 成本较 6 月峰值下降 52%。

## 架构视角

文章把 Agent 使用形态分成四层：Raw Sessions、Sessions with Skills、General Agents、Specialized Agents。越靠近 Specialized Agents，任务边界越窄，Uber 对运行环境、模型选择、质量和成本的控制越强；从交互式个人会话逐步转向云端托管并以工作结果计量。

这套 Factory 横跨 Code Gen、Validate、Deploy、Observe、Maintain。Specialized Agents 的例子包括 Minion（意图到 PR）、uReview（PR 到代码审查）、Agentic XP（XP 完成到 readout）、Conan AI（告警到 AI RCA）和 Fawkes（定时触发维护）。

## Harness 视角

Uber 的关键 Harness 决策包括：统一不同交互式 Harness 的安装、配置、认证与成本观测；在 400K Token 触发自动压缩；主交互线程与短生命周期 Sub-Agent 采用不同 Prompt Cache TTL；将 1,000 多个 MCP Server 收口到统一 Gateway；用 Tool Search 与 CLI 动态解析工具，避免预载全部 Schema；用 Code Mode 把轮询和批处理留在子进程，只把结果摘要送回模型；以 AI Context Graph 为 Agent 提供跨系统上下文。

文章给出的规模化治理结论是：应把成本从 Token 单价继续映射到完成任务的单位经济性，并用真实任务 Benchmark 在成本、质量和可靠性之间选择 Pareto 最优模型。

## 边界

文章明确说明，具体成本降幅取决于 Uber 的代码库、团队规模和 Agent 工作负载，不应直接外推；可迁移的是以真实工作构建 Benchmark、拆解成本方程并同时优化准确率与成本的方法。

## 关系

- sourced_from → [[sources/uber-blog|Uber Blog]]
- introduces → [[systems/uber-software-factory|Uber Software Factory]]
- introduces → [[practices/agent-workload-productization-ladder|Agent 工作负载产品化阶梯]]
- introduces → [[practices/outcome-based-agent-economics|面向结果的 Agent 单位经济性]]
- introduces → [[practices/benchmark-driven-agent-model-routing|基于真实任务 Benchmark 的 Agent 模型路由]]
- discusses → [[concepts/harness|Harness（模型外层执行系统）]]

