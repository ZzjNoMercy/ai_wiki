---
title: 搜索状态与模型会话隔离实践
type: engineering_practice
sources:
  - conversation-synthesis-fff-puddingteams-20260828-9bafcf50d8/fff-agent-code-search-practice-b33c55560c-3388215c0fa6.md
created: 2026-08-28
updated: 2026-08-28
schema_version: 0.4.0
---

# 搜索状态与模型会话隔离实践

## 问题

FFF 的搜索状态、Agent 的模型会话和搜索分页游标不是同一件事。若一个新建房间 Session 能复述上一段对话，很容易被误归因于搜索排序（frecency）。

## 根因

把三种状态混为一谈；或根因是旧 Worker Session Handle 被错误复用，或模型输入被注入了旧 transcript。房间 Session 若只隔离 UI 消息、却继续使用 Window 级“最新 Worker Handle”，就不是真正的会话隔离。

## 方案

三种状态严格分开：

- FFF 索引与排序数据库按 `workspaceId + canonicalPath` 隔离，保存文件索引、frecency 和查询选择历史。
- Worker 模型上下文按 `roomSessionId + workerId` 隔离，由 Worker Session Handle 指向真正的 Agent Session。
- FFF 分页游标受 Workspace 和当前 FFF Session 约束，只表示某次搜索的下一页位置。

正确的房间 Session 语义：同一 Workspace 中新建房间 Session，也必须为每个 Worker 建立新的模型会话；切回旧房间 Session 时，才恢复该房间自己的 Worker Session Handle。

## 验证

- 新建房间 Session 后再次搜索时，Worker 使用新的 Session Handle。
- 切回旧房间 Session 时，才恢复旧 Worker 上下文。

## 失效条件

- FFF history 只影响搜索排序，不保存用户或 assistant 的聊天正文。
- 搜索回答提到“上一轮”，不等于 FFF 泄漏聊天历史：先审计 Worker Session Handle 与 transcript 注入，而不是把问题归因于 frecency。

## 关系

- applies_to：[[systems/puddingteams|PuddingTeams]] — 会话隔离语义按 PuddingTeams 的 Worker 与房间 Session 模型定义。
