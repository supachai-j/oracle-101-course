# Outline — Oracle 101 Course

> 10 modules. Every module maps to ≥1 wiki page (Phase 5 quality gate).

The course follows a **build arc**: understand Oracle → understand its constitution → understand its anatomy → install it → extend it → run a fleet of them → put it all together as a personal Oracle.

## Module map

| # | Module | Outcome | Wiki sources | Source raw chapters |
|---|---|---|---|---|
| 1 | **What is Oracle?** | Learner can describe Oracle in one sentence and name the 4 problems it solves. | [`concept:oracle`](wiki/concept-oracle.md) | ch00, ch01 |
| 2 | **The 5 Principles + Rule 6** | Learner can recite the 6 rules and explain *why* each exists. | [`principle:nothing-is-deleted`](wiki/principle-nothing-is-deleted.md) · [`principle:patterns-over-intentions`](wiki/principle-patterns-over-intentions.md) · [`principle:external-brain-not-command`](wiki/principle-external-brain-not-command.md) · [`principle:curiosity-creates-existence`](wiki/principle-curiosity-creates-existence.md) · [`principle:form-and-formless`](wiki/principle-form-and-formless.md) · [`principle:transparency-rule-6`](wiki/principle-transparency-rule-6.md) | ch01 |
| 3 | **Anatomy of an Oracle** | Learner can name the four core repos and what each does. | [`tool:arra-oracle-v3`](wiki/tool-arra-oracle-v3.md) · [`tool:maw-js`](wiki/tool-maw-js.md) · [`tool:arra-oracle-skills-cli`](wiki/tool-arra-oracle-skills-cli.md) · [`concept:psi-folder`](wiki/concept-psi-folder.md) | ch02, ch04, ch05 |
| 4 | **Memory: How Oracles Remember** | Learner can explain ψ structure and the retrospective loop. | [`concept:psi-folder`](wiki/concept-psi-folder.md) · [`concept:retrospective`](wiki/concept-retrospective.md) | ch04 |
| 5 | **Install from Zero** | Learner can install the Oracle stack on a clean machine. | [`tool:arra-oracle-v3`](wiki/tool-arra-oracle-v3.md) · [`tool:maw-js`](wiki/tool-maw-js.md) · [`tool:arra-oracle-skills-cli`](wiki/tool-arra-oracle-skills-cli.md) | ch03 |
| 6 | **Awaken Your First Oracle** | Learner can run `/awaken` and emerge with a named, purposeful Oracle. | [`feature:awaken`](wiki/feature-awaken.md) · [`principle:transparency-rule-6`](wiki/principle-transparency-rule-6.md) | ch03, ch04 |
| 7 | **Skills & Plugins — Extending Oracle** | Learner can install a skill, write a custom slash-command, and recognize when to bud one into a plugin. | [`tool:arra-oracle-skills-cli`](wiki/tool-arra-oracle-skills-cli.md) · [`tool:maw-js`](wiki/tool-maw-js.md) | ch05, ch06, ch06b |
| 8 | **Multi-Agent Orchestration** | Learner can run two Oracles that talk to each other and split work as a family. | [`pattern:multi-agent-orchestration`](wiki/pattern-multi-agent-orchestration.md) | ch07 |
| 9 | **Autonomous Ops** | Learner can schedule an Oracle to run while they sleep — and understands the safety bounds. | [`pattern:autonomous-ops`](wiki/pattern-autonomous-ops.md) · [`principle:transparency-rule-6`](wiki/principle-transparency-rule-6.md) | ch09 |
| 10 | **Capstone — Build Your Own Oracle** | Learner ships one personally-designed Oracle: identity + purpose + ≥1 custom skill + 7 days of retrospectives. | _(synthesizes all 15 wiki pages)_ | (all chapters) |

## Coverage check

| Wiki page | Cited in modules |
|---|---|
| concept:oracle | 1 |
| concept:psi-folder | 3, 4 |
| concept:retrospective | 4 |
| principle:nothing-is-deleted | 2 |
| principle:patterns-over-intentions | 2 |
| principle:external-brain-not-command | 2 |
| principle:curiosity-creates-existence | 2 |
| principle:form-and-formless | 2 |
| principle:transparency-rule-6 | 2, 6, 9 |
| tool:arra-oracle-v3 | 3, 5 |
| tool:maw-js | 3, 5, 7 |
| tool:arra-oracle-skills-cli | 3, 5, 7 |
| feature:awaken | 6 |
| pattern:multi-agent-orchestration | 8 |
| pattern:autonomous-ops | 9 |

✅ Every wiki page is cited by ≥1 module.

## Pacing

For a part-time learner (1-2 hours/session):

| Sprint | Modules | Sessions |
|---|---|---|
| Foundations | 1, 2, 3, 4 | 4 |
| Hands-on | 5, 6, 7 | 3-4 |
| Advanced | 8, 9 | 2 |
| Capstone | 10 | 2-3 |

**Total: 11-13 sessions** ≈ 11-15 learner hours, matching the scope target (~10-15h).

## Module structure (template)

Each module page in `course-en.html` will follow this shape:

1. **Why this module** — 1-paragraph hook
2. **Prerequisites** — modules and tools assumed
3. **Concepts** — core ideas with wiki links
4. **Hands-on exercise** — concrete steps the learner runs
5. **Check yourself** — 3 questions
6. **Going further** — links to wiki and raw

## Done when (Phase 5 quality gate)

- [x] 10 modules defined with clear outcomes
- [x] Every module maps to ≥1 wiki page
- [x] Every wiki page cited by ≥1 module
- [x] Pacing maps to scope target (~10-15h)
- Next: Phase 6 — write `course-en.html` module bodies
