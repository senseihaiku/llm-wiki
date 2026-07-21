---
title: "Knowledge Base Inspiration Sources"
type: comparison
tags: [knowledge-base, rag, second-brain, inspiration]
sources: [cerebras-knowledge-base-blog, basic-memory-docs, nick-saraev-video]
last_updated: 2026-07-21
---

# Knowledge Base Inspiration Sources

Side-by-side of the knowledge-base/memory systems explored in the July 2026 sessions. Each section lists only what is **unique** to that source — solutions none of the others have. The [[Kepano]] vault philosophy is deliberately out of scope here (different problem: note structure, not knowledge retrieval).

## Cerebras Knowledge

Blog: [How We Built Our Knowledge Base](https://www.cerebras.ai/blog/how-we-built-our-knowledge-base) (2026-07-15, Isaac Tai, Daniel Kim, Mike Gao). 15k+ queries/day three months after launch.

Unique solutions:

- **One shared embeddings table** — every source (Slack, code, wiki, custom DBs) lands in a single Postgres/pgvector table (3,072 dims) with a common schema; new connectors are Python plugins added via pull request.
- **Distillation before embedding** — an LLM extracts `question / summary / resolution / systems / code_refs` from each Slack thread; the raw transcript is never embedded. Accuracy rose significantly from normalization.
- **Bursting** — consecutive same-author messages embedded separately with the thread topic prepended, gated by IDF ≥ 4.0, ≥ 200 chars, or emoji reactions (social signal).
- **Four-signal hybrid retrieval** — full-text, vector, IDF, and age decay fused with reciprocal rank fusion (k=60), then a small reranker, then context re-expansion around the winners.
- **Dumb MCP primitives** — retrieval tools (`search_slack`, `search_code`, `who_knows`) are deliberately LLM-free; the client agent orchestrates.

## Basic Memory

Docs: [docs.basicmemory.com](https://docs.basicmemory.com). Plain markdown you own, exposed to any AI client over MCP.

Unique solutions:

- **Typed semantic markdown** — observations `- [category] fact #tags (context)` and relations `- implements [[Note]]` make prose queryable as a graph with no database.
- **Picoschema schema system** — per-note-type schemas with `schema_infer` (discover patterns from existing notes), `schema_validate` (conformance), `schema_diff` (drift detection). Warn-mode by default, strict once patterns stabilize.

## llm-wiki (this framework)

The [Karpathy-gist](https://gist.github.com/karpathy/442a6bf555914893e9891c11519de94f) framework, Python CLI, v1.3.x. The repo whose skills (`/wiki-sync`, `/wiki-lint`, `/wiki-graph`, `maintainer`, `self-learn`) are wired into Claude Code.

Unique solutions:

- **Sessions as corpus** — Claude Code `.jsonl` transcripts are the source of truth; the immutable `raw/` layer is enforced at runtime with a quarantine file on conflict.
- **No silent overwrites** — contradictions stay visible under `## Contradictions` with both claims preserved.
- **`_context.md` folder scoping** — a cheap per-folder description lets deep queries skip irrelevant subtrees.

## llm_wiki (Tauri desktop app)

Separate project with a near-identical name — a fork of a desktop GUI implementing the same Karpathy gist, v0.5.x. Not a bug, just an unfortunate name collision.

Unique solutions:

- **Ingest queue with auto-pause** on LLM usage limits, plus batch wiki-check actions.
- **Capture surface** — browser extension and LAN-access mode for the local server.

## Nick Saraev video

[Cerebras Killed Notion, Obsidian, and Your "Second Brain"](https://www.youtube.com/watch?v=eCx3SSCcISo).

Unique solutions:

- **Blog-post-as-prompt replication** — paste the entire Cerebras post into Claude Code as the spec and let it build the pipeline for a small business (Slack, Gmail, GitHub, YouTube connectors).
- **With/without eval** — same 20 questions asked with and without the knowledge base: 17/20 correct vs 0/20.

## Synthesis

The common thread across everything that works: **meet data where it lives and normalize at ingest**. Every failure mode is one of two things — forcing all knowledge into a single platform, or embedding raw text naively without distillation.

## Connections

- [[CerebrasKnowledge]] — enterprise-scale reference architecture
- [[BasicMemory]] — semantic markdown + schema layer
- [[Karpathy llm-wiki gist]] — the spec both wiki projects implement
