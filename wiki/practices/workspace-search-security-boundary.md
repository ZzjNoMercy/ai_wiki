---
title: Workspace 搜索安全边界实践
type: engineering_practice
sources:
  - conversation-synthesis-fff-puddingteams-20260828-9bafcf50d8/fff-agent-code-search-practice-b33c55560c-3388215c0fa6.md
created: 2026-08-28
updated: 2026-08-28
schema_version: 0.4.0
---

# Workspace 搜索安全边界实践

## 问题

未受控的搜索能力可能绕过平台边界：全局 `pi-fff` 实例、绝对路径/`~/`/`../` 扫描、root 或 home 扫描、通过符号链接跳出 Workspace、cursor 跨范围复用，以及未登记或未信任的 Workspace 被当作搜索根。

## 根因

搜索边界缺少以 Workspace 为信任单元的受控装配；全局 Extension 或普通附件会隐式扩大搜索范围。

## 方案

受控 FFF 应当：

- 把搜索根固定为已登记且可信的 canonical Workspace。
- 按 Workspace 使用独立索引目录和排序数据库。
- 禁止绝对路径、`~/`、`../`、root 或 home 扫描。
- 给 cursor 增加 Workspace/Session 作用域，拒绝跨范围复用。
- 在调用 FFF 前检查 path 和文件约束的现存路径前缀，拒绝通过 Workspace 内符号链接跳到外部目录。
- Workspace 未登记、未信任或 canonical path 漂移时 fail closed。

用户把 Workspace 外的单个绝对文件路径粘到聊天窗口时，平台应先把文件冻结为当前 Session 的附件，再让 Agent 读取冻结副本；该文件不进入 FFF Workspace 索引。外部目录不能作为普通附件隐式扩大搜索边界，必须登记为 Workspace，或配置为明确且有生命周期的临时挂载范围。

## 验证

- Workspace 内指向外部目录的符号链接作为 path 或 constraint 时被拒绝。
- 命中文件全部位于当前 Workspace。

## 失效条件

- PuddingTeams 已受控集成 FFF 时，不应再手工安装全局 pi-fff，否则会产生两套配置和索引来源。
- Workspace 未登记、未信任或 canonical path 漂移时 fail closed，而不是降级放行。

## 关系

- applies_to：[[systems/puddingteams|PuddingTeams]] — 受控 FFF 装配的安全边界要求。
- applies_to：[[frameworks/fff|FFF]] — 对受控 FFF 实例的 Workspace 安全约束。
