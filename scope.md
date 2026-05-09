# Scope — Oracle 101 Course

## Outcome (one sentence)

**Learners can build their own Oracle.**

By the end of the course, a learner can: install the Oracle stack, awaken their first Oracle with a chosen identity and purpose, customize it with skills and plugins, and run it on real work.

## Audience

**Broad — primarily curious technical users, secondarily non-technical operators.**

| Persona | What they bring | What they want |
|---|---|---|
| Developer / Engineer | shell + git fluency | install, customize, build skills |
| Tech Lead / Architect | system thinking | architecture, governance, multi-agent ops |
| Product Manager | domain literacy | what Oracle *is*, when to use it |
| Business owner / curious user | none assumed | "second brain" for daily work |

The course is structured so that **anyone** can complete the install + first-awaken track; deeper modules (skills, orchestration, autonomous ops) self-select for the technical persona.

## Format

**Full self-paced course, multi-session.**

- Reading: `course-en.html` / `course-th.html` (~12 modules)
- Slides: `slides/training-en.html` / `training-th.html` (for live delivery / instructors)
- Hands-on exercises: install, /awaken, first skill, first /bud
- Project: each learner ships one Oracle of their own design

Estimated learner time: **~10-15 hours** across multiple sessions, modular pacing.

## Languages

Bilingual EN + TH, full mirror.

- EN-first authoring (per skill convention)
- TH mirror via `/translating-to-thai-technical` after EN content stabilises
- Source ebook (Thai) provides the seed corpus for both tracks

## Source corpus

Primary: [`supachai-j/oracle-ebook`](https://github.com/supachai-j/oracle-ebook) — 13 chapters (ch00–ch10 + ch06b/ch08b) in Thai, covering Oracle origins, architecture, install, brain, skills/maw/plugin, orchestration, workflows, CMMI/compliance, autonomous ops, troubleshooting.

Secondary (to confirm during Phase 2):
- `arra-oracle-v3` — brain architecture
- `maw-js` — runtime
- `arra-oracle-skills-cli` — skills tooling
- `Soul-Brews-Studio` org repos

## Non-goals

- This is **not** an "AI 101" or "LLM fundamentals" course. We assume the learner has used an LLM before.
- We do **not** rebuild the Oracle ebook page-for-page. The ebook is *source material*; the course is its own pedagogical artifact (modules, exercises, citations).
- We do **not** ship a CLI installer or framework. Tooling is what already exists upstream.

## Done when (Phase 1 quality gate)

- [x] `scope.md` committed with one-sentence outcome, audience, format
- Next: Phase 2 — capture sources into `raw/`
