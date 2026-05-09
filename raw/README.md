# raw/

Captured primary sources, **never edited after capture**.

## Format

Each file: `YYYY-MM-DD-<slug>.md`

Frontmatter:

```yaml
---
source_type: web | doc | transcript | session
source_url: <url-or-path>
ingested_at: YYYY-MM-DD
title: <verbatim title from source>
---
```

## Rules

1. **Never edit raw files** after capture. Re-ingest if the source changes — don't mutate history.
2. **Filter secrets before saving.** API keys, tokens, PII → replace with `<REDACTED:kind>`.
3. **Capture verbatim.** Preserve quotes, formatting, structure. Interpretation goes in `wiki/`, not here.
4. **One source per file.** Don't bundle.

## Workflow

```
Web/doc → WebFetch → save here → extract entities → wiki/ pages with [src: raw/...]
```
