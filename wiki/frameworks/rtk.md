---
title: rtk
type: software_framework
sources:
  - knowledge-file-4be80edb57/knowledge-imported-20260825-10-multi-agent-50-.md-c353f4a9b1-941251b63b09.md
  - conversation-correction-codex-multi-agent-token-20260830-1c071d3fe3/multi-agent-token-wiki-scope-v2-c875e42ad4-adddccc75aa9.md
  - conversation-correction-codex-multi-agent-token-20260830-1c071d3fe3/multi-agent-token-wiki-scope-cecfa617a3-ed1742b2e57f.md
created: 2026-08-30
updated: 2026-08-30
schema_version: 0.4.0
---

# rtk

rtk 是一个开源 CLI 代理，在命令执行前进行拦截，把 `git status`、`npm test`、`docker ps` 等命令的原始输出重写为压缩版本，以减少进入 Agent Context 的噪声和重复 Token。

- 项目地址：https://github.com/rtk-ai/rtk
- 文中引用的官方实测范围为 60%–90%，但作者强调不同命令差异显著，不能视为固定降幅。

## 集成方式

文章在 CodeBuddy 环境中通过 `PreToolUse` Hook 实现等价拦截，因为 rtk 官方 `--agent` 不支持 CodeBuddy。集成时还需要处理字段协议差异：rtk 输出 `updatedInput`，CodeBuddy 要求 `modifiedInput`；字段不匹配时可能不报错但不生效，文中使用转换脚本适配。

全局配置后，子 Agent 可以共同获得输出压缩效果。它压缩的是 CLI 文本，不负责改变模型的任务决策。

## 验证

作者不建议只用“同一需求完整运行两次、分别开关 rtk”的方式评估，因为模型探索和重试路径存在波动，噪声可能大于过滤器收益。更可靠的验证方法是使用 `rtk gain`，或者直接比较同一命令的原始输出与压缩输出；文本过滤是确定性的，可重复验证。

文中示例显示，`ps aux` 的压缩约为 98.9%，纯 `git status` 约为 31%。适用收益取决于具体命令输出结构。

## 关系

- implements：[[practices/multi-agent-token-cost-optimization|Multi-Agent Token 成本优化实践]]中的 CLI 输出压缩方向。
- derived_from：[[media/multi-agent-token-cost-optimization|靠这10个优化点，我们把Multi-Agent工作流成本降了50%以上]]。
