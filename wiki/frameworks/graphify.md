---
title: Graphify
type: software_framework
sources:
  - knowledge-file-4be80edb57/knowledge-imported-20260825-10-multi-agent-50-.md-c353f4a9b1-941251b63b09.md
  - conversation-correction-codex-multi-agent-token-20260830-1c071d3fe3/multi-agent-token-wiki-scope-v2-c875e42ad4-adddccc75aa9.md
  - conversation-correction-codex-multi-agent-token-20260830-1c071d3fe3/multi-agent-token-wiki-scope-cecfa617a3-ed1742b2e57f.md
created: 2026-08-30
updated: 2026-08-30
schema_version: 0.4.0
---

# Graphify

Graphify 是文章所使用的代码图谱工具。它通过 AST 和语义为代码仓库建立文件索引与依赖关系，使 Agent 在读取代码前先查询图谱、缩小候选文件范围，再读取少量目标文件。

## 使用方式

- 初始化仓库图谱后会生成 Graphify 目录；文章称初始化可能需要几分钟。
- `graphify hook install` 可绑定 `post-commit` 与 `post-checkout` Git Hook：提交后增量更新图谱，切换分支后切换对应图谱。
- 文中给出的项目地址：https://github.com/Graphify-Labs/graphify

## 对 Agent 搜索的作用

传统关键词盲搜可能先返回多个文件的匹配行，再经历多轮搜索与整文件读取。Graphify 将依赖关系作为搜索前置索引，用一次查询缩小文件范围，目标是减少无关匹配进入 Context 和降低探索轮次。这一用法 relates_to [[practices/agent-search-minimal-context|Agent 搜索最小上下文实践]]，也是 [[practices/multi-agent-token-cost-optimization|Multi-Agent Token 成本优化实践]] 中“减少无关上下文”的具体实现。

## 文中验证与边界

相同任务对比中，使用代码图谱时总 Token 为 676,987，未使用时为 875,352，文中报告下降 22.7%；输入 Token 下降 22.8%，输出 Token 只下降 1.5%。该结果来自文中的特定工作流，不代表所有仓库和任务都保持同一幅度。

Raw 中安装示例写作 `uv tool install graphifyy`，与项目名及 GitHub 仓库名 Graphify 不一致；本页不擅自修正该命令，实际安装方式仍需上游资料确认。

## 关系

- relates_to：[[practices/agent-search-minimal-context|Agent 搜索最小上下文实践]]。
- implements：[[practices/multi-agent-token-cost-optimization|Multi-Agent Token 成本优化实践]]中的代码搜索优化方向。
- derived_from：[[media/multi-agent-token-cost-optimization|靠这10个优化点，我们把Multi-Agent工作流成本降了50%以上]]。
