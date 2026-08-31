---
title: A Programming Paradigm for Spatiotemporal Composability
type: research_paper
sources:
  - read-later-4445b9b74d/later_84091bcce34b4bab91a7ad57-f00ab3bff8-26d6d03086bf.md
created: 2026-08-29
updated: 2026-08-29
schema_version: 0.4.0
---

# A Programming Paradigm for Spatiotemporal Composability

- 作者：Yifan Shi, Wei Zhang, Tianyi Cui
- 类型：Preprint，2026
- 标识：https://github.com/cordiverse/paper
- 收录说明：随 [[frameworks/deepseek-harness|DeepSeek Harness（DSH）]] 一起发布的论文，描述“时空可组合性”（spatiotemporal composability）编程范式；[[frameworks/cordis|Cordis]] 插件框架基于该论文实现。

## 核心概念

- 时间可组合性（time composability）：软件还在跑、请求还在进的时候，把里面某个组件换掉（进程不重启）。背景：LLM 来临之前软件基本在编译期定死，加新功能需要打新包并重启；text-to-SQL（2023 年起）在运行时产生全新 SQL 代码并无须重启执行；Generative UI（2025 年底出现）在运行时根据需要产生定制化的 UI 界面。
- 空间可组合性（space composability）：当软件中某一部分在运行时被换掉，软件本身需要知道新的这一部分是什么以及在哪里；因此需要一层抽象出来的接口——`id` 是接口（interface），`name` 是当下实现该能力的包（implementation）。

## 实现

- 实现：[[frameworks/cordis|Cordis]] 插件框架。
- 应用：[[frameworks/deepseek-harness|DeepSeek Harness]] 以此为设计动机，把循环、持久化、压缩等全部做成可运行时替换的插件。
