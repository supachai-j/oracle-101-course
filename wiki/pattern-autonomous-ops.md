---
id: pattern:autonomous-ops
type: pattern
title: Autonomous Ops (Cron, PM2, systemd, Triggers)
status: active
confidence: 0.5
sources:
  - raw/2026-05-09-oracle-ebook-ch09-autonomous-ops.md
created: 2026-05-09
updated: 2026-05-09
updated_log:
  - 2026-05-09: created
tiers: semantic
half_life_days: 180
tags: [pattern, ops, automation, cron, pm2, systemd, triggers]
---

# Autonomous Ops (Cron, PM2, systemd, Triggers)

## Summary

Autonomous ops in Oracle means "the system can take care of itself for some routine work" — *not* "the system does everything without humans". The pattern keeps long-running services alive (Oracle HTTP API on `:47778`, `maw serve` on `:3456`), runs scheduled jobs (health checks, reindex, backups, daily digests), and reacts to events (agent-idle, agent-crash, qa-failed, pr-merged) — but it explicitly draws a boundary: the system never auto-merges risky production changes, never force-pushes, never deletes data, never silently changes important config, and never makes business decisions on the owner's behalf. Tools are picked by job type: PM2 for app-level Node/Bun processes, cron for scheduled work, systemd for machine-level services, and triggers/hooks for event-driven work. This pattern is governed by [[principle:external-brain-not-command]] — Oracle informs and acts within scope, but humans still own the high-risk decisions.

## Claims

- Definition: "Autonomous ops simply means the system can take care of some of itself, but does not mean the system does everything without humans." `[src: raw/2026-05-09-oracle-ebook-ch09-autonomous-ops.md] {conf: 0.95}`
- Tool selection: **PM2** for processes that should stay alive (Oracle HTTP API, `maw serve`); **cron** for scheduled work (5-min health checks, hourly index sync, daily DB+psi backup, nightly digest); **systemd** for machine-level services that boot with the host; **triggers/hooks** for event-driven automation (post-commit, post-task-done, post-handoff). `[src: raw/2026-05-09-oracle-ebook-ch09-autonomous-ops.md] {conf: 0.9}`
- Always-on runtimes: `arra-oracle-v3` MCP (stdio), `arra-oracle-v3` HTTP API (`:47778`), `maw-js` server (`:3456`), `maw-ui`, and `ui-oracle` apps. "If these go down, Oracle remains a good idea but cannot be used in practice — agents can't search, UI can't see, maw can't manage the fleet." `[src: raw/2026-05-09-oracle-ebook-ch09-autonomous-ops.md] {conf: 0.9}`
- Documented event triggers: `agent-idle` (silent past threshold → `maw hey` to ask status), `agent-crash` (notify Gale with name+window+last log; do not restart before capturing evidence), `qa-failed` (post BUG, send back to dev branch; ≥3 failures escalate to human), `pr-merged` (notify dev home base to run `maw done`). `[src: raw/2026-05-09-oracle-ebook-ch09-autonomous-ops.md] {conf: 0.85}`
- Hard boundaries — what the system **must not** do autonomously: merge production code with high risk, force-push, delete data permanently, change important config without notice, hide real errors to make logs look clean, or make business decisions for the owner. `[src: raw/2026-05-09-oracle-ebook-ch09-autonomous-ops.md] {conf: 0.95}`
- The operating loop is Observe → Decide → Act → Record → Improve, where "every problem-solving event should leave a trace (feed, handoff, learning, retro)". `[src: raw/2026-05-09-oracle-ebook-ch09-autonomous-ops.md] {conf: 0.85}`
- Document automation boundary: markdown docs in the same PR as code can be automated; conversion to DOCX/PPTX/XLSX requires explicit human (Wind) instruction. `[src: raw/2026-05-09-oracle-ebook-ch09-autonomous-ops.md] {conf: 0.8}`

## Relationships

- uses → [[tool:maw-js]] `{conf: 0.9}`
- uses → [[tool:arra-oracle-v3]] `{conf: 0.85}`
- governs → [[principle:external-brain-not-command]] `{conf: 0.85}`
- composes → [[pattern:multi-agent-orchestration]] `{conf: 0.7}`

## Open questions

- [ ] How are the documented escalation thresholds (e.g. "QA fail ≥3 times → escalate to Wind") encoded as concrete config rather than convention?

## Changelog

- 2026-05-09 — created
