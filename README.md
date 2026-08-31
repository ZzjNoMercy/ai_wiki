# AI Wiki

[English](#english) · [中文](#中文说明)

A structured, evidence-backed knowledge base for large language models and the AI agent ecosystem.

一个面向大语言模型与 AI Agent 生态的结构化、证据驱动知识库。

## English

### Overview

AI Wiki is a structured knowledge base covering LLMs, AI agents, model harnesses, software frameworks, research papers, and engineering practices. It follows a compiled-RAG approach: source material is first compiled into typed, linked, and traceable Wiki pages, and queries are then answered from that compiled knowledge layer.

The project does not use model memory to fill factual gaps. Every fact and relationship should be directly supported by an authorized source snapshot, with provenance preserved through each page's `sources` frontmatter.

### Highlights

- **Traceable evidence**: pages under `wiki/` reference immutable snapshots registered in `raw/manifest.jsonl`.
- **Typed organization**: concepts, systems, frameworks, papers, media, sources, and engineering practices live in separate directories.
- **Knowledge-graph friendly**: relationships use Obsidian wikilinks with complete type-directory prefixes.
- **Explicit evidence boundaries**: only facts directly supported by the selected Raw material are recorded.
- **Agent-ready workflow**: the repository defines separate operating boundaries for Ingest, Query, and Lint tasks.

### Repository layout

| Path | Purpose |
| --- | --- |
| [`wiki/`](wiki/) | Compiled knowledge pages |
| [`wiki/index.md`](wiki/index.md) | Main index and page summaries |
| [`wiki/log.md`](wiki/log.md) | Append-only ingestion log |
| [`raw/`](raw/) | Read-only source material and immutable snapshots |
| [`raw/manifest.jsonl`](raw/manifest.jsonl) | Snapshot inventory and integrity metadata |
| [`schema/`](schema/) | Page-type, relationship, and frontmatter constraints |
| [`AGENTS.md`](AGENTS.md) | Operating contract and classification rules for AI agents |

The current knowledge base is primarily organized into:

- `wiki/concepts/`: stable concepts and methodologies
- `wiki/systems/`: concrete agent systems, products, and runnable implementations
- `wiki/frameworks/`: software frameworks and development libraries
- `wiki/practices/`: reusable engineering practices
- `wiki/papers/`: papers, preprints, and technical reports
- `wiki/media/`: articles, tutorials, blogs, and other media
- `wiki/sources/`: recurring publishers and source channels
- `wiki/companies/`: companies and organizations
- `wiki/analysis/`: cross-source analysis and evaluations

### Browsing and querying

Start with [`wiki/index.md`](wiki/index.md), then open the relevant pages as needed. The Wiki uses Obsidian wikilinks and can be read directly on GitHub or opened locally as an Obsidian Vault.

Queries should read from `wiki/`, not from `raw/`. If the compiled Wiki does not contain enough information, report the knowledge gap instead of bypassing the Wiki and inferring an answer from Raw material.

### Adding knowledge

1. Read [`AGENTS.md`](AGENTS.md) and the active [`schema/brain.schema.yaml`](schema/brain.schema.yaml).
2. Select the authorized Raw snapshots and resolve their exact `snapshot_path` values in `raw/manifest.jsonl`.
3. Plan long-lived entities, stable topics, page types, and relationships directly supported by evidence.
4. Generate or update Wiki pages through the PuddingClaw staging and publishing workflow.
5. Update index coverage and append one ingestion entry to `wiki/log.md`.
6. Before publishing, validate frontmatter, page paths, complete wikilinks, broken links, and factual attribution.

Key constraints:

- Treat `raw/` as read-only; never modify, rename, or delete its contents.
- `wiki/log.md` is append-only.
- Every new or updated page must cite at least one Raw snapshot selected for that ingestion.
- Use the exact manifest `snapshot_path` in `sources`; do not prefix it with `raw/`.
- Every wikilink must include its type directory, for example `[[concepts/compiled-rag]]`.
- `.puddingclaw/` and `.obsidian/` contain local runtime or editor state and are not versioned.

## 中文说明

### 项目简介

AI Wiki 收录 LLM、AI Agent、模型外层执行系统（Harness）、软件框架、研究论文和工程实践等主题。项目采用“编译式 RAG”思路：先把原始材料整理成按类型组织、可链接、可追溯的 Wiki 页面，再从 Wiki 中查询知识。

这里的内容不依赖模型常识补全。每项事实和关系都应由仓库中的原始快照直接支持，并通过页面 frontmatter 中的 `sources` 保留证据链。

### 特点

- **证据可追溯**：`wiki/` 页面引用 `raw/manifest.jsonl` 中登记的不可变原始快照。
- **按类型组织**：概念、系统、框架、论文、媒体、来源和工程实践分别存放。
- **知识图谱友好**：页面使用带完整目录前缀的 Obsidian wikilink 表达实体关系。
- **事实边界明确**：只记录当前选定 Raw 能够直接支持的事实，不用外部常识填补空白。
- **适合 Agent 工作流**：仓库约定了 Ingest、Query 与 Lint 的操作边界。

### 仓库结构

| 路径 | 内容 |
| --- | --- |
| [`wiki/`](wiki/) | 编译后的知识页面 |
| [`wiki/index.md`](wiki/index.md) | Wiki 总索引与页面摘要 |
| [`wiki/log.md`](wiki/log.md) | 仅追加的摄取日志 |
| [`raw/`](raw/) | 只读原始材料与不可变快照 |
| [`raw/manifest.jsonl`](raw/manifest.jsonl) | Raw 快照清单及完整性信息 |
| [`schema/`](schema/) | 页面类型、关系类型与 frontmatter 约束 |
| [`AGENTS.md`](AGENTS.md) | AI Agent 的操作契约与分类规则 |

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

### 浏览与查询

从 [`wiki/index.md`](wiki/index.md) 开始浏览，再按需打开相关页面。Wiki 使用 Obsidian wikilink；可以直接在 GitHub 阅读，也可以把仓库作为 Obsidian Vault 打开。

查询时只读取 `wiki/`，不要把 `raw/` 当作查询语料。若 Wiki 信息不足，应明确报告知识缺口，而不是绕过 Wiki 临时从 Raw 推断答案。

### 添加内容

1. 阅读 [`AGENTS.md`](AGENTS.md) 和当前 [`schema/brain.schema.yaml`](schema/brain.schema.yaml)。
2. 选择本次允许使用的 Raw 快照，并核对其在 `raw/manifest.jsonl` 中的精确 `snapshot_path`。
3. 规划长期实体、稳定主题、页面类型和有直接证据支持的关系。
4. 通过 PuddingClaw 的 staging/publish 流程生成或更新 Wiki 页面。
5. 更新索引覆盖情况，并向 `wiki/log.md` 追加一条摄取记录。
6. 发布前检查 frontmatter、页面路径、完整 wikilink、断链和事实归属。

重要约束：

- `raw/` 只读，禁止修改、重命名或删除。
- `wiki/log.md` 只允许追加，禁止改写已有记录。
- 每个新增或更新页面必须引用至少一个本次选中的 Raw 快照。
- `sources` 使用 manifest 中的精确 `snapshot_path`，不要添加 `raw/` 前缀。
- 所有 wikilink 都必须包含类型目录，例如 `[[concepts/compiled-rag]]`。
- `.puddingclaw/` 和 `.obsidian/` 是本机运行或编辑器状态，不纳入版本控制。

---

Repository description: **A structured, evidence-backed wiki for LLMs, AI agents, models, frameworks, and engineering practices.**
