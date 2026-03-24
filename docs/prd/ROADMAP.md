# Romans Course Page — Implementation Roadmap

This document tracks implementation progress through phased delivery. Phases are ordered by dependency: each phase builds on the infrastructure established by previous phases.

**Progress key:** `[ ]` Not started · `[~]` In progress · `[x]` Complete · `[—]` Deferred / descoped

## Phase Overview

| Phase | Name | Dependencies | Status |
| --- | --- | --- | --- |
| 0 | Project Foundation | — | Not started |
| 1 | Core Page Structure | Phase 0 | Not started |
| 2 | Content Population and Launch | Phase 1 | Not started |

---

## Phase 0 — Project Foundation

Establish the repository, hosting configuration, and file structure.

- [x] Create `romans-course` repository on GitHub (public)
- [x] Set up directory structure: `data/`, `handouts/`, `assets/img/`, `assets/css/`, `snippets/`
- [x] Create `.gitignore` (exclude `snippets/`, `.DS_Store`, OS files)
- [x] Create `CLAUDE.md` with project-level guidance for Claude Code
- [x] Create `README.md` with project description and content update instructions
- [ ] Enable GitHub Pages on the `main` branch
- [ ] Create skeleton `index.html` with Tailwind CDN, Alpine.js CDN, and Umami tracking script tags, confirming all load correctly
- [ ] Verify the site is accessible at `joshukraine.github.io/romans-course/`
- [ ] Configure custom domain: update Namecheap DNS (A records + CNAME), add `CNAME` file to repo, enable HTTPS in GitHub Pages settings
- [ ] Verify `joshukraine.com/romans-course/` loads correctly with HTTPS

**PRD references:** `01-overview.md` §Tech Stack; `02-page-layout-and-content.md` §1 "Site Structure", §11 "Custom Domain Configuration"

---

## Phase 1 — Core Page Structure

Build the functional page: data-driven rendering, navigation, responsive layout, and all interactive behavior.

- [ ] Optimize hero image: compress Parthenon source image for web (target < 200 KB), create responsive variants if needed, place in `assets/img/`
- [ ] Create Open Graph preview image (`assets/img/og-image.jpg`): 1200×630px, derived from Parthenon hero image with course title overlay
- [ ] Create `data/course.json` with schema and seed data (one unit, one or two lectures with placeholder content)
- [ ] Implement Alpine.js data layer: fetch `course.json` on init, store in reactive state
- [ ] Implement hero area: Parthenon background image with course title overlay, responsive cropping, fast loading
- [ ] Implement course introduction section (description, registration link, Viber link — read from JSON)
- [ ] Implement unit rendering: loop over units, display unit headers with number, title, passage, and lecture count
- [ ] Implement collapsible unit sections with Alpine.js (most recent unit expanded by default, others collapsed)
- [ ] Implement lecture card rendering within units: number, title, date (formatted with `Intl.DateTimeFormat`), passage
- [ ] Implement video thumbnail display: YouTube thumbnail image linked to video URL, opens in new tab
- [ ] Implement handout download links with download icons
- [ ] Implement quiz link display (shown only when `quizUrl` is present)
- [ ] Implement `upcoming` lecture status (show title and date only, no video/handout/quiz links)
- [ ] Implement anchor IDs for each lecture (`#lecture-{N}`) with hash-based scroll-on-load after Alpine renders
- [ ] Implement sticky navigation bar with course title
- [ ] Implement desktop navigation: horizontal unit anchor links in the navigation bar
- [ ] Implement mobile navigation: hamburger toggle with dropdown unit links (Alpine.js)
- [ ] Implement smooth scroll for unit anchor links, accounting for sticky header offset
- [ ] Implement responsive layout: mobile-first, max-width content area on desktop, readable spacing
- [ ] Implement footer: teacher name, organization link, copyright, Unsplash photo attribution
- [ ] Add Open Graph and Twitter Card meta tags in `<head>`
- [ ] Add Umami analytics tracking script in `<head>`
- [ ] Style the page: beige/blue color palette, typography, card styling, whitespace, icons (per §7 design direction)

**PRD references:** `02-page-layout-and-content.md` §2–§9

---

## Phase 2 — Content Population and Launch

Populate with real course data, add all existing handout PDFs, and prepare for sharing with students.

- [ ] Populate `course.json` with Unit 1 lectures (1–2): real titles, dates, YouTube URLs, handout references
- [ ] Populate `course.json` with Unit 2 lectures (3–4): real titles, dates, YouTube URLs, handout references
- [ ] Add all existing handout PDFs to `handouts/` with correct naming convention
- [ ] Add any upcoming lectures (5–6) with `"status": "upcoming"` if details are known
- [ ] Add quiz URLs if quizzes are ready; otherwise leave as `null`
- [ ] Test all links: YouTube videos open correctly, PDFs download, quiz links work
- [ ] Test on mobile: Viber link → phone browser flow, navigation, handout downloads, overall appearance
- [ ] Test anchor link sharing: verify `#lecture-N` URLs scroll to the correct lecture after page load
- [ ] Test social sharing preview: share the URL in Viber and Telegram, verify OG image and description render correctly
- [ ] Verify Umami is tracking page views in the dashboard
- [ ] Verify custom domain is fully operational: HTTPS active, `www` redirect works, `joshukraine.com/romans-course/` loads correctly
- [ ] Update `README.md` with final content update instructions and domain configuration notes
- [ ] Share the URL with the Viber community

**PRD references:** `02-page-layout-and-content.md` §4–§5, §8–§11; `01-overview.md` §Content Structure

---

## Future / Unscheduled

Items that are out of scope for the initial launch but may be added later:

- [ ] `joshukraine.com` root landing page (separate repo: `joshukraine.github.io`)
- [ ] Search or filter functionality (if the course grows large enough to warrant it)
- [ ] Print-friendly styling for the course overview
