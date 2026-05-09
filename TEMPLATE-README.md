# course-portal-template

A cloneable template for building bilingual (EN/TH) self-paced training courses with wiki-backed citations, reveal.js slides, marketing landing page, and one-command GitHub Pages deploy.

🌐 **Live example built from this template:** [supachai-j.github.io/agent-skills-training](https://supachai-j.github.io/agent-skills-training/)

---

## What you get

```
your-new-course/
├── index.html              # Marketing-style landing page (hero, features, CTAs, theme toggle)
├── 404.html                # Bilingual 404 page
├── course-en.html          # Self-paced course (English) — sticky TOC, 12 modules
├── course-th.html          # Self-paced course (Thai) — same structure, translated
├── wiki-index.md           # Wiki catalogue
├── SCHEMA.md               # Wiki domain schema
├── slides/
│   ├── training-en.html    # reveal.js deck (EN), canvas 1280×800
│   └── training-th.html    # reveal.js deck (TH)
├── wiki/                   # Cited knowledge pages (one per concept)
│   └── _TEMPLATE.md
├── raw/                    # Captured primary sources, never edited
└── graph/                  # Entity + edge JSONL (knowledge graph)
```

All pages share:
- **Dark/light theme toggle** with system preference detection
- **No JS framework** — just HTML/CSS, Google Fonts (Inter + Noto Sans Thai + JetBrains Mono)
- **No build step** — open `index.html` in a browser
- **GitHub Pages-ready** — `.nojekyll` and root layout

---

## Quick start

```bash
# 1. Clone (don't fork — you want a fresh history)
git clone https://github.com/supachai-j/course-portal-template.git my-course
cd my-course
rm -rf .git

# 2. Run the interactive setup
./setup.sh

# 3. Init git + push to your own repo
git init -b main
git add .
git commit -m "Initial: <your-course-name>"
gh repo create your-org/<repo-name> --public --source=. --push

# 4. Enable Pages
gh api -X POST /repos/your-org/<repo-name>/pages \
  -f "build_type=legacy" -f "source[branch]=main" -f "source[path]=/"

# 5. Open the site
open "https://<your-org>.github.io/<repo-name>/"
```

The setup script replaces every `{{TOKEN}}` in the templates. After it runs, fill in:
- `wiki-root/raw/` — capture 3-5 primary sources
- `wiki/` — write one page per concept, citing raw
- `course-en.html` — fill in 12 module bodies
- `slides/training-en.html` — condense the course into ~30 slides

---

## The 12-phase pipeline this template assumes

| # | Phase | Time |
|---|---|---|
| 1 | Scope (one-sentence outcome, audience, format) | ~30 min |
| 2 | Research & capture (3-5 primary sources → `raw/`) | ~2-4 h |
| 3 | Wiki init (customize SCHEMA.md) | ~30 min |
| 4 | Ingest (raw → wiki pages with citations) | ~2-3 h |
| 5 | Outline (10-12 modules, each cites wiki pages) | ~1 h |
| 6 | Course pages (`course-en.html` long-form) | ~3-5 h |
| 7 | Slide deck (`slides/training-en.html`) | ~2-3 h |
| 8 | Bilingual mirror (EN → TH) — _optional_ | ~3-4 h |
| 9 | Landing page tuning | ~1-2 h |
| 10 | Deploy to GitHub Pages | ~30 min |
| 11 | Validate (links, mobile, peer review) | ~1-2 h |
| 12 | Promote (hub featured, blog post) | ~1 h |

**Total:** ~20-30 hours bilingual · ~6-8 hours EN-only fast path.

Detailed pipeline doc: [supachai-j.github.io/process.html](https://supachai-j.github.io/process.html)
Authoring skill: [scaffolding-course-portal](https://github.com/supachai-j/agent-skills-training/tree/main/.agents/skills/scaffolding-course-portal)

---

## Tokens replaced by setup.sh

| Token | Example | Where it appears |
|---|---|---|
| `{{COURSE_NAME}}` | `Kubernetes 101` | titles, headers, OG tags |
| `{{COURSE_NAME_TH}}` | `Kubernetes เจาะลึกเชิงเทคนิค` | Thai pages, subtitles |
| `{{SLUG}}` | `kubernetes-101` | repo references in URLs |
| `{{TAGLINE}}` | `Master Kubernetes the open way` | hero, OG description |
| `{{TAGLINE_TH}}` | `เรียนรู้ Kubernetes แบบเปิด` | Thai hero |
| `{{AUTHOR}}` | `Supachai Jaturaprom` | footer, OG |
| `{{AUTHOR_HANDLE}}` | `supachai-j` | GitHub URLs |
| `{{REPO_URL}}` | `https://github.com/supachai-j/kubernetes-101` | links |
| `{{LIVE_URL}}` | `https://supachai-j.github.io/kubernetes-101/` | OG, canonical |
| `{{DOMAIN_DESCRIPTION}}` | `Kubernetes — what it is, ...` | wiki SCHEMA, descriptions |

---

## Customisation pointers

**Want a different colour scheme?** Edit the `:root` CSS variables — every page uses the same palette tokens (`--accent`, `--accent-2`, etc.).

**Don't need bilingual?** Delete `course-th.html` + `slides/training-th.html` and remove the language links from `index.html`.

**Need more than 12 modules?** Add new `<section id="m13">` blocks; the sticky TOC auto-counts via CSS counters.

**Want to skip slides?** Delete `slides/` and remove links — courses still work standalone.

---

## Credits

Pattern distilled from [agent-skills-training](https://github.com/supachai-j/agent-skills-training) (May 2026).
License: prose CC-BY-4.0, code Apache-2.0.
