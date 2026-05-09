---
id: principle:nothing-is-deleted
type: principle
title: Principle 1 — Nothing is Deleted
status: active
confidence: 0.5
sources:
  - raw/2026-05-09-oracle-ebook-ch01-what-is-oracle.md
created: 2026-05-09
updated: 2026-05-09
updated_log:
  - 2026-05-09: created
tiers: semantic
half_life_days: 180
tags: [philosophy, principle, history]
---

# Principle 1 — Nothing is Deleted

## Summary

The first of Oracle's 5 Principles, declared by Ajarn Nat. History is sacred: timestamps are truth. Things that have happened should be appended to, preserved, and explained further — never overwritten or erased. This principle drives every storage decision in Oracle: retrospectives accumulate, learnings stack, supersession marks the old as stale rather than removing it, and the [[concept:psi-folder]] keeps files instead of mutable databases-of-record.

## Claims

- "Nothing is Deleted — sacred history; timestamps are truth. Things that have happened should be appended to, kept, and further explained — not overwritten or made to disappear." (Translated from Thai source.) `[src: raw/2026-05-09-oracle-ebook-ch01-what-is-oracle.md] {conf: 0.9}`
- Oracle's principles "do not start from feature, they start from how to preserve truth, memory, and human agency" — this principle is the load-bearing one for *truth* and *memory*. `[src: raw/2026-05-09-oracle-ebook-ch01-what-is-oracle.md] {conf: 0.85}`
- The principles were laid down by Ajarn Nat (Nat Weerawan), founder of Oracle. `[src: raw/2026-05-09-oracle-ebook-ch01-what-is-oracle.md] {conf: 0.9}`

## Relationships

- governs → [[concept:oracle]] `{conf: 0.85}`
- governs → [[concept:psi-folder]] `{conf: 0.8}`
- governs → [[concept:retrospective]] `{conf: 0.75}`

## Open questions

- [ ] How does this principle reconcile with PII deletion or right-to-be-forgotten requests at scale?

## Changelog

- 2026-05-09 — created
