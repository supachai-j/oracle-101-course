---
id: concept:retrospective
type: concept
title: Retrospective Loop (/rrr — episodic to semantic memory)
status: active
confidence: 0.5
sources:
  - raw/2026-05-09-oracle-ebook-ch04-oracle-brain.md
  - raw/2026-05-09-oracle-ebook-ch05-skills-maw-plugin.md
created: 2026-05-09
updated: 2026-05-09
updated_log:
  - 2026-05-09: created
tiers: semantic
half_life_days: 180
tags: [memory, ritual, lifecycle]
---

# Retrospective Loop (/rrr — episodic to semantic memory)

## Summary

The retrospective loop is the practice — invoked by the `/rrr` skill — of closing a session by writing a retrospective, an "AI diary", and lessons learned, then persisting them under `ψ/memory/retrospectives/` and `ψ/memory/learnings/`. It is the mechanism by which a single session's episodic experience becomes part of the durable, semantic knowledge that future Oracles read on `/recap`. Retrospectives are stored as files in the [[concept:psi-folder]] vault and indexed by [[tool:arra-oracle-v3]] for search. Without `/rrr`, a session's lessons fade with the context window; with it, they survive.

## Claims

- The `ψ/` vault includes `memory/retrospectives/` for end-of-session summaries and `memory/learnings/` for discovered patterns and lessons. `[src: raw/2026-05-09-oracle-ebook-ch04-oracle-brain.md] {conf: 0.85}`
- `/rrr` is described as: "writes a retrospective, AI diary, and lessons learned and saves them to `ψ/` as long-term memory; used to close work with a trace, not letting lessons disappear with the context window". `[src: raw/2026-05-09-oracle-ebook-ch05-skills-maw-plugin.md] {conf: 0.9}`
- "Every session should end with `/rrr` so Oracle remembers what happened, what was decided, and what follow-ups remain." `[src: raw/2026-05-09-oracle-ebook-ch05-skills-maw-plugin.md] {conf: 0.85}`
- Retrospective output feeds the counterpart skill `/recap`, which on session start "pulls context from retro, handoff and git state back into the current session" — closing the loop from yesterday's experience to today's working memory. `[src: raw/2026-05-09-oracle-ebook-ch05-skills-maw-plugin.md] {conf: 0.85}`
- A core idea behind Oracle is that "agents should not keep everything in their own heads, because when the session ends the memory disappears" — the retrospective is one of the rituals that converts in-head memory into external, durable memory. `[src: raw/2026-05-09-oracle-ebook-ch04-oracle-brain.md] {conf: 0.85}`

## Relationships

- uses → [[concept:psi-folder]] `{conf: 0.9}`
- governs → [[principle:nothing-is-deleted]] `{conf: 0.75}`
- composes → [[concept:oracle]] `{conf: 0.7}`

## Open questions

- [ ] What is the exact schema or template a `/rrr` retrospective writes? (Source describes purpose, not format.)

## Changelog

- 2026-05-09 — created
