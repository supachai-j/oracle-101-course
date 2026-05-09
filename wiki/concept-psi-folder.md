---
id: concept:psi-folder
type: concept
title: ψ (psi) folder — Oracle's Tangible Memory
status: active
confidence: 0.8
sources:
  - raw/2026-05-09-oracle-ebook-ch01-what-is-oracle.md
  - raw/2026-05-09-oracle-ebook-ch02-architecture.md
  - raw/2026-05-09-oracle-ebook-ch04-oracle-brain.md
created: 2026-05-09
updated: 2026-05-09
updated_log:
  - 2026-05-09: created
tiers: semantic
half_life_days: 180
tags: [memory, vault, files]
---

# ψ (psi) folder — Oracle's Tangible Memory

## Summary

ψ ("psi") is the Greek letter from *psyche* (mind), chosen by Ajarn Nat as the symbol for Oracle's memory system. The `ψ/` folder is the brain of each individual Oracle — a file-based vault that stores learnings, retrospectives, handoffs, traces, and resonance as plain files that both humans and agents can read. This is a deliberate design choice: knowledge should be readable, portable, and survive longer than a single session, not locked inside a black box. The structure is also the durable backing for [[concept:retrospective]], [[feature:awaken]], and the wider [[tool:arra-oracle-v3]] memory layer.

## Claims

- ψ is taken from the Greek letter for *psyche* (mind); Ajarn Nat chose it as the symbol of Oracle's memory system, and the `ψ/` folder is the brain of each Oracle, storing memories, lessons, and handoffs as human-readable files. `[src: raw/2026-05-09-oracle-ebook-ch01-what-is-oracle.md] {conf: 0.85}`
- The default `ψ/` layout includes `inbox/` (handoffs, incoming messages), `memory/learnings/`, `memory/retrospectives/`, `memory/resonance/` (identity, principles), `writing/`, `learn/` (cloned repos for study), `archive/`, `outbox/`, and `metrics/`. `[src: raw/2026-05-09-oracle-ebook-ch02-architecture.md] {conf: 0.9}`
- The structure is intentionally human-readable so that when search or UI behaves oddly, the user can open files in `ψ` and verify directly — "not secret data in a black box". `[src: raw/2026-05-09-oracle-ebook-ch02-architecture.md] {conf: 0.85}`
- The default `ψ` structure is created by `/awaken` and may differ across individual Oracles. `[src: raw/2026-05-09-oracle-ebook-ch02-architecture.md] {conf: 0.85}`
- ψ is one of three storage substrates of [[tool:arra-oracle-v3]], alongside SQLite+FTS5 and LanceDB; ψ holds the file-based knowledge such as learnings, retrospectives, inboxes, and traces. `[src: raw/2026-05-09-oracle-ebook-ch04-oracle-brain.md] {conf: 0.85}`
- Files are the chosen medium because both agents and humans can read them the same way; debugging is "just open the file" rather than trusting a black box. `[src: raw/2026-05-09-oracle-ebook-ch04-oracle-brain.md] {conf: 0.8}`

## Relationships

- composes → [[tool:arra-oracle-v3]] `{conf: 0.85}`
- uses → [[principle:nothing-is-deleted]] `{conf: 0.8}`
- depends-on → [[feature:awaken]] `{conf: 0.7}`

## Open questions

- [ ] How do teams synchronize `ψ/` across multiple machines or fleet members beyond manual git workflows?

## Changelog

- 2026-05-09 — created
