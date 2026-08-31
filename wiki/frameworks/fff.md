---
title: FFF
type: software_framework
sources:
  - conversation-synthesis-fff-puddingteams-20260828-9bafcf50d8/fff-agent-code-search-practice-b33c55560c-3388215c0fa6.md
created: 2026-08-28
updated: 2026-08-28
schema_version: 0.4.0
---

# FFF

FFF 是 Rust 原生、SIMD 加速的文件查找与内容搜索库。面向编码 Agent 在大型 Workspace 中定位文件和符号的场景，在普通扫描之上提供预索引、排序与分页能力。

## 核心能力

- 预索引：搜索主要查询已经建立的索引，不必每次重新遍历整个目录。
- Frecency 排序：综合文件的访问频率与最近使用时间，把常用文件提前。
- Git 感知：修改、暂存和未跟踪文件获得更高优先级。
- 查询历史：记录查询与被选文件之间的排序信号，让后续相似搜索更容易命中正确位置。
- 模糊文件查找：可以按概念、路径片段和近似名称定位文件。
- 多关键词搜索：一次调用搜索多个字面量。
- 游标分页：首屏结果保持有界，需要更多结果时沿 cursor 继续。

## 对 Agent 的价值

假设仓库有 33 行 TODO，其中 30 行在一个近期频繁修改但与当前任务无关的噪声文件，另外 3 行在真正相关的实现文件：无排序的全量返回会同时浪费搜索输出 token，并增加关键文件被截断的概率。FFF 先按 Git 状态和 frecency 对文件排序，再返回有限首屏并提供分页游标。

它的收益不是“搜索不消耗 token”，而是把 token 用在更可能影响结论的文件上。价值在大仓库、重复探索、热点文件明显、TODO/FIXME 噪声多、或需要同时定位多个关联符号时最明显；小仓库中的一次精确搜索，内置 grep/find 往往已经足够。

FFF 是更好的检索入口，不是穷举证明工具；不替代文件读取、代码理解、测试和验收。

## 集成

- [[frameworks/pi-fff|@ff-labs/pi-fff]] 把 FFF 接入 Pi，通过 Node binding 在进程内调用。
- [[systems/puddingteams|PuddingTeams]] 将 FFF 作为受控 Harness 能力装配，过滤可能绕过平台边界的全局 pi-fff 实例。
