---
title: Cordis
type: software_framework
sources:
  - read-later-4445b9b74d/later_84091bcce34b4bab91a7ad57-f00ab3bff8-26d6d03086bf.md
created: 2026-08-29
updated: 2026-08-29
schema_version: 0.4.0
---

# Cordis

Cordis 是 [[frameworks/deepseek-harness|DeepSeek Harness（DSH）]] 的底层插件框架：DSH 的 `*.cordis.yml` 官方约定名即源于它（文中“底层框架叫 Cordis”）。

## 事实要点

- 实现论文 [[papers/spatiotemporal-composability|A Programming Paradigm for Spatiotemporal Composability]] 描述的“时空可组合性”范式（文中“以及基于该论文的 Cordis 插件框架”“它的实现 Cordis”）。
- 插件形态：一个普通的 npm 包，入口是 Cordis 插件的 `apply(ctx)` 函数（文中称“有一点像 React 的写法”）。
- 以 DSH 工具包为例的 API：`ctx.tools.register(defineTool(...))`、`ctx.on('tools/pre-execute', ...)`、`ctx.on('tools/post-execute', ...)`、`ctx.on('agent/pre-step', ...)`。
- GitHub：https://github.com/cordiverse/cordis
- 已知边界：本次 raw 仅支持以上事实；Cordis 独立于 DSH 的能力细节不在本次授权范围内。
