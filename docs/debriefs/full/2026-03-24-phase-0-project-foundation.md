# Romans Course Page — Phase 0: Project Foundation Debrief

- **Date:** 2026-03-24
- **Scope:** Phase 0 — Project Foundation (all 10 roadmap items)
- **PRD references:** `01-overview.md` §Tech Stack; `02-page-layout-and-content.md` §1 "Site Structure", §11 "Custom Domain Configuration"
- **Issues closed:** #1, #2, #3, #4, #5, #9
- **PRs merged:** #6, #7, #8, #10, #11, #12, #14
- **PRD deviations logged:** 3 (Tailwind v4, Elements addition, custom domain architecture)

---

## 1. What We Built (and Why It Matters)

Phase 0 establishes everything the project needs before a single line of application code is written: the repository, file structure, hosting pipeline, custom domain with HTTPS, and developer tooling. The site is now live at `https://joshukraine.com/romans-course/` — currently showing a skeleton page with the course title, confirming the full delivery chain works end to end.

**What's in place:**

- Public GitHub repo with PRD-driven development infrastructure (`docs/prd/`, `CLAUDE.md`, label taxonomy, ROADMAP with progress tracking)
- Directory structure matching the PRD spec: `data/`, `handouts/`, `assets/img/`, `assets/css/`, `snippets/` (gitignored)
- Skeleton `index.html` with four CDN scripts: Tailwind CSS v4, Tailwind Plus Elements, Alpine.js 3.15.3, Umami analytics
- `bin/dev` script for local development with live reload
- GitHub Pages deploying from `main` — push-to-deploy with zero configuration
- Custom domain `joshukraine.com` with HTTPS enforced, serving the course page at `/romans-course/`
- MIT license for code, all-rights-reserved for course content
- README with content update workflow and local dev instructions

**What was intentionally deferred:**

- All page content, layout, and styling (Phase 1)
- Real course data and handout PDFs (Phase 2)
- The `joshukraine.com` root landing page (future/unscheduled — currently shows a "Coming soon" placeholder)

This phase is pure foundation. There's nothing to show a student yet, but there's everything a developer needs to start building. The deployment pipeline is proven, the domain is live, and the project conventions are documented.

---

## 2. Architecture & Design Decisions

### Zero-build-step static site

The most consequential architecture decision was already in the PRD: no npm, no bundler, no SSG. Everything runs from CDN scripts and static files. This means:

- **Deployment is `git push`.** No CI pipeline, no build artifacts, no environment variables. GitHub Pages serves raw files from `main`.
- **Adding a lecture is editing JSON and dropping PDFs.** The content update workflow (`02-page-layout-and-content.md` §5 "Content Update Workflow") is designed for someone who knows git but doesn't want to think about builds.
- **The tradeoff is real:** no tree-shaking, no CSS purging, no module bundling. We're loading full CDN libraries. For a single-page site with a handful of scripts, this is the right call — the simplicity is worth more than the kilobytes.

### CDN dependency stack (and the three deviations)

The PRD specified Tailwind CSS via CDN and Alpine.js via CDN. During implementation, we discovered three things that required updates:

**Tailwind v3 → v4** (`01-overview.md` §Tech Stack): The PRD referenced `cdn.tailwindcss.com`, which is the v3 Play CDN. Tailwind v4 is the current release and uses a completely different CDN URL (`@tailwindcss/browser@4`) and configuration model (`@theme` directives in `<style type="text/tailwindcss">` instead of a `tailwind.config` JS object). This will affect how we define the beige/blue color palette in Phase 1.

**Tailwind Plus Elements addition** (`01-overview.md` §Tech Stack): Joshua has a Tailwind Plus subscription with licensed HTML snippets used as design reference. Those snippets depend on the Elements library (`@tailwindplus/elements@1`) for interactive behavior — disclosures, dropdowns, dialogs. Rather than re-implementing all that interaction logic in Alpine.js, we added Elements as a CDN dependency. This narrows Alpine.js's scope to data binding and templating (fetching `course.json`, looping over units/lectures, conditional rendering), while Elements handles UI component behavior with built-in accessibility. The two libraries don't conflict — they operate in different lanes.

**Custom domain architecture** (`02-page-layout-and-content.md` §11 "Custom Domain Configuration"): This was the most painful discovery. The PRD says to put the custom domain on the `romans-course` repo. What we learned: setting a custom domain on a GitHub Pages *project* repo serves content at the domain root (`joshukraine.com/`), not at `/romans-course/`. To get the expected path structure, the custom domain must live on the *user site* repo (`joshukraine.github.io`), and project repos are automatically served at `/repo-name/` underneath. We created `joshukraine.github.io` with a placeholder page and moved the domain there. The DNS records at Namecheap didn't need to change.

### File structure

The `index.html` is intentionally minimal — 30 lines with four script tags and a heading. It exists to prove the CDN dependencies load, not to lay out the page. Phase 1 will build the real structure.

The `bin/dev` script wraps `live-server --port=8000`. This follows the `bin/dev` convention Joshua uses across projects, so every repo starts the same way. `live-server` is a global npm package (already in Joshua's `~/.default-npm-packages`), not a project dependency — consistent with the zero-build-step principle.

### `.claude/settings.json` in version control

We track `.claude/settings.json` (shared project permissions for Claude Code) but gitignore `.claude/settings.local.json` (user-specific overrides). This ensures anyone working with Claude Code on this repo gets the same permission baseline.

---

## 3. Test Coverage & Quality

There is no automated test suite for this project. The PRD explicitly chose a no-build, no-tooling approach — there's no test framework, no linter, no CI pipeline.

**What we validated manually:**

- `bin/dev` starts `live-server` and opens the browser
- The page renders with Tailwind classes applied (confirmed visually)
- Live reload works on file save (confirmed by editing `index.html`)
- GitHub Pages deployment succeeds (confirmed via API: `status: built`)
- `https://joshukraine.com/romans-course/` loads with HTTPS (confirmed in browser)
- DNS records resolve correctly (confirmed via `dig`)

**What's intentionally not tested:**

- Console errors from CDN scripts — the 404s we saw (`favicon.ico`, Chrome DevTools probe) are harmless browser behavior, not script failures
- Umami analytics — the tracking script is present but only fires on the production domain (`joshukraine.com`), not on `localhost`

**Verification commands:**

```bash
bin/dev                    # Start local server, verify page loads
# Visit http://127.0.0.1:8000 and check browser console for script errors
```

**Recommendation for Phase 1:** As the page grows in complexity (Alpine.js data layer, JSON rendering, navigation), consider adding a simple smoke test — even just a shell script that curls the local server and checks for a 200 response and expected content. But don't add a test framework. The project's strength is its simplicity.

---

## 4. Product Tour — Try It Yourself

### Story: Verify the live site

1. Open `https://joshukraine.com/romans-course/` in your browser
2. You should see the course title "Послання до Римлян" centered on a warm stone-colored background (`bg-stone-50`)
3. Below it, the subtitle "Експозиційний курс з вивчення Послання до Римлян"
4. Check the padlock icon — HTTPS should be active
5. Open DevTools → Console. You may see 404s for `favicon.ico` (we haven't added one yet) — that's expected and harmless

### Story: Verify the root domain

1. Open `https://joshukraine.com/`
2. You should see "Coming soon." — this is the placeholder from the `joshukraine.github.io` repo
3. This root page is a separate repo and will eventually become a personal landing page

### Story: Run the local dev server

1. In your terminal, from the project root: `bin/dev`
2. Your browser opens to `http://127.0.0.1:8000`
3. You see the same course title page
4. Open `index.html` in your editor, change the heading text, save
5. The browser reloads automatically — confirm the change appears
6. Ctrl+C to stop the server

### Story: Review the project structure

1. Open the repo in your editor
2. Key files to look at:
   - `CLAUDE.md` — project guidance, guardrails, ambiguity table
   - `README.md` — content update workflow, local dev instructions, license
   - `docs/prd/README.md` → `ROADMAP.md` — Phase 0 is all checked off
   - `docs/prd/CHANGELOG.md` — three deviation entries from today
   - `index.html` — skeleton page with four CDN scripts
   - `bin/dev` — the dev server wrapper
3. Note the empty directories with `.gitkeep` files: `data/`, `handouts/`, `assets/img/`, `assets/css/`
4. Note `snippets/` is in `.gitignore` — this is where Tailwind Plus reference files go locally

### Story: Review the GitHub repo

1. Visit `https://github.com/joshukraine/romans-course`
2. Check the label set: `feat`, `fix`, `chore`, `docs`, `test`, `triage`, `blocked`, `wontfix`, `duplicate`
3. Check closed issues: all 6 are closed with linked PRs
4. Check Settings → Pages: custom domain field should be empty (the domain lives on `joshukraine.github.io`)
5. Visit `https://github.com/joshukraine/joshukraine.github.io` — this is the user site repo that owns the `joshukraine.com` domain

---

## Follow-Up Items

- **Fold CHANGELOG deviations back into PRD files.** We have 3 entries. The CHANGELOG guidelines suggest folding at end-of-phase. Consider updating `01-overview.md` §Tech Stack to reflect Tailwind v4 and Elements, and `02-page-layout-and-content.md` §11 to reflect the user-site-repo approach.
- **Favicon.** The browser requests `favicon.ico` on every page load. Not critical, but a simple favicon would eliminate the 404 noise. Could be added in Phase 1.
- **`joshukraine.github.io` repo is minimal.** It has a bare-bones `index.html` and a CNAME file. If you want a real landing page later, that's tracked in the ROADMAP under "Future / Unscheduled."
