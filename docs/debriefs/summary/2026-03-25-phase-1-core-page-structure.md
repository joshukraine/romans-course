# Romans Course Page — Phase 1 Summary

- **Date:** 2026-03-25
- **Scope:** Phase 1 — Core Page Structure (23/23 roadmap items + follow-up fix #24)
- **PRD references:** `02-page-layout-and-content.md` §2–§9; `01-overview.md` §Tech Stack
- **Issues closed:** #15, #16, #17, #18, #19, #20, #24
- **PRs merged:** #21, #22, #23, #25, #26, #27, #28

## What Was Built

Phase 1 built the complete functional course page: Alpine.js data layer fetching `course.json`, Parthenon hero image with dark overlay and course title, sticky navigation with data-driven unit links (desktop) and Elements `<el-dialog>` hamburger menu (mobile), course introduction section with conditional registration/Viber CTAs, collapsible unit sections via `<el-disclosure>`, lecture cards with YouTube thumbnails, handout downloads, quiz links, upcoming status badges, deep linking with auto-expand of collapsed parent units, footer with teacher attribution and Unsplash credit, OG/Twitter meta tags, and a custom beige/blue color palette via Tailwind v4 `@theme`. A `/simplify` review extracted helper functions, added error handling, and fixed HTML semantics. A `/drift-check` confirmed PRD alignment and 3 CHANGELOG deviations were folded back into PRD files.

## Key Architecture Decisions

- **Three-library stack:** Tailwind v4 (styling), Elements (UI component behavior — disclosures, dialogs), Alpine.js (data binding and templating). Each has a clear, non-overlapping responsibility.
- **Single `x-data` on `<body>`:** All state in one object — appropriate for a single-page site with one data source. Helpers (`videoId()`, `pluralize()`, `dateFmt`) live here since separate JS files would require a build step.
- **Custom `@theme` colors:** `sand-*` (warm beige) and `sky-blue-*` (muted blue) defined as first-class Tailwind scales, drawn from the Parthenon image tones.
- **Deep linking via `$nextTick`:** After Alpine renders, check URL hash, auto-expand parent `<el-disclosure>` if collapsed, then scroll to target. Couples to Elements' `hidden` attribute, accepted as low-risk.
- **Linked video thumbnails, not embedded iframes:** YouTube thumbnails are ~10 KB each vs ~500 KB per iframe. Critical for 60+ lectures on one page.
- **Slavic plural rules:** Correct Ukrainian pluralization for all numbers (1/few/many), not just the simplified 1/2–4/5+ check.

## Test Coverage

No automated tests. Manual verification covered: data loading, hero image, navigation (desktop + mobile), unit expand/collapse, lecture cards (all field types), deep linking, footer, responsive layout, touch targets. `/simplify` review caught and fixed: missing fetch error handling, per-card formatter allocation, inline regex hack, incorrect pluralization, missing hero dimensions, missing preconnect hints, invalid `<dt>`/`<dd>` without `<dl>`.

## Follow-Up Items

- Tailwind CDN is parser-blocking (inherent to no-build-step constraint — monitor FCP)
- No responsive `srcset` for hero image (low priority at 171 KB)
- Favicon still missing (minor console noise)
- OG image needs stylized text overlay (deferred to future update)
