---
title: Multi-Agent Token 成本优化实践
type: engineering_practice
sources:
  - knowledge-file-4be80edb57/knowledge-imported-20260825-10-multi-agent-50-.md-c353f4a9b1-941251b63b09.md
  - conversation-correction-codex-multi-agent-token-20260830-1c071d3fe3/multi-agent-token-wiki-scope-v2-c875e42ad4-adddccc75aa9.md
created: 2026-08-30
updated: 2026-08-30
schema_version: 0.4.0
---

# Multi-Agent Token 成本优化实践

## 问题

Multi-Agent 系统把需求分析、编码、审查、测试和视觉验证分派给多个 Agent 后，系统提示词、工具 Schema、工具结果、代码和文档、长期记忆以及不断增长的历史消息会在多轮调用中重复进入模型。系统可以完成任务，但成本随 Agent 数量和轮次快速增长，而且若缺少按 Wave、Trace 或 Session 的度量，很难判断 Token 花在哪里。

## 根因

- 上下文默认一次性、全量加载，条件性规则和暂时无关的资料长期占位。
- 最长生命周期的主 Agent 直接接收 MCP 原始 payload，后续每轮重复携带。
- 全能型 Agent 暴露所有工具 Schema，并把前端、后端、测试和视觉历史混在同一会话。
- 代码盲搜、重复读取 Skill 和高噪声 CLI 输出制造无效 Token。
- 无依赖工具调用被串行化，导致相同历史被多打包数轮。
- 动态状态混入稳定指令前缀，降低 Prompt Cache 命中。

## 方案

整体遵循三条原则：只加载当前需要的上下文、减少无关上下文、减少重复上下文。

### 只加载当前需要的上下文

1. 对 Skill 使用渐进式披露：正文保留职责和分流骨架，模板、条件规则与阶段细节移入 `references/`，使用时再读取。
2. 把数据库迁移、编译、启动、健康检查和测试执行等确定性操作放进脚本或 CLI；大模型负责理解和生成，脚本负责可重跑的精确执行。文章以 Playwright CLI 替换逐步浏览器 MCP 操作为例。
3. 将 TAPD、Figma 等大型 MCP 数据获取交给短生命周期专用 Agent，主 Agent 只接收结构化摘要。
4. 长期记忆先读轻量 `INDEX.md`，按标题、标签和摘要筛选 Top 结果，再读取少量正文。

### 减少无关上下文

5. 先按 S/M/L 预判规模：小需求使用单 Agent；中大型需求才拆成短生命周期角色 Agent。为每个角色设置工具白名单和模型层级，避免所有 MCP Schema 对所有 Agent 可见。
6. 用 [[frameworks/graphify|Graphify]] 等代码图谱先通过文件索引与依赖关系缩小范围，再读取少量代码文件，避免关键词盲搜反复带入无关匹配。

### 减少重复上下文

7. 把稳定指令放在前、动态内容后置；将进度状态外化到文件，维持稳定前缀并便于中断恢复。
8. 上游已经加载并写入技术方案的信息由文档向下游传递，子 Agent 不再重复加载同一 Skill 或长期记忆。
9. 使用 [[frameworks/rtk|rtk]] 等确定性过滤器，在 CLI 输出进入模型上下文前压缩噪声。
10. 无数据依赖的工具调用和测试批次并行执行，减少因串行调用产生的历史重复打包轮次。

## 验证

- 主 Agent 端到端 Token：708,783（17 轮）降至 315,266（9 轮），文中报告降幅 55.5%，轮次下降 47%。
- MCP 数据获取子 Agent 化后，文中同一工作流的单轮输入从 1,030,000 降至 634,905，下降 38.4%。
- 代码图谱对比中，总 Token 从 875,352 降至 676,987，下降 22.7%；主要来自输入 Token 减少。
- 测试与视觉 Agent 使用较低成本模型后，文中报告该部分成本下降 64%。
- rtk 是确定性文本过滤，可使用 `rtk gain` 或同一命令的原始/压缩输出直接对比；不同命令的收益差异很大。
- 文章基于分项实测区间反推，中型需求完整流程预计降低约 50%–65%；作者同时说明仍需补齐严格的端到端 A/B 复核。

## 适用范围

- 包含多个角色、多个 Wave 和大量工具调用的中大型 Agent 任务。
- 主 Agent 生命周期长、MCP payload 大、代码仓库探索轮次多的开发流程。
- 能将确定性步骤脚本化，并能为 Agent 配置独立工具集合和模型的 Harness。

## 失效条件与边界

- 小任务强行拆分会增加多份系统提示词成本，拆分收益可能覆盖不了固定开销。
- 各项优化数据来自不同局部实验，不能简单相加为严格的端到端因果结论。
- 模型执行路径具有随机性，不宜用各跑一次的完整工作流直接评估确定性过滤器。
- CLI 压缩率依命令而异，文中示例从 `git status` 的约 31% 到 `ps aux` 的约 98.9%，不能把 60%–90% 当作固定收益。
- Hook 协议或字段名不兼容可能静默失效；文中 rtk 的 `updatedInput` 与 CodeBuddy 所需 `modifiedInput` 需要转换。
- 索引、摘要和代码图谱可能遗漏信息；它们用于缩小候选范围，不能替代代码阅读、测试和最终验收。

## 关系

- applies_to：[[concepts/multi-agent|Multi-Agent（多智能体）]]。
- uses：[[frameworks/graphify|Graphify]]、[[frameworks/rtk|rtk]]。
- derived_from：[[media/multi-agent-token-cost-optimization|靠这10个优化点，我们把Multi-Agent工作流成本降了50%以上]]。
