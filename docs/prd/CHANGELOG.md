# PRD Changelog

This file tracks material deviations from the PRD discovered during implementation. It is the record of the "never silently deviate" rule. See `~/.claude/docs/prd-workflow/spec-driven-development.md` §2–§3 for the deviation threshold and workflow.

## Format

Each entry records one deviation or decision:

```text
### YYYY-MM-DD — [file changed]

**What changed:** One-sentence description of the change.

**Why:** The rationale — what was wrong, what was discovered, or what prompted the pivot.

**Category:** Correction | Discovery | Pivot
```

**Categories:**

- **Correction** — The PRD was wrong or inconsistent.
- **Discovery** — Implementation revealed something the PRD didn't anticipate.
- **Pivot** — A deliberate decision to change direction.

## Guidelines

- **Newest entries first** — add new entries at the top of the Entries section.
- Log when the PR is created, not after it's merged.
- The "why" is required — a change without rationale is not useful.
- Skip typo fixes and formatting corrections.
- At natural breakpoints (end of a phase, or 10+ accumulated entries), fold changes back into the PRD files themselves during a sync session.

---

## Entries

*All entries below were folded back into their respective PRD files on 2026-03-25 at the Phase 1 boundary.*

### 2026-03-24 — 02-page-layout-and-content.md §11 "Custom Domain Configuration"

**What changed:** Custom domain (`joshukraine.com`) is configured on the `joshukraine.github.io` user site repo, not on the `romans-course` project repo. The `romans-course` repo has no CNAME file and no custom domain setting. The course page is served at `joshukraine.com/romans-course/` automatically as a GitHub Pages project page under the user site's domain.

**Why:** Setting a custom domain on a project repo serves that repo's content at the domain root (`joshukraine.com/`), not at the expected path (`joshukraine.com/romans-course/`). GitHub Pages only supports the `/repo-name/` path structure when the custom domain is owned by the user site repo (`username.github.io`). The PRD expects the course page at `/romans-course/` with the root reserved for a future landing page.

**Category:** Discovery

### 2026-03-24 — 01-overview.md §Tech Stack

**What changed:** Added Tailwind Plus Elements (`@tailwindplus/elements@1`) as a CDN dependency for interactive UI component behavior (disclosures, dropdowns, dialogs).

**Why:** The project uses Tailwind Plus snippets as design reference. Elements provides the interactive behavior these snippets depend on — with built-in accessibility (ARIA, keyboard nav, focus management) — reducing the amount of custom Alpine.js needed. Alpine.js scope is narrowed to data fetching, templating, and rendering logic (`course.json` binding, loops, conditionals, date formatting). The two libraries operate in complementary lanes: Elements owns UI component behavior, Alpine.js owns data binding.

**Category:** Pivot

### 2026-03-24 — 01-overview.md §Tech Stack

**What changed:** Tailwind CSS CDN updated from v3 Play CDN (`cdn.tailwindcss.com`) to v4 browser CDN (`@tailwindcss/browser@4`).

**Why:** Tailwind CSS v4 is the current release. The v3 CDN URL referenced in the PRD is outdated. v4 uses a different configuration model (`@theme` directive in `<style type="text/tailwindcss">` instead of `tailwind.config` JS object), which will affect Phase 1 styling work.

**Category:** Discovery
