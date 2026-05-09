---
id: concept:oracle
type: concept
title: Oracle (External Brain for AI Agents)
status: active
confidence: 0.8
sources:
  - raw/2026-05-09-oracle-ebook-ch00-intro.md
  - raw/2026-05-09-oracle-ebook-ch01-what-is-oracle.md
created: 2026-05-09
updated: 2026-05-09
updated_log:
  - 2026-05-09: created
tiers: semantic
half_life_days: 180
tags: [core, definition]
---

# Oracle (External Brain for AI Agents)

## Summary

Oracle is an external-brain system that lets humans and multiple AI agents work continuously across sessions by sharing memory, skills, workflow, orchestration, and UI. It is not a single bot, not a database, and not a web page — it is a stack composed of [[tool:arra-oracle-v3]] (memory), [[tool:maw-js]] (runtime), [[tool:arra-oracle-skills-cli]] (skills), and a [[concept:psi-folder]] vault, all governed by the [[principle:nothing-is-deleted]] family of principles. Oracle was founded by Nat Weerawan ("Ajarn Nat") who designed every layer of the architecture and the 5 Principles + Rule 6 that govern Oracle behavior.

## Claims

- Oracle is described in the shortest form as "an external brain that humans and many agents can use together" ("Oracle คือสมองภายนอกที่คนและ agent หลายตัวใช้ร่วมกันได้"). `[src: raw/2026-05-09-oracle-ebook-ch01-what-is-oracle.md] {conf: 0.9}`
- Oracle is a set of memory, skill, workflow, orchestration, and UI joined into an external brain — not a single bot, not just a database, and not just a web page. `[src: raw/2026-05-09-oracle-ebook-ch01-what-is-oracle.md] {conf: 0.85}`
- Pre-Oracle pain points it addresses include session context loss, knowledge scattered across chat/repo/people, agents stepping on each other's work, tools tied to one agent, and UIs that pretend to be the source of truth. `[src: raw/2026-05-09-oracle-ebook-ch01-what-is-oracle.md] {conf: 0.8}`
- Oracle was founded by Nat Weerawan ("Ajarn Nat"), who laid down the philosophy (5 Principles + Rule 6) and designed the architecture from `arra-oracle-v3` (brain) through `maw-js` (nervous system) to `arra-oracle-skills-cli` (skills) and the UI/plugin ecosystem. `[src: raw/2026-05-09-oracle-ebook-ch00-intro.md] {conf: 0.9}`
- Memorable mental model: `arra-oracle-v3` is the brain, `psi` is the notebook, `arra-oracle-skills-cli` installs habits, `maw-js` is the nervous system, and `maw-ui`/`ui-oracle` are the windows. `[src: raw/2026-05-09-oracle-ebook-ch01-what-is-oracle.md] {conf: 0.85}`
- The Unity Formula `infinity = oracle(oracle(oracle(...)))` expresses that many Oracles, on many machines, with many personalities, become ONE in a systemic sense when they share MCP, memory, and protocol — not because they are identical, but because they read and forward the same truth. `[src: raw/2026-05-09-oracle-ebook-ch01-what-is-oracle.md] {conf: 0.75}`

## Relationships

- composes → [[tool:arra-oracle-v3]] `{conf: 0.9}`
- composes → [[tool:maw-js]] `{conf: 0.9}`
- composes → [[tool:arra-oracle-skills-cli]] `{conf: 0.9}`
- uses → [[concept:psi-folder]] `{conf: 0.85}`
- governs → [[principle:nothing-is-deleted]] `{conf: 0.85}`

## Open questions

- [ ] How do Oracle deployments differ in scope between solo developers and teams running fleets of 7+ agents (per ch07 example)?

## Changelog

- 2026-05-09 — created
