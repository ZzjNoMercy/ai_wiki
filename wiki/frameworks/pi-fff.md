---
title: "@ff-labs/pi-fff"
type: software_framework
sources:
  - conversation-synthesis-fff-puddingteams-20260828-9bafcf50d8/fff-agent-code-search-practice-b33c55560c-3388215c0fa6.md
created: 2026-08-28
updated: 2026-08-28
schema_version: 0.4.0
---

# @ff-labs/pi-fff

`@ff-labs/pi-fff` 是把 FFF 接入 Pi 的包：通过 Node binding 在进程内调用 FFF，不需要为每次搜索启动一个新的 `rg` 子进程，并在 Session 启动后为工作目录建立后台索引。上游包页面：https://pi.dev/packages/@ff-labs/pi-fff 。

## 安装与使用

- 只有直接使用原生 Pi、不经过 PuddingTeams 时，才按上游说明执行 `pi install npm:@ff-labs/pi-fff`（作为 Pi 全局 Extension）。
- PuddingTeams 已受控集成 FFF 时，不应再手工安装全局 pi-fff，否则会产生两套配置和索引来源。

## 关系

- uses：[[frameworks/fff|FFF]] — 通过 Node binding 在进程内调用 FFF，避免为每次搜索启动新的 rg 子进程。
- applies_to：[[systems/pi-coding-agent|Pi（编码 Agent）]] — 把 FFF 接入 Pi，Session 启动后为工作目录建立后台索引。
