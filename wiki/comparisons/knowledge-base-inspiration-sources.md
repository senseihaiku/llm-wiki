---
title: "Knowledge Base Inspiration Sources"
type: comparison
tags: [knowledge-base, rag, second-brain, memory, inspiration]
sources: [cerebras-knowledge-base-blog, basic-memory-docs, honcho-docs, hindsight-docs, graphlit-docs, graphify-repo, indydevdan-videos, nick-saraev-video]
last_updated: 2026-07-21
---

# Knowledge Base Inspiration Sources

Side-by-side of the knowledge-base/memory systems explored in the July 2026 sessions. Each section lists only what is **unique** to that source — solutions none of the others have. The [[Kepano]] vault philosophy is deliberately out of scope (different problem: note structure, not knowledge retrieval).

## Cerebras Knowledge

Blog: [How We Built Our Knowledge Base](https://www.cerebras.ai/blog/how-we-built-our-knowledge-base) (2026-07-15). 15k+ queries/day three months after launch.

- **One shared embeddings table** — every source lands in a single Postgres/pgvector table (3,072 dims) with a common schema; connectors are Python plugins added via PR.
- **Distillation before embedding** — LLM extracts `question / summary / resolution / systems / code_refs` per Slack thread; raw transcript never embedded.
- **Bursting** — same-author message runs embedded separately, gated by IDF ≥ 4.0, ≥ 200 chars, or reactions.
- **Four-signal retrieval** (full-text, vector, IDF, age decay) fused with RRF (k=60) → reranker → context re-expansion.
- **Dumb MCP primitives** — LLM-free retrieval tools; the client agent orchestrates.

## Honcho (Plastic Labs)

[docs](https://honcho.dev/docs) · [repo](https://github.com/plastic-labs/honcho) — 6.1k stars, AGPL-3.0, hosted or self-host.

- **Theory-of-mind representations** — memory keyed by *observer/observed pairs*: Bob's model of Alice is built only from what Bob witnessed. No other system models perspectives.
- **Dialectic endpoint** — ask natural-language questions *about a peer*; an agent reasons over derived conclusions and answers. "Memory as an agent you interrogate", not top-k retrieval.
- **Dreaming** — autonomous consolidation (fires after ≥50 new conclusions + cooldown + user inactivity): deduction resolves contradictions, induction finds patterns needing ≥2 data points.
- **Peers are symmetric** — humans and AI agents both get memory/identity, enabling multi-agent group memory.

## Hindsight (Vectorize)

[docs](https://hindsight.vectorize.io/) · [repo](https://github.com/vectorize-io/hindsight) — 18.7k stars, MIT, [LongMemEval SOTA 91.4%](https://venturebeat.com/data/with-91-accuracy-open-source-hindsight-agentic-memory-provides-20-20-vision), [arXiv paper](https://arxiv.org/pdf/2512.12818).

- **Four typed memory networks** — world facts / experience facts (the agent's own actions) / consolidated observations / mental models — consulted cheapest-distilled-first. Separates "a source said X" from "I concluded X".
- **Confidence-scored belief revision** — opinions carry confidence that updates as evidence arrives; consolidation preserves source history.
- **TEMPR retrieval** — semantic + BM25 + graph traversal + temporal filtering in parallel, RRF-fused, cross-encoder reranked, sub-100ms. Temporal questions went ~32% → ~80%.
- **Mission / Directives / Disposition** — natural-language identity and hard rules that shape how reflect interprets memory.

## Graphlit

[docs](https://docs.graphlit.dev) — SaaS only. **Warning: new signups closed as of mid-2026** (founder pivoted to Dossium). Design reference, not a dependency.

- **Feeds** — 30+ managed connectors with OAuth, recurring polls, incremental sync, dedup, delete-mirroring. The scheduled-ingest layer everyone else hand-rolls.
- **Observables** — an entity *plus its observation history across content* (where a person appears, with which orgs, when). Temporality attached to entities, not chunks.
- **Multimodal ingestion as first-class** — audio with speaker diarization, video, OCR — all normalized into one Schema.org/JSON-LD graph.
- **60+ MCP tools** including publishing (TTS, markdown-with-citations export).

## Graphify

[repo](https://github.com/safishamsi/graphify) — 93k stars. llm-wiki already bridges to it (`llmwiki/graphify_bridge.py`, default engine for `llmwiki graph`).

- **No vector store** — tree-sitter AST parsing + graph traversal instead of embeddings; deterministic, LLM-free.
- **Every edge explained** — connections tagged `EXTRACTED` (explicit in source) vs `INFERRED` (resolved by the tool).
- **Leiden community detection** with LLM-free labels.

## Basic Memory

[docs](https://docs.basicmemory.com) · [original repo](https://github.com/basicmachines-co/basic-memory) — 3.5k stars, AGPL-3.0, v0.22.1, pushed 2026-07-21 (active). Cloud $15/mo.

- **Typed semantic markdown** — observations `- [category] fact #tags (context)` and relations `- verb [[Note]]` make prose queryable as a graph with no database; each observation indexed as its own chunk.
- **Picoschema schemas** with `schema_infer` / `schema_validate` / `schema_diff` (drift detection).
- **Local embeddings by default** — FastEmbed bge-small (384-dim), zero API cost, hybrid text+vector fusion, content-hashed incremental re-embedding.
- **memory:// URLs + build_context** — graph traversal from any permalink with depth/timeframe/wildcards; cross-project routing (`project::note-path`).
- **metadata_filters** — Mongo-style operators (`$in`, `$between`, dot-notation) over YAML frontmatter.

## The two llm-wiki upstreams (forks are stale)

- **[Pratiyush/llm-wiki](https://github.com/Pratiyush/llm-wiki)** (347 stars, v1.3.82) — upstream of this repo; local fork ~1 month behind. Unique: 4-factor **confidence scoring with a 5-state lifecycle machine**, 16 lint rules, 12-tool MCP server, Ollama synthesis backend, multi-CLI ingest (Claude Code, Codex, Cursor, Gemini, Copilot).
- **[nashsu/llm_wiki](https://github.com/nashsu/llm_wiki)** (≈15k stars, v0.6.5 released 2026-07-20) — the Tauri desktop app. Unique: **4-signal relevance scoring** (direct links, source overlap, Adamic-Adar, type affinity) + Louvain communities, Rust agent backend, optional LanceDB vectors, Obsidian-vault compatibility, web-clipper, local HTTP API + MCP so external agents can query the wiki.

## IndyDevDan (method, not tooling)

[Loop-engineering rebuttal](https://www.youtube.com/watch?v=VQy50fuxI34) (2026-07-13) · [Fusion harness](https://www.youtube.com/watch?v=AQl5Q-0l7FQ) (2026-07-20).

- **AI Developer Workflows / software factory** — "loop engineering is a terrible rebrand of the SDLC"; instead: code + agents + engineers as three actors, code being the cheapest and most reliable.
- **Two constraints**: planning (prompting) and reviewing (validation) — engineers "always show up at the ends"; `/auto-validate` writes the validation gate *before* the build runs.
- **Fusion harness** — two frontier models in parallel; `/fusion` reports **consensus / divergence / discarded**. Divergence is where the value lives — a merge policy for conflicting claims.
- **"Whoever owns your agent harness owns your results"** — template your expertise into agents and skills you own.

## Synthesis

Three layers keep recurring across everything that works:

1. **Substrate you own** — markdown + git (Basic Memory, llm-wiki, kepano). Human-auditable, survives every vendor.
2. **Derived machine layer** — distillation, embeddings, typed facts, confidence (Cerebras, Hindsight, Honcho). Rebuildable from the substrate, never source-of-truth.
3. **Harness** — the workflows and MCP tools that connect the two (IndyDevDan's ADWs, Cerebras' dumb primitives, Basic Memory's memory:// traversal).

Every failure mode is a layer violation: forcing all knowledge into one platform (single-source-of-truth dream), embedding raw text without distillation, or renting the substrate from a SaaS that can close signups (Graphlit).

## Connections

- [[CerebrasKnowledge]] — enterprise reference architecture
- [[BasicMemory]] — semantic markdown + schema layer
- [[Honcho]] — person/agent modeling
- [[Hindsight]] — belief revision + temporal retrieval
- [[Karpathy llm-wiki gist]] — the spec both wiki projects implement
