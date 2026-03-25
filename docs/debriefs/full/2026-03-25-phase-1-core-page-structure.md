# Romans Course Page — Phase 1: Core Page Structure Debrief

- **Date:** 2026-03-25
- **Scope:** Phase 1 — Core Page Structure (all 23 roadmap items + follow-up fix #24)
- **PRD references:** `02-page-layout-and-content.md` §2–§9; `01-overview.md` §Tech Stack
- **Issues closed:** #15, #16, #17, #18, #19, #20, #24
- **PRs merged:** #21, #22, #23, #25, #26, #27, #28
- **Additional:** `/simplify` review applied, `/drift-check` passed, 3 CHANGELOG deviations folded back into PRD

---

## 1. What We Built (and Why It Matters)

Phase 1 transformed the skeleton page from Phase 0 into a fully functional, styled course page. A student can now visit the site and see the complete course structure — hero image, course description, collapsible unit sections, lecture cards with video thumbnails, handout downloads, quiz links, and a footer with proper attribution. The page is responsive, accessible, and ready for real content.

**What's in place:**

- **Data layer** (`data/course.json` + Alpine.js) — the single source of truth for all course content, fetched and rendered reactively on page load
- **Hero area** — compressed Parthenon background image (171 KB) with dark overlay and white course title
- **Sticky navigation** — course title left, data-driven unit anchor links right (desktop), Elements `<el-dialog>` hamburger menu (mobile)
- **Course introduction** — description, registration button, and Viber community link (conditionally rendered from JSON)
- **Course content** — collapsible unit sections via `<el-disclosure>`, lecture cards with YouTube thumbnails, handout download links, quiz buttons, and upcoming lecture status badges
- **Deep linking** — anchor IDs (`#unit-N`, `#lecture-N`) with hash-based scroll after Alpine renders, auto-expanding collapsed parent units
- **Footer** — teacher/org attribution, Unsplash photo credit, copyright, GitHub link
- **Social sharing** — Open Graph and Twitter Card meta tags, 1200×630 OG image
- **Visual design** — custom `sand-*` / `sky-blue-*` color palette via Tailwind v4 `@theme`, consistent typography, card styling, generous whitespace

**What was intentionally deferred to Phase 2:**

- Real course content (all data is placeholder)
- Actual handout PDFs
- Real YouTube video URLs
- Quiz URLs
- Mobile device testing on real hardware (done via Chrome DevTools only)
- Social sharing preview verification (OG tags are in place but untested with real sharing)

**Follow-up work completed beyond the original 6 issues:**

- Issue #24 — auto-expand parent unit when deep-linking to a lecture inside a collapsed section
- `/simplify` review — extracted helper functions, added fetch error handling, fixed HTML semantics, added performance optimizations
- `/drift-check` — verified alignment with PRD, folded 3 CHANGELOG deviations back into PRD files

---

## 2. Architecture & Design Decisions

### The Alpine.js data layer (`index.html:86–130`)

The entire app state lives in a single `x-data` object on `<body>`. This is deliberate — for a single-page site with one JSON data source, there's no benefit to splitting state across multiple Alpine components. The shape is minimal:

```javascript
{
  course: {},       // Top-level course metadata (title, description, teacher, URLs)
  units: [],        // Array of unit objects, each containing lectures
  loading: true,    // UI flag for loading spinner
  error: false,     // UI flag for fetch failure
  dateFmt: ...,     // Hoisted Intl.DateTimeFormat instance (single allocation)
  videoId(url),     // YouTube URL → video ID extraction
  pluralize(n, ...) // Correct Slavic plural forms (1/few/many)
}
```

**Why `fetch` instead of embedding JSON in the HTML?** The PRD's architectural guardrail (#2) says "all course data lives in `course.json`." This separation means content updates never touch the HTML — you edit one JSON file, commit, push, done. It also means the JSON schema is testable independently.

**Why helper methods on `x-data` instead of separate JS?** The project's #1 guardrail is "no build step." We can't import modules or create separate `.js` files that would need bundling. Alpine.js `x-data` is the natural place for shared logic in this architecture. The three helpers (`dateFmt`, `videoId()`, `pluralize()`) were extracted during the `/simplify` review from inline expressions that were repeated or hard to read.

### The three-library stack: Tailwind v4 + Elements + Alpine.js

This was the biggest architectural decision in Phase 1 — adding Tailwind Plus Elements alongside Alpine.js. Each library has a clear lane:

- **Tailwind CSS v4** — styling only. The `@theme` directive defines the custom `sand-*` and `sky-blue-*` color scales. No configuration JS.
- **Tailwind Plus Elements** — UI component behavior. `<el-disclosure>` for collapsible unit sections (with built-in ARIA, keyboard nav, animated transitions). `<el-dialog>` for the mobile navigation menu. These are custom HTML elements that handle all the interactive plumbing — we don't write toggle logic, focus trapping, or escape-key handlers.
- **Alpine.js** — data binding and templating. Fetches JSON, loops over units/lectures (`x-for`), conditionally shows/hides elements (`x-if`, `x-show`), formats dates, and renders text (`x-text`).

**Why not just Alpine.js for everything?** We could have implemented collapsible sections with `x-show` and a manual `open` state variable. But Elements gives us disclosure behavior for free — including accessibility features (ARIA `aria-expanded`, keyboard support) that we'd have to hand-code otherwise. The tradeoff is a dependency on a commercial library, but since Joshua already has a Tailwind Plus license and the snippets serve as design reference, this was the lower-risk path.

**What we considered and rejected:**

- **Embedding videos instead of linking.** PRD §2d's Resolved Decisions section explicitly chose linked thumbnails over iframes. Each YouTube embed adds ~500 KB of iframe overhead. With 60+ lectures eventually, that's 30+ MB of iframes on a single page. Linked thumbnails are 5–15 KB each.
- **CSS gradient blur decorations from the hero snippet.** The Tailwind Plus hero snippet includes decorative gradient blobs. We removed them — they use indigo/pink tones that clash with the warm beige/blue palette, and they add visual noise behind the Parthenon image.
- **`@apply` for repeated link classes.** The `/simplify` review flagged `text-sky-blue-600 hover:text-sky-blue-500` appearing 7 times. We decided against extracting it to a `.link-accent` class — in a Tailwind project, 7 utility repetitions is normal, and `@apply` adds a layer of indirection that makes the HTML harder to scan.

### Deep linking with `$nextTick` and disclosure expansion (`index.html:114–126`)

This was the trickiest timing problem. The flow is:

1. Alpine fetches `course.json` → sets `loading = false` → DOM renders
2. `$nextTick` fires → checks `window.location.hash`
3. If hash targets a lecture inside a collapsed disclosure → remove `hidden` attribute from the `<el-disclosure>`
4. Scroll to the target element

The coupling to `<el-disclosure>`'s `hidden` attribute (line 120–121) is an implementation detail of the Elements library. If Elements changes its collapse mechanism, this breaks. We accepted this risk because: (a) it's a one-time init path, not a hot loop; (b) Elements is a stable commercial library unlikely to change its API; (c) the alternative — reimplementing disclosure in Alpine.js — would be more code with worse accessibility.

### Ukrainian pluralization (`index.html:98–102`)

Ukrainian follows Slavic plural rules that depend on the last two digits, not just the number:

- 1 (not 11): лекція (singular)
- 2–4 (not 12–14): лекції (few)
- Everything else: лекцій (many)

The initial implementation used a simplified `n < 5` check that would break for 11, 12, 21, etc. The `/simplify` review caught this and we extracted a correct `pluralize()` function. This matters because the course will eventually have 60+ lectures — unit lecture counts could plausibly reach into the teens or twenties.

### Custom color palette (`index.html:43–65`)

We defined two 10-shade color scales in the Tailwind v4 `@theme` directive:

- `sand-*` — warm beige tones drawn from the Parthenon stone (sandy cream for backgrounds, darker shades for text)
- `sky-blue-*` — muted blue tones drawn from the Parthenon sky (for links, buttons, interactive accents)

This gives us the PRD's §7 "warm beiges and muted blues" palette as first-class Tailwind colors. We can write `bg-sand-100` or `text-sky-blue-600` like any built-in color. The alternative was using Tailwind's built-in `stone` scale, but it was too cool/grey for the warm Mediterranean aesthetic the PRD describes.

---

## 3. Test Coverage & Quality

There is no automated test suite. This is a static HTML site with no build step, no test framework, and no linter. All validation is manual.

**What we verified manually during Phase 1:**

- Data layer: JSON loads, course title renders, unit/lecture counts correct
- Hero: background image loads, title text visible with contrast
- Navigation: desktop unit links scroll to correct sections, mobile hamburger opens/closes
- Course content: units expand/collapse, lecture cards render with all field types
- Video thumbnails: YouTube thumbnail loads from video ID extraction
- Handout links: download attribute present, click triggers download
- Quiz button: visible when `quizUrl` is set, hidden when null
- Upcoming lectures: show only title/date/passage, no media links
- Deep linking: `#lecture-1` expands collapsed parent unit and scrolls
- Footer: teacher name from JSON, all attribution links work
- Responsive: mobile viewport looks correct in Chrome DevTools
- Touch targets: all interactive elements ≥ 44px

**What the `/simplify` review caught and fixed:**

- No fetch error handling (added `.catch()` with Ukrainian error message)
- `Intl.DateTimeFormat` created per card per render (hoisted to single instance)
- YouTube regex inline hack (extracted to `videoId()` helper)
- Incorrect Ukrainian pluralization for numbers 11+ (extracted `pluralize()` with Slavic rules)
- Missing hero image dimensions (added `width`/`height`/`fetchpriority`)
- No preconnect hints for CDN origins (added for jsdelivr and YouTube)
- Invalid HTML: `<dt>`/`<dd>` without `<dl>` wrapper (fixed)

**Verification commands:**

```bash
bin/dev                    # Start local server, visit http://127.0.0.1:8000
# Test deep linking: http://127.0.0.1:8000/#lecture-1
# Test mobile: Chrome DevTools → toggle device toolbar → 375px width
```

**Gaps and recommendations:**

- No automated smoke test exists. A simple script that fetches the local page and checks for expected content (course title, unit headers) would catch regressions. Worth adding if the page grows more complex.
- Social sharing previews (OG tags) are untested with real sharing platforms. This is a Phase 2 verification item.
- No real-device mobile testing. Chrome DevTools is a good approximation but doesn't catch touch behavior, Viber in-app browser quirks, or actual network conditions.

---

## 4. Product Tour — Try It Yourself

Start the dev server:

```bash
bin/dev
```

### Story: Student visits the course page

1. Visit `http://127.0.0.1:8000`
2. You see the Parthenon hero image with "Послання до Римлян" in white text
3. Below: the course description paragraph, a blue "Реєстрація" button, and a "Viber-спільнота →" link
4. Scroll down: two unit sections. **Unit 2 is expanded** (most recent), Unit 1 is collapsed
5. In Unit 2: Lecture 3 has a YouTube thumbnail with a play button overlay, a handout download link, and no quiz button. Lecture 4 shows "Очікується" (upcoming) — title and date only, no video/handout/quiz
6. Click the Unit 1 header (or the + icon) — it expands to show Lectures 1 and 2
7. Lecture 1 has a video thumbnail, one handout, and a "Пройти тест" quiz button
8. Lecture 2 has a video thumbnail and two handouts, no quiz
9. Scroll to the footer: "Джошуа Стіл · Euro Team Outreach", photo credit, copyright, GitHub link

### Story: Student receives a shared lecture link

1. Visit `http://127.0.0.1:8000/#lecture-1`
2. The page loads, Unit 1 **auto-expands** (even though it's not the most recent), and scrolls to Lecture 1's card
3. The lecture card is immediately visible with the video thumbnail and handout

### Story: Desktop navigation

1. Widen the browser to ≥1024px
2. The sticky nav shows "Послання до Римлян" on the left, "Розділ 1" and "Розділ 2" links on the right
3. Click "Розділ 1" — the page smooth-scrolls to Unit 1's header, accounting for the sticky nav offset
4. The hamburger icon is hidden on desktop

### Story: Mobile navigation

1. Narrow the browser to <768px (or use Chrome DevTools device toolbar)
2. The nav shows only the course title and a hamburger icon
3. Click the hamburger — a slide-in panel appears from the right with "Розділ 1: Вступ і основи" and "Розділ 2: Гнів Божий об'явлений"
4. Tap "Розділ 1" — the menu closes and the page scrolls to Unit 1

### Story: Error state

1. Rename `data/course.json` to `data/course.json.bak` temporarily
2. Visit `http://127.0.0.1:8000`
3. After a brief loading spinner, you see: "Не вдалося завантажити дані курсу. Спробуйте оновити сторінку."
4. Rename the file back and refresh — everything works again

### Story: Conditional content visibility

1. Open `data/course.json` in your editor
2. Set `"registrationUrl": null` — save and check the page. The "Реєстрація" button disappears.
3. Set `"viberUrl": null` — save. The "Viber-спільнота" link disappears. The entire CTA section hides.
4. Restore both URLs and save.

---

## Follow-Up Items

- **Tailwind CDN is parser-blocking.** The `@tailwindcss/browser@4` script has no `defer` or `async` — it must compile CSS before the page can paint. This is an inherent cost of the no-build-step architecture. If First Contentful Paint becomes a concern on mobile, precompiling to a static CSS file would be the fix (but requires a build step). Monitor via Umami analytics once the site is live with real traffic.
- **No responsive `srcset` for hero image.** Mobile users download the full 1600px image. A future optimization would generate an 800px variant and use `srcset`. Low priority — 171 KB is acceptable.
- **Favicon still missing.** Browsers request `favicon.ico` on every page load, generating 404s. A simple favicon would clean up the console noise.
- **OG image has no text overlay.** The current OG image is a plain crop of the Parthenon photo. Joshua wants a stylized version with course title text, similar to YouTube thumbnails. Deferred to a future update.
