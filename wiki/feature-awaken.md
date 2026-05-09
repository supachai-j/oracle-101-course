---
id: feature:awaken
type: feature
title: /awaken — Oracle Birth Ritual
status: active
confidence: 0.5
sources:
  - raw/2026-05-09-oracle-ebook-ch02-architecture.md
  - raw/2026-05-09-oracle-ebook-ch03-install-from-zero.md
  - raw/2026-05-09-oracle-ebook-ch05-skills-maw-plugin.md
  - raw/2026-05-09-oracle-ebook-ch06-maw-commands.md
created: 2026-05-09
updated: 2026-05-09
updated_log:
  - 2026-05-09: created
tiers: semantic
half_life_days: 180
tags: [feature, ritual, lifecycle, identity]
---

# /awaken — Oracle Birth Ritual

## Summary

`/awaken` is the birth ritual for a new Oracle. It is the skill you run inside an empty repo (or a freshly-budded repo) to give that Oracle an identity — naming it, discovering its principles, creating its `ψ/` structure, and writing the initial `CLAUDE.md`. Without `/awaken`, `maw bud` only produces a repo and a fleet entry; the Oracle has no self yet. With it, the new Oracle has a name, a memory vault, and a constitutional anchor in the 5 Principles + Rule 6.

## Claims

- "`/awaken` — birth ritual of an Oracle: name it, discover its principles, create `ψ/`, and lay down initial identity. Suitable for an empty repo or a new agent with no memory of its own." `[src: raw/2026-05-09-oracle-ebook-ch05-skills-maw-plugin.md] {conf: 0.9}`
- "Use `/awaken` in an empty repo to create a new Oracle that has its own memory structure and principles." `[src: raw/2026-05-09-oracle-ebook-ch05-skills-maw-plugin.md] {conf: 0.85}`
- The default `ψ/` structure that the new Oracle inherits is created by `/awaken`. `[src: raw/2026-05-09-oracle-ebook-ch02-architecture.md] {conf: 0.85}`
- `/awaken` is the second half of `maw bud`: "`maw bud` creates the repo + fleet config but the Oracle still has no identity — must `/awaken` to set the name, discover principles, and write `CLAUDE.md`". `[src: raw/2026-05-09-oracle-ebook-ch06-maw-commands.md] {conf: 0.95}`
- The post-bud workflow is: `maw bud spark --org <org>` → `maw wake spark` → run `/awaken` inside the new session. `[src: raw/2026-05-09-oracle-ebook-ch06-maw-commands.md] {conf: 0.9}`

## Relationships

- composes → [[concept:psi-folder]] `{conf: 0.9}`
- depends-on → [[tool:arra-oracle-skills-cli]] `{conf: 0.85}`
- governs → [[principle:nothing-is-deleted]] `{conf: 0.6}`
- awakens-from → [[concept:oracle]] `{conf: 0.7}`

## Open questions

- [ ] Does `/awaken` have a `--fast` mode (~5min) and a default Soul Sync mode (~20min) like other Oracle birthing skills, or is the timing source-undefined?

## Changelog

- 2026-05-09 — created
