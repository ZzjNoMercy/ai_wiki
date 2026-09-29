# AI Wiki

[English](README.md) · [中文](README_CN.md)

一个面向大语言模型与 AI Agent 生态的结构化、证据驱动知识库。

## 项目简介

AI Wiki 收录 LLM、AI Agent、模型外层执行系统（Harness）、软件框架、研究论文和工程实践等主题。项目采用“编译式 RAG”思路：先把原始材料整理成按类型组织、可链接、可追溯的 Wiki 页面，再从 Wiki 中查询知识。

这里的内容不依赖模型常识补全。每项事实和关系都应由仓库中的原始快照直接支持，并通过页面 frontmatter 中的 `sources` 保留证据链。

## 特点

- **证据可追溯**：`wiki/` 页面引用 `raw/manifest.jsonl` 中登记的不可变原始快照。
- **按类型组织**：概念、系统、框架、论文、媒体、来源和工程实践分别存放。
- **知识图谱友好**：页面使用带完整目录前缀的 Obsidian wikilink 表达实体关系。
- **事实边界明确**：只记录当前选定 Raw 能够直接支持的事实，不用外部常识填补空白。
- **适合 Agent 工作流**：仓库约定了 Ingest、Query 与 Lint 的操作边界。

## 仓库结构

| 路径 | 内容 |
| --- | --- |
| [`wiki/`](wiki/) | 编译后的知识页面 |
| [`wiki/index.md`](wiki/index.md) | Wiki 总索引与页面摘要 |
| [`wiki/log.md`](wiki/log.md) | 仅追加的摄取日志 |
| [`raw/`](raw/) | 只读原始材料与不可变快照 |
| [`raw/manifest.jsonl`](raw/manifest.jsonl) | Raw 快照清单及完整性信息 |
| [`wiki.schema.json`](wiki.schema.json) | 唯一的页面类型、目录与 frontmatter 约束 |
| [`AGENTS.md`](AGENTS.md) | AI Agent 的操作与证据边界 |

当前知识主要分布在以下目录：

- `wiki/concepts/`：稳定概念与方法论
- `wiki/systems/`：具体 Agent 系统、产品或可运行实现
- `wiki/frameworks/`：软件框架与开发库
- `wiki/practices/`：可复用的工程实践
- `wiki/papers/`：论文、预印本与技术报告
- `wiki/media/`：文章、教程、博客和其他媒体内容
- `wiki/sources/`：稳定的发布渠道与来源
- `wiki/companies/`：公司与组织实体
- `wiki/analysis/`：跨来源分析与评估

## 浏览与查询

从 [`wiki/index.md`](wiki/index.md) 开始浏览，再按需打开相关页面。Wiki 使用 Obsidian wikilink；可以直接在 GitHub 阅读，也可以把仓库作为 Obsidian Vault 打开。

查询时只读取 `wiki/`，不要把 `raw/` 当作查询语料。若 Wiki 信息不足，应明确报告知识缺口，而不是绕过 Wiki 临时从 Raw 推断答案。

## 添加内容

1. 阅读 [`AGENTS.md`](AGENTS.md) 和根目录的 [`wiki.schema.json`](wiki.schema.json)。
2. 选择本次允许使用的 Raw 快照，并核对其在 `raw/manifest.jsonl` 中的精确 `snapshot_path`。
3. 规划长期实体、稳定主题、页面类型和有直接证据支持的关系。
4. 在独立候选区生成页面，审阅固定候选及 Wiki diff 后再发布。
5. 更新索引覆盖情况，并向 `wiki/log.md` 追加一条摄取记录。
6. 发布前检查 frontmatter、页面路径、完整 wikilink、断链和事实归属。

重要约束：

- `raw/` 只读，禁止修改、重命名或删除。
- `wiki/log.md` 只允许追加，禁止改写已有记录。
- 每个新增或更新页面必须引用至少一个本次选中的 Raw 快照。
- `sources` 使用 manifest 中的精确 `snapshot_path`，不要添加 `raw/` 前缀。
- 所有 wikilink 都必须包含类型目录，例如 `[[concepts/compiled-rag]]`。
