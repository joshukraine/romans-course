# Romans Course Page — Phase 0 Summary

- **Date:** 2026-03-24
- **Scope:** Phase 0 — Project Foundation (10/10 roadmap items complete)
- **PRD references:** `01-overview.md` §Tech Stack; `02-page-layout-and-content.md` §1, §11
- **Issues closed:** #1, #2, #3, #4, #5, #9
- **PRs merged:** #6, #7, #8, #10, #11, #12, #14

## What Was Built

Phase 0 established the complete project infrastructure: public GitHub repo with PRD-driven development conventions, directory structure, skeleton `index.html` with Tailwind CSS v4 + Tailwind Plus Elements + Alpine.js 3.15.3 + Umami analytics (all via CDN), `bin/dev` local development script, GitHub Pages deployment from `main`, custom domain `joshukraine.com` with HTTPS, MIT license for code with all-rights-reserved for course content, and full GitHub issue/label taxonomy. The site is live at `https://joshukraine.com/romans-course/` showing a skeleton page.

## Key Architecture Decisions

- **Zero build step:** All dependencies loaded via CDN `<script>` tags. Deployment is `git push` to `main`.
- **Tailwind v4 (not v3):** PRD referenced outdated v3 CDN. Updated to `@tailwindcss/browser@4` which uses `@theme` directives instead of `tailwind.config`.
- **Tailwind Plus Elements added:** Provides interactive UI behavior (disclosures, dropdowns) from Tailwind Plus snippets with built-in accessibility. Alpine.js scope narrowed to data binding and templating.
- **Custom domain on user site repo:** Setting a custom domain on a project repo serves at domain root, not `/repo-name/`. Moved domain ownership to `joshukraine.github.io` so the course page is served at `joshukraine.com/romans-course/` as the PRD expects.
- **Split license:** MIT for code, all rights reserved for course content (Joshua Steele).

## Test Coverage

No automated tests. Manual verification confirmed: CDN scripts load, Tailwind classes apply, `bin/dev` works with live reload, GitHub Pages deploys successfully, HTTPS active on custom domain.

## Follow-Up Items

- Fold 3 CHANGELOG deviations back into PRD files (recommended at phase boundary)
- Add favicon to eliminate browser 404 noise (low priority, Phase 1)
- `joshukraine.github.io` has only a placeholder page (tracked in ROADMAP under Future/Unscheduled)
