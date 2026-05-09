---
id: pattern:multi-agent-orchestration
type: pattern
title: Multi-Agent Orchestration (Family, talk-to, team-agents)
status: active
confidence: 0.5
sources:
  - raw/2026-05-09-oracle-ebook-ch07-orchestration.md
created: 2026-05-09
updated: 2026-05-09
updated_log:
  - 2026-05-09: created
tiers: semantic
half_life_days: 180
tags: [pattern, orchestration, family, delegation, tiers]
---

# Multi-Agent Orchestration (Family, talk-to, team-agents)

## Summary

Oracle orchestration is the pattern of running not one super-AI but a *family* of named Oracles, each with its own role, domain, and context, while still sharing memory through [[tool:arra-oracle-v3]] and coordination through [[tool:maw-js]]. The pattern combines three things: (1) named family members (e.g. Gale = orchestrator, Leaf = manufacturing dev, Bamboo = general dev, Kati = QA, Luna = content, Latte = research, Sky = trading), (2) a three-tier delegation model (Arrows / Squads / Federation) that picks the lightest viable orchestration for each task, and (3) explicit reporting contracts (heartbeat, STUCK, DONE) so silent agents are caught early. The principle is "lowest tier that works" — and the human stays the owner of risky decisions.

## Claims

- An Oracle deployment is "not a single AI doing everything alone, but a family of agents that work together — each with a name, a role, its own context, and shared central memory". The reference family example is 7 Oracles. `[src: raw/2026-05-09-oracle-ebook-ch07-orchestration.md] {conf: 0.9}`
- Orchestration spans three layers: Memory (`arra-oracle-v3`), Orchestration (`maw-js` for tmux, messaging, capture, fleet), and UI/Lens (`maw-ui`, `ui-oracle`). `[src: raw/2026-05-09-oracle-ebook-ch07-orchestration.md] {conf: 0.9}`
- Three delegation tiers: **Tier 1 Arrows** (agent-tool fire-and-collect, ≤5min, no coordination, ~3-7x token cost, doesn't survive session death); **Tier 2 Squads** (`TeamCreate` + `SendMessage` + `TaskList`, 5-30min coordinated work, dies with parent session); **Tier 3 Federation** (`maw wake`/`maw workon`/real tmux session, long-running, survives session death, cross-machine when transport is configured). `[src: raw/2026-05-09-oracle-ebook-ch07-orchestration.md] {conf: 0.9}`
- The decision rule is "lowest tier that works": read-only → Arrow; coordinated implementation → Squad; needs to outlive session or cross machines → Federation. `[src: raw/2026-05-09-oracle-ebook-ch07-orchestration.md] {conf: 0.9}`
- A reporting contract is mandatory regardless of tier: heartbeat every ~5min (`maw hey gale "[name] PROGRESS: ..."`), STUCK on block, DONE on completion. Without it, the dominant failure mode is "Silent Agent" — quiet for 30+ minutes with no one noticing. `[src: raw/2026-05-09-oracle-ebook-ch07-orchestration.md] {conf: 0.9}`
- Documented failure modes the pattern must guard against: Silent Agent, Merge Conflicts (no clear ownership), Orphaned Worktrees (no `maw done` after agent death), Prompt loss (instructions sent after `claude -p` finishes), and Wrong Tier. `[src: raw/2026-05-09-oracle-ebook-ch07-orchestration.md] {conf: 0.85}`
- Orchestrator discipline: the lead Oracle (e.g. Gale) "should not write code competing with the team — lead should assign, monitor, merge, and verify"; and Gale "should not run `maw workon` for a dev Oracle, because the worktree/window will end up in Gale's session, not in the dev's home base". `[src: raw/2026-05-09-oracle-ebook-ch07-orchestration.md] {conf: 0.85}`

## Relationships

- uses → [[tool:maw-js]] `{conf: 0.95}`
- uses → [[tool:arra-oracle-v3]] `{conf: 0.85}`
- governs → [[principle:form-and-formless]] `{conf: 0.7}`
- composes → [[pattern:autonomous-ops]] `{conf: 0.6}`

## Open questions

- [ ] What is the production-ready status of cross-machine peer transport for Tier 3 federation — chapter explicitly flags it as "intro" needing config + token + runbook?

## Changelog

- 2026-05-09 — created
