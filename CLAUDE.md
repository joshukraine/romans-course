# CLAUDE.md — Romans Course Page

A static, mobile-friendly website that serves as the central hub for all materials in Joshua Steele's expository course on the Book of Romans, taught at an evangelical church in Lviv, Ukraine. Built with plain HTML, Tailwind CSS (CDN), and Alpine.js (CDN), hosted on GitHub Pages at `joshukraine.com/romans-course/`. No build step — push to `main` and the site is live.

## Quick-Start Commands

```bash
# No build step. Start a local server with live reload:
live-server --port=8000       # Opens browser, reloads on file changes
```

There is no test framework or linter for this project. Validation is manual: check links, test on mobile, verify PDF downloads.

## Key Files

| File | Purpose |
| --- | --- |
| `index.html` | The single page — all course content rendered here |
| `data/course.json` | Single source of truth for all course content metadata |
| `handouts/` | PDF handouts, named `lecture-{NN}-{slug}.pdf` |
| `assets/img/` | Hero image, OG image, and other static assets |
| `docs/prd/README.md` | PRD index — start here for any feature question |
| `docs/prd/ROADMAP.md` | Task list with checkboxes, one PR per item |
| `docs/prd/CHANGELOG.md` | PRD deviation log — update before merging any deviation |
| `~/.claude/docs/label-taxonomy.md` | Work type labels, branch naming, board configuration |

## Architectural Guardrails

1. **No build step** — everything runs from CDN scripts and static files. Do not introduce npm, bundlers, or SSGs.
2. **JSON-driven content** — all course data lives in `course.json`. Do not hardcode lecture content in HTML.
3. **No licensed snippets in version control** — Tailwind Plus snippets go in `snippets/` (gitignored) for local reference only.

## Handling Ambiguity

| Decide and Move | Stop and Ask |
| --- | --- |
| CSS/styling choices, Tailwind class selection | Changing the `course.json` schema |
| HTML structure within a section | Adding a dependency not in the tech stack (no npm, no new CDN libs) |
| Icon choice, spacing, color shade | Changing the URL structure or anchor ID format |
| Alpine.js directive usage for existing features | Altering the handout naming convention |
| Error message or fallback text wording | Anything that affects the custom domain configuration |

**The test:** Is the decision easily reversed (HTML/CSS change, no schema change)? Decide and move — state the assumption. Does it change the data model or a public-facing URL? Ask first.

## Conventions

- **Commits:** Conventional Commits with scope — `feat(hero): add Parthenon background image`
- **Branches:** `feat/`, `fix/`, `chore/`, `docs/`, `test/` prefixes
- **Content updates:** Edit `course.json`, add PDFs to `handouts/`, commit, push. See `docs/prd/02-page-layout-and-content.md` §5.
- **PRD deviations:** Log in `docs/prd/CHANGELOG.md` before merging — never silently deviate
- **Language:** Site content is in Ukrainian. Code comments, commit messages, and documentation are in English.
