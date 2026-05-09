---
id: tool:maw-js
type: tool
title: maw-js — Multi-Agent Orchestrator (Nervous System)
status: active
confidence: 0.8
sources:
  - raw/2026-05-09-oracle-ebook-ch02-architecture.md
  - raw/2026-05-09-oracle-ebook-ch05-skills-maw-plugin.md
created: 2026-05-09
updated: 2026-05-09
updated_log:
  - 2026-05-09: created
tiers: semantic
half_life_days: 180
tags: [tool, orchestrator, runtime, tmux, plugins]
---

# maw-js — Multi-Agent Orchestrator (Nervous System)

## Summary

`maw-js` is Oracle's nervous system — the multi-agent workflow orchestrator. Where [[tool:arra-oracle-v3]] gives agents memory, `maw-js` gives them teamwork. It manages tmux sessions, sends messages to agents, captures pane output, monitors fleet status, runs lifecycle commands (`wake`, `sleep`, `stop`, `done`), exposes an API and websocket on default port `3456`, and loads plugins from a runtime plugin directory. Crucially, `maw-js` does not replace Oracle memory or the agent itself — it is the visible coordination layer that makes sure many agents on the same machine (or across peers) work in an orderly, observable way through real tmux panes that humans can peek at.

## Claims

- "`maw-js` is the multi-agent workflow orchestrator" — its job is to control the workflow (open/wake sessions, send messages, read pane output, watch fleet status, run wake/sleep/stop/done lifecycle, expose API+websocket, and load plugins). `[src: raw/2026-05-09-oracle-ebook-ch05-skills-maw-plugin.md] {conf: 0.9}`
- "Maw is not Oracle memory and is not the agent — it is the middle layer that helps many agents in one machine, or across peers, work in an orderly way." `[src: raw/2026-05-09-oracle-ebook-ch05-skills-maw-plugin.md] {conf: 0.9}`
- It controls agents through tmux so that sessions and panes are visible — "agents are not invisible workers in the back, they have sessions and panes you can open and watch". `[src: raw/2026-05-09-oracle-ebook-ch05-skills-maw-plugin.md] {conf: 0.9}`
- The default `maw serve` port for API/websocket/UI is `3456`. `[src: raw/2026-05-09-oracle-ebook-ch02-architecture.md] {conf: 0.9}`
- Configuration env vars: `MAW_HOME` (per-instance config/plugin/UI), `MAW_CONFIG_DIR` (`maw.config.json` and `fleet/*.json`), `MAW_PORT` (server port, default `3456`), `MAW_PLUGIN_HOME` (runtime plugin packages, e.g. `~/.maw/plugins`). `[src: raw/2026-05-09-oracle-ebook-ch02-architecture.md] {conf: 0.9}`
- Memorable phrasing: "`maw-js` is the nervous system; `maw-ui` is the eye; Oracle memory is the brain — three things that work together but are not one thing." `[src: raw/2026-05-09-oracle-ebook-ch05-skills-maw-plugin.md] {conf: 0.85}`

## Relationships

- composes → [[concept:oracle]] `{conf: 0.9}`
- uses → [[tool:arra-oracle-v3]] `{conf: 0.85}`
- composes → [[pattern:multi-agent-orchestration]] `{conf: 0.9}`
- composes → [[pattern:autonomous-ops]] `{conf: 0.85}`

## Open questions

- [ ] How does cross-machine peer transport (mentioned in ch07) progress beyond the "intro/needs token+runbook" status?

## Changelog

- 2026-05-09 — created
