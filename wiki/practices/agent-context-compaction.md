---
title: Agent 上下文 Compact 工程实践
type: engineering_practice
sources:
  - manual-upload-785681c302/opencode-agent-compact-engineering-practice.md-1785921032328-20d84d7559-8130ff6bba3a.md
created: 2026-08-05
updated: 2026-08-31
schema_version: 0.4.0
---

# Agent 上下文 Compact 工程实践

面向长时运行 Agent 的上下文压缩（Compact）工程实践：结构化滚动摘要、完整历史与模型投影分离、最近上下文保留、事务式检查点、溢出恢复和工具协议边界。本实践适用于（applies_to）[[systems/opencode|OpenCode]]，OpenCode 是当前记录的最完整实现。

## 问题

- 长时运行的编码 Agent 和研究 Agent 上下文持续增长，把完整历史持续发送给模型成本高、容易溢出；
- 自由形式的“总结对话”没有输出契约：格式漂移、难以验证，容易遗漏“正在做什么”和“接下来做什么”；
- 已完成工作与待执行动作不分，后续 Agent 可能重复执行旧任务；
- 半完成的压缩若被采用，可能破坏整个会话。

## 根因

- 上下文无上限增长，而模型可见窗口有限；
- 摘要没有固定输出 Schema，无法保证目标、状态、下一步等关键字段存在；
- 存储（审计、展示、恢复）与模型可见上下文未分离，共用同一物理表示时互相污染；
- 压缩结果未事务化提交，失败也可能替换上下文边界；
- 会切换模型或 Provider 时，旧的 reasoning/tool 协议消息无法可靠复用。

## 方案

1. **固定输出 Schema**：按固定章节和顺序输出（Objective、Important Details、Work State 的 Completed/Active/Blocked、Next Move、Relevant Files），所有章节即使无内容也必须保留；使用简短列表；保留精确文件路径、符号、命令、错误文本、URL 和标识符。
2. **滚动 anchored summary**：首次压缩创建摘要；后续压缩把 previous summary 作为显式输入，执行“保留仍成立的事实、删除已失效内容、合并新事实”，而不是每次从零自由总结。
3. **专用隐藏 compaction agent**：隔离的内部模型任务（隐藏、禁止工具调用），只总结历史，不回答原对话、不继续执行原任务；使用与原对话相同的语言；不得提及压缩过程。
4. **确定性消息序列化**：把角色、推理、工具调用、工具结果等转换为稳定文本标签（如 `[User]:`、`[Assistant tool call]: tool(args)`、`[Tool result]:`），媒体转为 `[Attached mime: name]` 占位，工具结果/Shell 输出设截断上限并标记 `[truncated]`，与 Provider 消息协议解耦。
5. **摘要与 recent 分离**：旧上下文并入滚动摘要，近期原始上下文按 Token 预算（而非固定消息条数）原样保留，必要时允许在一条序列化消息内部拆分。
6. **事务式检查点**：发布 Started/Ended 两类事件；只有模型调用成功、无 Provider error、摘要非空时才发布 Ended，失败压缩不替换当前上下文边界。
7. **Context Epoch 边界**：完成检查点后进入新的 Context Epoch，Provider 原生 reasoning、tool message 或签名数据不跨边界复用，降低跨 Provider 或加密推理消息不兼容的风险。
8. **溢出恢复**：Provider 返回 context overflow 时可触发一次恢复性 Compact。

## 验证

该实践由 OpenCode 实现（implements，见 [[systems/opencode|OpenCode]]），并有对应测试与设计约束证据：

- 固定章节和媒体占位符测试：`packages/core/test/session-compaction.test.ts`；
- 重复 Compact、recent tail、媒体剥离和预算边界测试：`packages/opencode/test/session/compaction.test.ts`；
- durable checkpoint、Context Epoch 和 overflow recovery 设计约束：`specs/v2/session.md`。

测试文件及其覆盖范围来自本次 Raw 的源码证据索引（分析 commit：`c387fe190bbd22e9396d264effe242d157f866d2`）。

## 适用范围

本实践 applies_to [[concepts/harness|Harness]] 的上下文与状态管理职责。

- 长时运行的编码 Agent 和研究 Agent；
- 工具调用多、单次 ToolMessage 较大的 Agent；
- 需要在多次压缩后继续保持任务状态的会话；
- 需要审计完整历史、但又不能把完整历史持续发送给模型的平台；
- 会切换模型或 Provider、不能可靠复用旧 reasoning/tool 协议消息的系统。

## 失效条件与边界

- 固定 Schema 只能降低遗漏概率，不能保证摘要事实绝对正确；关键状态仍应结构化持久化。
- Recent 如果按字符或任意消息下标截断，可能破坏用户轮次或工具调用协议；文本 recent 允许消息内拆分适合文本检查点，但不能直接当作 Provider 原生 ToolMessage 列表恢复。
- 工具输出统一截到 2,000 字符可能丢失领域关键内容；平台需要为大型结果提供独立可寻址产物或按工具定制压缩策略。
- 只保留摘要、不保留完整历史，会失去审计与纠错能力，不属于该实践的完整实现。
- 把 Compact 摘要作为普通用户指令注入而没有来源标记，可能被后续 Agent 当成新任务执行。
