---
id: <entity-type>:<kebab-slug>            # e.g., concept:progressive-disclosure
type: <entity-type>                       # one of the types in SCHEMA.md §2
title: <Human-readable title>
status: active                            # active | stale | faded | orphan
confidence: 0.5                           # page-level rollup
sources: []                               # raw/ files that contributed
created: YYYY-MM-DD
updated: YYYY-MM-DD
updated_log:
  - YYYY-MM-DD: created
tiers: semantic                           # episodic | semantic | procedural
half_life_days: 180
tags: []
---

# <Title>

## Summary

One paragraph. Plain language. Wikilink any jargon.

## Claims

- <Claim.> `[src: raw/YYYY-MM-DD-source.md] {conf: 0.5}`

## Relationships

- uses → [[entity-id]] `{conf: 0.x}`

## Open questions

- [ ] <question>

## Changelog

- YYYY-MM-DD — created
