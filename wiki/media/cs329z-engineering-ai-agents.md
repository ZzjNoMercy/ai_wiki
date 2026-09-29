---
title: "CS 329Z: Engineering AI Agents"
type: media
sources:
  - web-capture-cs329z-stanford-20c64e618d/cs329z-engineering-ai-agents-course-homepage-20260914-ddc1edf11e-50d0e0774ed8.md
created: 2026-09-14
updated: 2026-09-14
schema_version: 0.4.0
---

# CS 329Z: Engineering AI Agents

CS 329Z 是 Stanford 2026 年秋季课程，主题是 Agentic Systems 工程。课程从简单 LLM pipeline、compound AI system 延伸到 autonomous agent，强调问题分解、组件选择、数据收集与整理、评测，以及实际系统设计中的权衡。

## 课程信息

- 课程主页：https://cs329z.stanford.edu/
- 学期：Stanford / Fall 2026
- 授课教师：Diyi Yang、Michael Ryan、John Yang
- 上课时间：每周一、周三 13:30–14:50
- 首次课程：2026 年 9 月 23 日
- 课程表注明安排可能调整，讲义将在发布后陆续链接到主页。

## 教学内容

课程先让学生从头构建 RAG、工具使用与 Agent Loop 等核心组件，再讨论 DSPy、LangChain/LangGraph、LlamaIndex 等框架如何抽象这些模式。后续主题包括 Agent 设计模式与 Scaffold、记忆、多 Agent、优化、Agent 数据、评测、LLM-as-Judge、安全与 Guardrail、Coding Agent、Proactive Agent，以及长运行系统的可靠性与可观测性。

## 实践安排

- HW1 要求不使用 Agent 框架，仅通过 Chat Completion 调用和自行编写的代码，从企业邮件检索与推理流程逐步构建包含工具、终端、记忆和 Human-in-the-loop 的 Agentic Harness。
- HW2 要求为预构建 Agent 设计评测套件，包括代码评分器、至少一个 LLM-as-Judge、基于 request、environment、stopping criteria、scorer 四元组的 Benchmark 任务与错误分析。
- 学期项目以“Making Life at Stanford Better with Agents”为主题，要求小组构建改善 Stanford 校园生活某一方面的 Agentic System。

## 定位

这是一门覆盖 Agent 构建、优化和评测完整工程链路的教学课程；当前页面首先作为课程主页与后续讲义入口保存。

## 关系

- discusses → [[concepts/agent|Agent（智能体）]]
