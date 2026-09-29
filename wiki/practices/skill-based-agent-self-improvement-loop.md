---
title: 基于 Skill 的 Agent 自我改进闭环
type: engineering_practice
sources:
  - knowledge-file-4be80edb57/knowledge-imported-20260830-how-warp-builds-self-improving-agents-on-claude---claude-by--d0eb9dd10a-d24d9bd248b7.md
created: 2026-09-01
updated: 2026-09-01
schema_version: 0.4.0
---

# 基于 Skill 的 Agent 自我改进闭环

## 问题

Agent 会反复产生无帮助的评论或低质量输出。人工逐次重写 prompt 难以扩展，而只存在于单次会话中的纠正无法被后续运行继承。

## 根因

- 任务执行与反馈积累没有形成持久闭环。
- 点赞或点踩只表达结果好坏，没有解释原因，难以转化为具体修改。
- 若直接自动吸收所有反馈，错误或冲突意见也会被固化。
- 把所有规则塞进一个不断膨胀的 Skill，会增加上下文负担并产生互相冲突的指令。

## 方案

1. 建立内层（base/inner）Skill，保存完成任务所需的领域知识、原则和指令。
2. 在 Agent 产物旁提供低摩擦反馈入口，要求人类说明“为什么好或不好”。
3. 保存反馈以及它对应的 Agent 建议、任务结果与上下文。
4. 定期运行外层（improver）Skill，比较 Agent 输出与人类回应，提出对内层 Skill 的小而聚焦的候选修改。
5. 将 Skill 视为普通文件和代码资产：候选通过 PR、code review 和人工批准后合并。
6. 让下一次 Agent 运行加载合并后的 Skill，从而完成跨运行继承。

这一闭环 implements [[concepts/agent-self-improvement|Agent Self-Improvement]]；其原始案例与细节 derived_from [[media/how-warp-builds-self-improving-agents-on-claude|How Warp builds self-improving agents on Claude]]。

## 验证

- 对可验证任务，配套 verification harness 或参考语料，检查修改前后的任务结果。
- 对不易自动验证的任务，使用确定性评估、golden outputs 或领域专家反馈。
- 保留 PR diff 与审核记录，使每次 Skill 变化可追踪、可拒绝、可回滚。
- 同时观察全局指标，例如从 issue 到 merge 的时间、参与贡献者数量与运行成本，避免只优化局部评论质量。
- Warp 的 issue triage 案例以“未识别 ready to spec”为明确失败信号，外层 improver 提交最小 Skill 修改，随后由人类审核合并。

## 适用范围

- 任务跨运行重复出现，且领域知识或评价标准可写入 Skill。
- 有稳定的反馈渠道，或能构造任务级验证器与参考样例。
- 团队需要让改进经过现有代码审查与版本管理流程，而不是直接在线修改生产行为。

## 失效条件与边界

- 反馈量太少、质量低或贡献者不可信时，改进器缺少可靠信号；需要合理性检查和贡献者过滤。
- 只写规则而不解释原则与原因，容易过拟合个例。
- Skill 过大时应拆分并渐进式加载，否则会浪费上下文并降低一致性。
- 修改跨度过大、没有评测或绕过人工审核时，无法判断退化来源，也难以安全回滚。
- 该实践依赖人类门控，不应仅凭“外层 Skill 修改内层 Skill”就归为严格的 [[concepts/recursive-self-improvement|递归自我改进（RSI）]]。
