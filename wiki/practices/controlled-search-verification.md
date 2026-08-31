---
title: 受控搜索集成验证实践
type: engineering_practice
sources:
  - conversation-synthesis-fff-puddingteams-20260828-9bafcf50d8/fff-agent-code-search-practice-b33c55560c-3388215c0fa6.md
created: 2026-08-28
updated: 2026-08-28
schema_version: 0.4.0
---

# 受控搜索集成验证实践

## 问题

只看开发服务器终端日志判断工具是否被调用并不可靠：工具调用与结果属于 Session 事件，默认不一定逐条打印到标准输出。

## 根因

把“开发服务器打印日志”当作工具调用的证据；没有以执行记录、Worker Session Handle 和实际返回结果为准。

## 方案

准备两个或三个只存在于当前 Workspace 的关键词，让 Agent 只调用一次 `multi_grep`，并要求报告实际工具名和命中文件。验收检查：

1. 执行记录中的实际工具名是 `multi_grep`。
2. 命中文件全部位于当前 Workspace。
3. 首屏结果有界，更多结果通过 cursor 表达。
4. 新建房间 Session 后再次搜索时，Worker 使用新的 Session Handle。
5. 切回旧房间 Session 时，才恢复旧 Worker 上下文。
6. Workspace 内指向外部目录的符号链接作为 path 或 constraint 时被拒绝。

## 验证

以执行时间线、Worker Session Handle 和实际返回结果为准，而不是开发服务器终端日志。

## 失效条件

- 不能根据首屏宣称“仓库里没有其他命中”：应增加精确 path/constraints 或沿 cursor 继续。
- 要证明整个仓库绝无某模式，需要精确约束、完整翻页，必要时使用确定性审计命令。

## 关系

- applies_to：[[systems/puddingteams|PuddingTeams]] — 对 PuddingTeams 受控搜索集成的验收方法。
