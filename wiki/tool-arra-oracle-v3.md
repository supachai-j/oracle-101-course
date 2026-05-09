---
id: tool:arra-oracle-v3
type: tool
title: arra-oracle-v3 — Oracle's Memory and Brain Repo
status: active
confidence: 0.8
sources:
  - raw/2026-05-09-oracle-ebook-ch02-architecture.md
  - raw/2026-05-09-oracle-ebook-ch04-oracle-brain.md
created: 2026-05-09
updated: 2026-05-09
updated_log:
  - 2026-05-09: created
tiers: semantic
half_life_days: 180
tags: [tool, memory, runtime, mcp, http-api]
---

# arra-oracle-v3 — Oracle's Memory and Brain Repo

## Summary

`arra-oracle-v3` is the memory-layer repo of Oracle — the "brain". It runs both an MCP server (so AI agents can call search, learn, trace, and handoff over stdio) and an HTTP API on default port `47778` (so UIs and other services can query the same data). Internally it combines SQLite + FTS5 for keyword search, LanceDB for vector/semantic search, and the [[concept:psi-folder]] vault for human-readable knowledge files (learnings, retrospectives, inboxes, traces). Its single most important architectural property is that the smart layer (vector) can fall back to the basic layer (FTS) when it fails — degraded Oracle still works.

## Claims

- `arra-oracle-v3` is the memory-layer runtime; the system installation guide says "start with `arra-oracle-v3` because agents and UI must depend on the memory layer". `[src: raw/2026-05-09-oracle-ebook-ch04-oracle-brain.md] {conf: 0.9}`
- It exposes both an MCP server (for agent stdio calls) and an HTTP API (for UIs/services); the default HTTP port is `47778`. `[src: raw/2026-05-09-oracle-ebook-ch02-architecture.md] {conf: 0.9}`
- Storage substrate: SQLite + FTS5 for keyword search via `oracle.db`; LanceDB for vector embeddings; the `ψ` vault for files (handoffs, learnings, retros, traces). `[src: raw/2026-05-09-oracle-ebook-ch02-architecture.md] {conf: 0.9}`
- Capabilities documented in upstream architecture include: MCP server, HTTP API, indexer, vault CLI, search, learn, stats, traces, inbox, forum, feed, and vector map. `[src: raw/2026-05-09-oracle-ebook-ch04-oracle-brain.md] {conf: 0.85}`
- Resilience principle: "if vector search fails the system can fall back to FTS — when the smart part falls down, the basic part must still work". `[src: raw/2026-05-09-oracle-ebook-ch04-oracle-brain.md] {conf: 0.9}`
- Configuration env vars: `ORACLE_DATA_DIR` (data store, e.g. `~/.oracle`), `ORACLE_DB_PATH` (override SQLite path), `ORACLE_REPO_ROOT` (path MCP uses for vault writes), and `ORACLE_PORT` (HTTP API port; default `47778`). `[src: raw/2026-05-09-oracle-ebook-ch02-architecture.md] {conf: 0.9}`

## Relationships

- composes → [[concept:oracle]] `{conf: 0.9}`
- uses → [[concept:psi-folder]] `{conf: 0.9}`
- alternative-to → [[tool:maw-js]] `{conf: 0.4}`

## Open questions

- [ ] Which embedding provider is recommended as default for LanceDB (Ollama is mentioned as an option in install)?

## Changelog

- 2026-05-09 — created
