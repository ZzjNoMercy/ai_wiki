# AI Wiki

[English](README.md) · [中文](README_CN.md)

A structured, evidence-backed knowledge base for large language models and the AI agent ecosystem.

## Overview

AI Wiki is a structured knowledge base covering LLMs, AI agents, model harnesses, software frameworks, research papers, and engineering practices. It follows a compiled-RAG approach: source material is first compiled into typed, linked, and traceable Wiki pages, and queries are then answered from that compiled knowledge layer.

The project does not use model memory to fill factual gaps. Every fact and relationship should be directly supported by an authorized source snapshot, with provenance preserved through each page's `sources` frontmatter.

## Highlights

- **Traceable evidence**: pages under `wiki/` reference immutable snapshots registered in `raw/manifest.jsonl`.
- **Typed organization**: concepts, systems, frameworks, papers, media, sources, and engineering practices live in separate directories.
- **Knowledge-graph friendly**: relationships use Obsidian wikilinks with complete type-directory prefixes.
- **Explicit evidence boundaries**: only facts directly supported by the selected Raw material are recorded.
- **Agent-ready workflow**: the repository defines separate operating boundaries for Ingest, Query, and Lint tasks.

## Repository layout

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

## Browsing and querying

Start with [`wiki/index.md`](wiki/index.md), then open the relevant pages as needed. The Wiki uses Obsidian wikilinks and can be read directly on GitHub or opened locally as an Obsidian Vault.

Queries should read from `wiki/`, not from `raw/`. If the compiled Wiki does not contain enough information, report the knowledge gap instead of bypassing the Wiki and inferring an answer from Raw material.

## Adding knowledge

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
