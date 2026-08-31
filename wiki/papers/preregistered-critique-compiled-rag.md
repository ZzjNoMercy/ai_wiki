---
title: 预注册证伪实验：编译式 RAG 成本分析
type: research_paper
sources:
  - manual-upload-785681c302/preregistered-critique.md-1785069962628-ca95cdaeee-c0c80fd07f6b.md
created: 2026-07-30
updated: 2026-07-30
schema_version: 0.4.0
---

# 预注册证伪实验：编译式 RAG 成本分析

## 概述

本实验采用**预注册对照设计**（preregistered controlled experiment），对编译式 RAG（[[concepts/compiled-rag|编译式 RAG]]）与向量 RAG 的查询成本进行了系统性对比。

## 实验设计

- **实验组**：编译式 wiki 查询 —— 先编译原始文档为结构化 wiki 页面，查询时读取完整页面
- **对照组**：向量 RAG 查询 —— 标准的 embedding → 检索 → 生成流程
- **测量指标**：单次查询消耗的 token 数量

## 核心结果

| 方法 | 单次查询 token 消耗 |
|------|---------------------|
| 编译式 Wiki 查询 | ~1,650,000 token |
| 向量 RAG 查询 | ~80,000 token |
| **成本比** | **~20:1** |

## 结论

1. **成本主张被证伪**：编译式 RAG 的"摊薄省成本"（amortization saves cost）核心卖点未通过实证检验。即使考虑编译成本的一次性投入可被大量查询摊薄，单次查询 20 倍的 token 差距使得在绝大多数场景下，编译式方案的总成本显著高于向量 RAG。

2. **Token 膨胀是关键瓶颈**：编译产物的结构化格式（完整页面、元数据、互链）导致每次查询需要读入远超原始文档的 token 量，这是成本差距的根本原因。

3. **适用场景受限**：编译式 RAG 可能在以下条件下仍有价值：
   - 查询模式高度重复，缓存命中率极高
   - 对延迟而非成本敏感的场景
   - 需要跨文档关系推理的复杂查询

## 局限与未解决问题

- 实验仅测量 token 消耗，未评估答案质量、召回率或用户满意度
- GBrain 的零 LLM 建图可能降低编译成本，但查询端 token 膨胀问题未解决
- 需要更多独立复现实验验证结论的稳健性

## 与相关工作的关系

本实验直接挑战了（`challenges`）[[concepts/compiled-rag|编译式 RAG]] 的成本假设，并为 [[frameworks/gbrain|GBrain]] 等实现指出了关键优化方向。
