# CLAUDE.md — Romans Course Page

A static, mobile-friendly website that serves as the central hub for all materials in Joshua Steele's expository course on the Book of Romans, taught at an evangelical church in Lviv, Ukraine. Built with plain HTML, plain CSS, and Alpine.js (CDN), hosted on GitHub Pages at `joshukraine.com/romans-course/`. No build step — push to `main` and the site is live.

## Quick-Start Commands

```bash
bin/dev                       # Start local server with live reload
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
3. **Plain CSS and native HTML** — all styling lives in the one `<style>` block in `index.html` (named classes, tokens as custom properties on `:root`). No Tailwind or other CSS framework. Prefer native elements (`<details>`, `<dialog>`) over JavaScript components.

## Handling Ambiguity

| Decide and Move | Stop and Ask |
| --- | --- |
| CSS/styling choices, class naming | Changing the `course.json` schema |
| HTML structure within a section | Adding a dependency not in the tech stack (no npm, no new CDN libs) |
| Icon choice, spacing, color shade | Changing the URL structure or anchor ID format |
| Alpine.js directive usage for existing features | Altering the handout naming convention |
| Error message or fallback text wording | Anything that affects the custom domain configuration |

**The test:** Is the decision easily reversed (HTML/CSS change, no schema change)? Decide and move — state the assumption. Does it change the data model or a public-facing URL? Ask first.

## Browser Support

Many students visit on Android phones, often older devices running Samsung Internet or an outdated WebView. Nobody may lose access to content or navigation because a feature is missing.

- Baseline **"Widely available"** features are safe to use.
- **"Newly available"** features are allowed only with a fallback that keeps the page fully usable. A missing feature may cost polish (an animation, one-module-at-a-time) but never function. Name the fallback in the PR, and test the page with the feature disabled or missing.
- Write CSS flat. Avoid nesting, `color-mix()` and range media queries (`width >= …`); older Android browsers drop the whole rule.

## Modern Web Guidance

Use the Modern Web Guidance (MWG) skill for HTML, CSS and client-side JS work here. Run its CLI with telemetry off, inline per command (other CLIs read the same generic variable, so don't export it from the shell profile):

```bash
DISABLE_TELEMETRY=1 npx -y modern-web-guidance@latest search "<topic>"
DISABLE_TELEMETRY=1 npx -y modern-web-guidance@latest retrieve <guide-id>
DISABLE_TELEMETRY=1 npx -y modern-web-guidance@latest legacy-scan .
```

`legacy-scan` covers only about 20% of the guides, so a clean scan isn't proof. Apply the browser-support rule above to anything a guide suggests.

## Conventions

- **Commits:** Conventional Commits with scope — `feat(hero): add Parthenon background image`
- **Branches:** `feat/`, `fix/`, `chore/`, `docs/`, `test/` prefixes
- **Content updates:** Edit `course.json`, add PDFs to `handouts/`, commit, push. See `docs/prd/02-page-layout-and-content.md` §5.
- **PRD deviations:** Log in `docs/prd/CHANGELOG.md` before merging — never silently deviate
- **Language:** Site content is in Ukrainian. Code comments, commit messages, and documentation are in English.
