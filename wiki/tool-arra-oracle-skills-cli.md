---
id: tool:arra-oracle-skills-cli
type: tool
title: arra-oracle-skills-cli — Skills Installer for Multi-Agent Workflows
status: active
confidence: 0.8
sources:
  - raw/2026-05-09-oracle-ebook-ch02-architecture.md
  - raw/2026-05-09-oracle-ebook-ch03-install-from-zero.md
  - raw/2026-05-09-oracle-ebook-ch04-oracle-brain.md
  - raw/2026-05-09-oracle-ebook-ch05-skills-maw-plugin.md
  - raw/2026-05-09-oracle-ebook-ch06-maw-commands.md
created: 2026-05-09
updated: 2026-05-09
updated_log:
  - 2026-05-09: created
tiers: semantic
half_life_days: 180
tags: [tool, cli, skills, profiles]
---

# arra-oracle-skills-cli — Skills Installer for Multi-Agent Workflows

## Summary

`arra-oracle-skills-cli` is the CLI that installs Oracle skills (markdown workflow files) into each agent's skill folder — `~/.claude/skills/` for Claude Code, `~/.codex/skills/` for Codex, etc. Skills are not processes: they are reusable workflow markdown that agents read and follow, so installing them does not consume CPU/RAM. The CLI ships profile bundles — `seed` (~15 skills), `standard` (~30), and `full`/`lab` (47+, recommended for production) — and the `/go <profile>` skill switches between them. By installing the same skills across every agent, multiple agents (Claude, Codex, Cursor, Gemini CLI, etc.) end up speaking a shared workflow language: `/recap`, `/rrr`, `/trace`, `/learn`, `/forward`, [[feature:awaken]], and so on.

## Claims

- The CLI's job is to "install skills into the skill folder of each agent — Claude has its skills folder, Codex has its skills folder, and other agents have their own". `[src: raw/2026-05-09-oracle-ebook-ch04-oracle-brain.md] {conf: 0.9}`
- Skills are markdown files in `~/.claude/skills/` or `~/.codex/skills/`, not processes; "uninstalled skills don't consume resources, they're just files". `[src: raw/2026-05-09-oracle-ebook-ch03-install-from-zero.md] {conf: 0.9}`
- Profile lineup: `seed` (~15 skills, "starter / new Oracle"), `standard` (~30, "session management + trace + learn"), `full`/`lab` (47+, "recommended — all skills, used in production"). `[src: raw/2026-05-09-oracle-ebook-ch03-install-from-zero.md] {conf: 0.9}`
- Install command pattern: `bun add -g "github:$ORACLE_SOURCE_ORG/arra-oracle-skills-cli"` then `arra-oracle-skills install -g --profile lab`; agent-specific install via `--agent claude` or `--agent codex`. `[src: raw/2026-05-09-oracle-ebook-ch03-install-from-zero.md] {conf: 0.9}`
- Profile switching at runtime is via the `/go` skill in Claude Code: `/go lab`, `/go full`, `/go standard`. `[src: raw/2026-05-09-oracle-ebook-ch03-install-from-zero.md] {conf: 0.85}`
- Core "must-know" skills installed by the CLI include `/recap` (start), `/rrr` (close), `/trace` (search), `/learn` (explore), `/forward` (handoff), `/who-are-you` (identity check), `/awaken` (birth a new Oracle), and `/philosophy` (review 5 Principles + Rule 6). `[src: raw/2026-05-09-oracle-ebook-ch05-skills-maw-plugin.md] {conf: 0.9}`
- Skills as a layer "make working style not bound to a single agent — if today is Claude and tomorrow is Codex, the system still has a shared language and steps". `[src: raw/2026-05-09-oracle-ebook-ch04-oracle-brain.md] {conf: 0.9}`

## Relationships

- composes → [[concept:oracle]] `{conf: 0.9}`
- uses → [[tool:arra-oracle-v3]] `{conf: 0.7}`
- composes → [[feature:awaken]] `{conf: 0.85}`
- composes → [[concept:retrospective]] `{conf: 0.85}`

## Open questions

- [ ] Which "Skill → Plugin" migrations from ch05 (`maw-learn`, `maw-incubate`, `maw-project`) are production-ready vs scaffold/stub?

## Changelog

- 2026-05-09 — created
