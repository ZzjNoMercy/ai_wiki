---
title: PuddingTeams
type: system
sources:
  - conversation-synthesis-fff-puddingteams-20260828-9bafcf50d8/fff-agent-code-search-practice-b33c55560c-3388215c0fa6.md
created: 2026-08-28
updated: 2026-08-31
schema_version: 0.4.0
---

# PuddingTeams

PuddingTeams 将 FFF 作为受控 Harness 能力装配，而不是要求用户把 `pi-fff` 手工安装为 Pi 全局 Extension。受控装配用于保证配置、Workspace 和索引边界一致，并过滤可能绕过平台边界的全局 `pi-fff` 实例。

## 配置分层

- Harness 提供所有 Pi Worker 的全局默认：`builtin` 或 `fff`。
- 单个 Pi Worker 可选择 `inherit`、`builtin` 或 `fff`；其中 `inherit` 继承 Harness 的全局 Worker 默认。
- Manager 独立配置 `off`、`builtin` 或 `fff`，默认关闭。Manager 搜索只在 Solo 模式生效；Direct 是纯 Worker 通道，Group 使用协作 relay。

## 工具命名

使用 override 模式时，Agent 看到的仍是标准工具名 `grep`、`find` 和 `multi_grep`，不需要学习 `ffgrep` 或 `fffind` 等第二套名字。

## 会话与安全语义

- Worker 模型上下文按 `roomSessionId + workerId` 隔离，由 Worker Session Handle 指向真正的 Agent Session。
- 同一 Workspace 中新建房间 Session，也必须为每个 Worker 建立新的模型会话；切回旧房间 Session 时，才恢复该房间自己的 Worker Session Handle。房间 Session 若只隔离 UI 消息、却继续使用 Window 级“最新 Worker Handle”，就不是真正的会话隔离。
- 受控 FFF 将搜索根固定为已登记且可信的 canonical Workspace，按 Workspace 使用独立索引目录与排序数据库，禁止绝对路径/`~/`/`../`/root/home 扫描，cursor 带 Workspace/Session 作用域，拒绝符号链接跳出，Workspace 未登记、未信任或 canonical path 漂移时 fail closed。
- 用户把 Workspace 外的单个绝对文件路径粘到聊天窗口时，平台先把文件冻结为当前 Session 的附件，再让 Agent 读取冻结副本；该文件不进入 FFF Workspace 索引。外部目录必须登记为 Workspace，或配置为明确且有生命周期的临时挂载范围。

## 关系

- uses：[[frameworks/fff|FFF]] — 将 FFF 作为受控 Harness 能力装配。
- relates_to：[[systems/pi-coding-agent|Pi（编码 Agent）]] — PuddingTeams 的 Harness 为所有 Pi Worker 提供全局搜索默认。
- relates_to：[[concepts/harness|Harness]] — 以平台统一配置、Workspace 隔离和 fail-closed 规则约束搜索能力。
