---
title: GBrain
type: software_framework
sources:
  - manual-upload-785681c302/gbrain-implementation.md-1785069962628-3f55e791d8-7bfc6d0a9aa3.md
created: 2026-07-30
updated: 2026-07-30
schema_version: 0.4.0
---

# GBrain

GBrain 是编译式 RAG（[[concepts/compiled-rag|编译式 RAG]]）的开源生产级实现，将"先编译后查询"的理念落成了包含完整子系统的工程框架。

## 架构

GBrain 由三个核心子系统组成：

| 子系统 | 职责 |
|--------|------|
| **Storage**（存储） | 管理原始文档与编译产物的持久化 |
| **Graph**（图谱） | 维护页面间的结构化链接关系 |
| **Retrieval**（检索） | 在编译产物上执行查询，返回相关页面 |

## 关键特性

### Ingest → Query 闭环

GBrain 实现了完整的摄取到查询生命周期：
1. **Ingest**：读入原始文档，编译为结构化 wiki 页面
2. **Query**：直接读取已编译页面，无需实时向量检索

### 零 LLM 自动建图

GBrain 的核心创新之一是**零 LLM 自动建图**（zero-LLM auto-graph construction）：在不调用大语言模型的情况下，通过确定性规则自动抽取页面间的 wikilink 关系并构建知识图谱。这显著降低了编译阶段的计算成本与延迟。

## 与编译式 RAG 的关系

GBrain 是编译式 RAG 概念的第一个（已知）开源生产实现，将 Karpathy 的 LLM Wiki 构想从理念推进到可运行的工程系统。其零 LLM 建图特性直接回应了编译阶段成本过高的顾虑，但查询阶段的 token 膨胀问题仍需进一步工程优化（参见 [[papers/preregistered-critique-compiled-rag|预注册证伪实验]] 中的成本数据）。
