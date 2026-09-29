---
title: How Warp builds self-improving agents on Claude
type: media
sources:
  - knowledge-file-4be80edb57/knowledge-imported-20260830-how-warp-builds-self-improving-agents-on-claude---claude-by--d0eb9dd10a-d24d9bd248b7.md
created: 2026-09-01
updated: 2026-09-01
schema_version: 0.4.0
---

# How Warp builds self-improving agents on Claude

- 标题：How Warp builds self-improving agents on Claude
- 原始署名：Michael Segner
- 发布平台：Claude（claude.com/blog），见 [[sources/claude-blog|Claude Blog]]。
- 原文 URL：https://claude.com/blog/how-warp-builds-self-improving-agents-on-claude
- 发布时间：2026-08-26；分类：Agents；产品：Claude Platform。
- 本页 sourced_from [[sources/claude-blog|Claude Blog]]。

## 内容概述

Warp 面对的问题是：代码审查 Agent 会产生帮助不大的评论和低质量输出；人工反复改 prompt 不可扩展，而且一次会话里的反馈会随会话结束而消失。文章提出把反馈转化为可复用 Agent Skill 更新，使后续运行继承改进。

核心模式由两层 Skill 和人工反馈组成：

1. 内层（base/inner）Skill 保存领域知识与任务指令，负责执行实际任务。
2. 人类对结果给出带原因的明确反馈；相比单纯点赞或点踩，原因能提供可执行的改进信号。
3. 外层（improver）Skill 定期汇总反馈，对比 Agent 建议与人类回应，并提出对内层 Skill 的小而聚焦的修改。
4. 修改作为普通代码变更进入 PR 和 code review；人类批准合并后，下一次运行继承新版 Skill。

文章以开源仓库的 issue triage Agent 为例：当 Agent 未识别出某个 issue 已“ready to spec”时，维护者留下明确原因；在 Oz 中运行的外层 improver 据此提出最小 Skill 修改，并通过 PR 接受审核。Warp 还把这一模式用于 spec-writing、code review 与 triage Agent。

## 工程边界

- Skill 应记录原则及原因，而不是不断堆叠僵硬规则；保持体量小，并使用渐进式披露。
- 反馈可能错误，因此需要合理性检查、贡献者过滤和人类审核。
- 可验证领域应配置 verification harness 或参考语料；不易验证的领域可使用确定性评估、golden outputs 或专家反馈。
- Skill 是跨运行、稳定且有意修改的程序性知识；memory 通常在推理期间自动写入并持续变化，两者不应混为一谈。

## 关系

- introduces → [[practices/skill-based-agent-self-improvement-loop|基于 Skill 的 Agent 自我改进闭环]]
- discusses → [[concepts/agent-self-improvement|Agent Self-Improvement]]
