# SCHEMA — Oracle 101

> Constitution of this wiki. Every Ingest / Query / Lint operation reads this file.

## 1. Domain

Oracle is an external-brain system for AI agents — 5 Principles + Rule 6, maw runtime, skills/plugin ecosystem, multi-agent orchestration, autonomous ops.

## 2. Entity catalogue

Edit this table to fit your domain. Keep the type set small and stable.

| type | id pattern | gets its own page? | notes |
|---|---|---|---|
| `concept` | `concept:<kebab>` | yes | core ideas, definitions, mental models |
| `feature` | `feature:<kebab>` | yes | concrete features / capabilities |
| `pattern` | `pattern:<kebab>` | yes | reusable patterns, workflows |
| `tool` | `tool:<name>` | yes | tooling specific to your domain |
| `decision` | `decision:<date>-<slug>` | yes | ADRs / authoritative decisions |
| `source` | `source:<kebab>` | no | reference only — links from raw/ |

## 3. Relation catalogue

- `uses` — A consumes B at runtime
- `depends-on` — A cannot function without B
- `composes` — A is built from / orchestrates B
- `alternative-to` — A and B solve overlapping problems
- `extends` — A is a specialisation of B
- `supersedes` — new claim/decision replaces older one (old stays, marked stale)
- `contradicts` — flagged for Lint to resolve
- `cites` — A draws evidence from source B

## 4. Page rules

- One concept per page.
- YAML frontmatter mandatory (see `wiki/_TEMPLATE.md`).
- Every claim has an inline source marker `[src: raw/...]`.
- Wikilinks use entity IDs: `[[concept:foo]]`, not `[[Foo]]`.
- Status: `active` (default), `stale`, `faded`, `orphan`.

## 5. Ingest rules

- Raw source → `raw/YYYY-MM-DD-<slug>.md` untouched.
- Entity extraction runs against §2.
- A new page is created when entity is "yes" in the catalogue _and_ ≥1 non-trivial claim exists.
- Existing page updates: reinforce matching claims (bump confidence), append new ones.

## 6. Confidence and decay

- First observation: `confidence: 0.5`
- Reinforcement (independent source): `conf ← 1 - (1 - conf) * 0.6`
- Decay half-life:
  - `decision`: 365 days
  - `concept`, `pattern`, `feature`, `tool`: 180 days
  - `source`: not applicable (raw is immutable)

## 7. Privacy and secrets

Never written to wiki/ or graph/, redacted in raw/ on detection:
- API keys, tokens, signed URLs
- Passwords, private keys, certificates
- PII beyond `person:` name

Replacement token: `<REDACTED:apikey|token|pii|secret|other>`.

## 8. Training-deck rules

This wiki feeds two HTML training decks (English + Thai) under `slides/`. When a wiki page is updated with a new "deck-worthy" claim, mark the claim with `tag: deck` so Crystallize can find it.
