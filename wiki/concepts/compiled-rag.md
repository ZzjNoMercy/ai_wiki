---
title: 编译式 RAG
type: concept
sources:
  - manual-upload-785681c302/karpathy-llm-wiki.md-1785069962627-3a2cdec832-f3ca050e6cc2.md
  - manual-upload-785681c302/gbrain-implementation.md-1785069962628-3f55e791d8-7bfc6d0a9aa3.md
  - manual-upload-785681c302/preregistered-critique.md-1785069962628-ca95cdaeee-c0c80fd07f6b.md
created: 2026-07-30
updated: 2026-07-30
schema_version: 0.4.0
---

# 编译式 RAG

编译式 RAG（Compiled RAG）是一种检索增强生成范式，其核心主张是：**先将原始资料一次性编译成结构化知识库（wiki），查询时直接读取编译产物，而非每次实时检索原始文档**。

## 核心理念

编译式 RAG 由 Andrej Karpathy 在 LLM Wiki 概念中提出。其关键洞察是将知识整理视为"复利资产"（compound asset）：一次编译、长期复用。编译阶段将原始文档转化为结构化、可互链的 wiki 页面；查询阶段直接读取已编译页面，无需实时 embedding 计算或向量检索。支持者认为，随着查询次数增加，前期编译成本被摊薄，长期总成本低于传统向量 RAG。

## 工程实现

编译式 RAG 的开源生产实现是 [[frameworks/gbrain|GBrain]]，它将该概念落成了包含 Storage（存储）、Graph（图谱）和 Retrieval（检索）三个子系统的完整工程方案。GBrain 实现了从摄取（ingest）到查询（query）的完整闭环，并支持零 LLM 自动建图——即在不调用大语言模型的情况下自动抽取页面间的链接关系。

## 成本争议

一项预注册对照实验对编译式 RAG 的成本主张提出了挑战，详见 [[papers/preregistered-critique-compiled-rag|预注册证伪实验]]。实测数据显示：

- 编译式 wiki 单次查询消耗约 **165 万 token**
- 向量 RAG 单次查询仅消耗约 **8 万 token**
- 编译式查询成本约为向量 RAG 的 **20 倍**

该实验的核心结论是：编译式 RAG 的"摊薄省成本"卖点被证伪——编译产物的 token 膨胀效应显著，即使大量复用也难以抵消单次查询的额外开销。

## 关系视角

- [[frameworks/gbrain|GBrain]] 实现了（`implements`）编译式 RAG 的工程系统
- [[papers/preregistered-critique-compiled-rag|预注册证伪实验]] 挑战了（`challenges`）编译式 RAG 的成本主张
- 编译式 RAG 概念的源头（`sourced_from`）是 Karpathy 的 LLM Wiki 构想

## 待解决问题

- 编译产物的 token 效率优化是否有工程手段可以显著改进？
- 在查询模式高度重复的特定场景中，摊薄效应是否仍能成立？
- 零 LLM 自动建图的质量能否达到人工整理的水平？
