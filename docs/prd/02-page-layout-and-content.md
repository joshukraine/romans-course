# Romans Course Page: Page Layout and Content Structure

This document specifies the page layout, navigation, data model, and content update workflow for the Romans Course Page.

→ See `01-overview.md` for vision, personas, tech stack, and scope boundaries.

---

## 1. Site Structure

The site is a single HTML page (`index.html`) that renders all course content from a JSON data file. There are no subpages, no routing, and no separate views. The entire course — units, lectures, handouts, quizzes — is presented on one scrollable page with collapsible sections for progressive disclosure.

### File layout in the repository

```
romans-course/
├── index.html              ← the single page
├── CNAME                   ← custom domain config for GitHub Pages
├── data/
│   └── course.json         ← all course content metadata
├── handouts/
│   ├── lecture-01-handout-name.pdf
│   ├── lecture-01-another-handout.pdf
│   └── ...
├── assets/
│   ├── img/
│   │   ├── hero.jpg        ← Parthenon hero image (compressed for web)
│   │   └── og-image.jpg    ← Open Graph social sharing preview image
│   └── css/                ← custom CSS if any overrides are needed beyond Tailwind
├── snippets/               ← Tailwind Plus reference snippets (gitignored, local use only)
├── CLAUDE.md
├── README.md
└── .gitignore              ← excludes snippets/, Tailwind Plus files, OS files
```

Handout filenames MUST follow a consistent convention: `lecture-{NN}-{descriptive-slug}.pdf`, where `{NN}` is the zero-padded lecture number and `{descriptive-slug}` is a lowercase kebab-case description. Examples: `lecture-03-katecho-word-study.pdf`, `lecture-04-descent-staircase.pdf`. This convention keeps the `handouts/` directory sorted by lecture order and makes filenames predictable.

---

## 2. Page Layout

The page has four major sections, rendered top to bottom:

### 2a. Header and Navigation Bar

A fixed or sticky navigation bar visible at all times, containing:

- **Course title**: "Послання до Римлян" (or a shorter form if space requires on mobile)
- **Navigation toggle** (mobile): hamburger icon that opens a dropdown or slide-in menu with anchor links to each unit
- **Unit navigation** (desktop): horizontal list of anchor links, one per unit (e.g., "Розділ 1", "Розділ 2", …), enabling quick jump to any unit on the page

The navigation bar SHOULD be visually lightweight — compact, readable, and not competing with the hero area below it. It SHOULD use a warm, muted color palette (beige/blue tones) consistent with the course's visual identity.

### 2b. Hero Area

A prominent visual section immediately below the navigation bar, featuring the Parthenon image as a background or dominant visual element. This serves as the first impression of the course page and anchors the visual identity.

**Hero image:** The source image is a photograph of the Parthenon in Athens by Spencer Davis, sourced from Unsplash (free license). The original is high-resolution and must be compressed and optimized for web delivery. The hero area SHOULD:

- Display the Parthenon image as a CSS background image or full-width `<img>`, cropped to a landscape banner aspect ratio (roughly 3:1 or 4:1 on desktop, taller on mobile)
- Overlay the course title and a brief tagline or subtitle in large, readable text with sufficient contrast against the image (text shadow, semi-transparent overlay, or both)
- Load quickly — the image MUST be compressed (target under 200 KB for the web-optimized version) and SHOULD use responsive image techniques (`srcset` or Tailwind's responsive utilities) to serve a smaller version on mobile
- Not be so tall that it pushes all content below the fold on mobile. The hero sets the tone; the content is the point.

The warm stone tones of the Parthenon (sandy beige, cream, warm grey) and the muted blue of the sky naturally establish the color palette for the rest of the page.

### 2c. Course Introduction

A brief section immediately below the hero area that provides:

- **Course description**: 2–3 sentences explaining what the course covers, who it's for, and the teaching format (monthly lectures, two 45-minute sessions per meeting)
- **Registration link**: a call-to-action button linking to the Google Forms registration form (if registration is still open) or a note that registration is closed
- **Viber community link**: a secondary link inviting visitors to join the Viber community for announcements and discussion

This section is static content that changes infrequently. It MAY be hardcoded in the HTML rather than driven by the JSON, since it's not structured course content.

### 2d. Course Content (the main body)

This is the primary section of the page, rendered from `course.json`. It displays the course organized by units and lectures.

**Unit level:**

Each unit is presented as a collapsible section using the Tailwind Plus Elements `<el-disclosure>` component, which provides built-in accessibility (ARIA attributes, keyboard navigation) and animated expand/collapse behavior. The unit header displays:

- Unit number and title (e.g., "Розділ 1: Вступ і основи")
- Scripture passage covered (e.g., "Римлян 1:1–17")
- Lecture count indicator (e.g., "2 лекції" or "4 лекції")
- Visual indicator of expanded/collapsed state (chevron icon or similar)

The most recent unit SHOULD be expanded by default when the page loads. All other units SHOULD be collapsed. This ensures that returning students see the latest content immediately without scrolling past completed material.

**Lecture level:**

Within an expanded unit, each lecture is displayed as a card or block containing:

- **Lecture number and title** (e.g., "Лекція 3: Гнів Божий — Римлян 1:18")
- **Date** of the lecture, formatted for Ukrainian locale (e.g., "25 січня 2025")
- **Video link**: a prominent button or linked thumbnail that opens the YouTube video. SHOULD open in a new tab. The video is linked, not embedded — embedding would add weight to the page and slow loading when multiple lectures are visible. Embedding MAY be reconsidered later if user feedback suggests it's desired.
- **Handouts**: a list of downloadable PDFs, each showing the handout title with a download icon/button. Clicking downloads the PDF directly (the browser's default behavior for linked PDF files, or using the `download` attribute on the `<a>` tag).
- **Quiz link**: if a quiz exists for this lecture, a button linking to the Google Forms quiz. Opens in a new tab. If no quiz exists yet, this element is simply absent (not shown as "coming soon" or disabled).

**Lecture card states:**

A lecture entry in the JSON MAY have a `status` field to distinguish between:

- **`published`**: video is available, handouts are uploaded, the lecture is fully ready for students. This is the default display state.
- **`upcoming`**: the lecture is scheduled but has not yet been delivered. The card displays the lecture title, date, and passage, but no video or handout links. This gives students a preview of what's coming without dead links.

If the `status` field is absent, the lecture SHOULD be treated as `published`.

### 2e. Footer

A simple footer containing:

- Course teacher name and church/ministry affiliation
- Link to Euro Team Outreach website
- Copyright notice
- Photo attribution: "Hero photo by Spencer Davis on Unsplash" with a link to the original image (https://unsplash.com/photos/ilQmlVIMN4c). Required by Unsplash license terms.
- MAY include a link to the GitHub repository for the technically curious

---

## 3. Navigation Behavior

### Mobile (< 768px)

- The header contains a hamburger icon on the right side
- Tapping the hamburger opens a full-width dropdown or slide-down menu listing all units by number and short title
- Tapping a unit link scrolls to that unit's section and closes the menu
- The menu closes when tapping outside it or tapping the hamburger again

### Desktop (≥ 768px)

- The header displays unit links horizontally
- Clicking a unit link scrolls smoothly to that unit's section
- The hamburger icon is hidden

### Scroll behavior

- Unit anchor links use smooth scrolling (CSS `scroll-behavior: smooth` or Alpine-managed scrolling)
- The header offset MUST be accounted for so that scrolling to a unit anchor doesn't hide the unit title behind the sticky header

---

## 4. Data Model (`course.json`)

The JSON file is the single source of truth for all course content metadata. The HTML template renders whatever this file contains.

### Schema

```json
{
  "course": {
    "title": "Послання до Римлян",
    "description": "Експозиційний курс з вивчення Послання до Римлян...",
    "registrationUrl": "https://forms.google.com/...",
    "viberUrl": "https://invite.viber.com/...",
    "teacher": {
      "name": "Джошуа Стіл",
      "organization": "Euro Team Outreach",
      "organizationUrl": "https://euroteamoutreach.org"
    }
  },
  "units": [
    {
      "number": 1,
      "title": "Вступ і основи",
      "passage": "Римлян 1:1–17",
      "lectures": [
        {
          "number": 1,
          "title": "Огляд курсу та методологія вивчення Біблії",
          "date": "2025-01-25",
          "passage": "Римлян 1:1–7",
          "status": "published",
          "videoUrl": "https://www.youtube.com/watch?v=XXXXXXXXXXX",
          "handouts": [
            {
              "title": "Методологія вивчення Біблії (10 пунктів)",
              "file": "handouts/lecture-01-bible-study-methodology.pdf"
            }
          ],
          "quizUrl": null
        }
      ]
    }
  ]
}
```

### Field reference

**`course` object (top-level metadata):**

| Field | Type | Required | Notes |
| --- | --- | --- | --- |
| `title` | string | MUST | Course title displayed in the header and introduction |
| `description` | string | MUST | 2–3 sentence course description for the introduction section |
| `registrationUrl` | string or null | SHOULD | Google Forms registration link. Set to `null` if registration is closed; the UI hides the registration button when null. |
| `viberUrl` | string or null | SHOULD | Viber community invite link. Set to `null` to hide. |
| `teacher` | object | MUST | Teacher name, organization, and URL for the footer |

**`units` array:**

| Field | Type | Required | Notes |
| --- | --- | --- | --- |
| `number` | integer | MUST | Unit number (1–7). Used for display and anchor IDs. |
| `title` | string | MUST | Unit title in Ukrainian |
| `passage` | string | MUST | Scripture passage range for the unit (e.g., "Римлян 1:1–17") |
| `lectures` | array | MUST | Array of lecture objects within this unit. MAY be empty for future units not yet started. |

**`lectures` array (within a unit):**

| Field | Type | Required | Notes |
| --- | --- | --- | --- |
| `number` | integer | MUST | Continuous lecture number across the entire course (not per-unit). |
| `title` | string | MUST | Lecture title in Ukrainian |
| `date` | string (ISO 8601) | MUST | Lecture date in `YYYY-MM-DD` format. Displayed in Ukrainian locale format on the page. |
| `passage` | string | SHOULD | Scripture passage covered in this specific lecture. May overlap with but be more specific than the unit passage. |
| `status` | string | MAY | `"published"` (default if absent) or `"upcoming"`. Controls which elements are displayed. |
| `summary` | string or null | MAY | 2–3 sentence topic summary displayed below the video thumbnail. Null or absent to hide the section. |
| `videoUrl` | string or null | MUST for published | YouTube video URL. Null for upcoming lectures. |
| `handouts` | array | MUST | Array of handout objects. MAY be empty. |
| `quizUrl` | string or null | MAY | Google Forms quiz URL. Null or absent if no quiz exists for this lecture. |

**`handouts` array (within a lecture):**

| Field | Type | Required | Notes |
| --- | --- | --- | --- |
| `title` | string | MUST | Display title for the handout in Ukrainian |
| `file` | string | MUST | Relative path to the PDF file in the repository (e.g., `"handouts/lecture-03-katecho-word-study.pdf"`) |

---

## 5. Content Update Workflow

Adding materials for a new lecture follows this sequence:

1. **Add PDF files**: drop handout PDFs into the `handouts/` directory, following the naming convention `lecture-{NN}-{descriptive-slug}.pdf`.
2. **Edit `course.json`**: add a new lecture object to the appropriate unit's `lectures` array. Fill in the title, date, passage, video URL, handout references, and quiz URL.
3. **Commit and push**: commit the new PDFs and the updated JSON file. Push to `main`.
4. **Verify**: visit the live site. The new lecture should appear automatically within the correct unit section.

For adding a new unit or updating course metadata (description, registration link), the same pattern applies — edit `course.json`, commit, push.

### Content that lives outside the JSON

The following elements are hardcoded in `index.html` and change infrequently:

- Page `<head>` metadata (title, description, Open Graph tags)
- CDN script/link tags for Tailwind and Alpine
- Footer structure and copyright year
- Any decorative or structural HTML that doesn't vary with course content

---

## 6. Responsive Behavior

### Breakpoints

The site uses Tailwind's default breakpoints:

- **Mobile** (default, < 640px): single-column layout, hamburger navigation, full-width lecture cards
- **Tablet** (sm: 640px+): slightly wider content area, still single-column
- **Desktop** (md: 768px+ or lg: 1024px+): centered content with max-width constraint, horizontal unit navigation in the header

### Content width

The main content area SHOULD have a max-width constraint on larger screens (e.g., `max-w-4xl` or `max-w-5xl`) to maintain readable line lengths. Content is centered horizontally with auto margins.

### Touch targets

All interactive elements (buttons, links, navigation items) MUST meet minimum touch target sizes for mobile use (at least 44×44 CSS pixels per WCAG guidelines). This is especially important for the handout download buttons and navigation links, which students will be tapping on phones.

---

## 7. Visual Design Direction

This section provides design intent, not pixel-precise specifications. The implementation has latitude within these guidelines.

- **Color palette**: warm beiges and muted blues, drawn from the natural tones of the Parthenon hero image — sandy stone, cream, warm grey for primary surfaces and backgrounds; soft, desaturated blue (sky tones from the image) for accents, links, and interactive elements. These tones are consistent with the handout design language already established for the course. Avoid cold grays, stark black-and-white, or saturated primary colors. Sufficient contrast for readability is required (WCAG AA minimum).
- **Typography**: clean, readable sans-serif body text. Ukrainian Cyrillic support is required (Tailwind's default `font-sans` stack includes system fonts with Cyrillic support). Headings MAY use a slightly different weight or size but SHOULD remain in the same font family for consistency.
- **Icons**: minimal icon usage. Download icons for handout links, a play icon for video links, and navigation chevrons. Lucide, Heroicons, or similar icon sets (loaded via CDN or inline SVG) are preferred over icon fonts.
- **Whitespace**: generous spacing between units and lectures. The page should feel airy and scannable, not dense or cluttered.
- **Cards**: lecture entries SHOULD be visually distinct blocks (subtle background, border, or shadow) to create clear separation between lectures within a unit.

---

## 8. Open Graph and Social Sharing

When the course URL is shared in Viber, Telegram, Facebook, or other platforms that render link previews, the page MUST present a meaningful preview. This requires Open Graph meta tags in the `<head>`.

**Required meta tags:**

- `og:title` — course title in Ukrainian
- `og:description` — brief course description in Ukrainian
- `og:image` — URL to the social sharing preview image (the OG image)
- `og:url` — canonical URL of the page
- `og:type` — `website`
- `og:locale` — `uk_UA`

**OG image:** The social sharing preview image SHOULD be derived from the hero image (the Parthenon photograph), optionally with the course title overlaid. The recommended OG image size is 1200×630 pixels. This image is stored in `assets/img/og-image.jpg` and referenced by absolute URL in the meta tag. It can be generated once and updated infrequently.

**Twitter Card tags** (`twitter:card`, `twitter:title`, `twitter:description`, `twitter:image`) SHOULD also be included for broader platform coverage, using the same content as the OG tags.

---

## 9. Analytics

The site uses Umami for privacy-respecting analytics, self-hosted on the Euro Team Outreach infrastructure at `lens.euroteamoutreach.org`.

**Tracking script** (to be placed in `<head>`):

```html
<script defer src="https://lens.euroteamoutreach.org/script.js" data-website-id="81aa77d9-0c13-4656-a7a6-4e8c428484f5"></script>
```

The Umami site is configured with the domain `joshukraine.com`. Page paths (`/romans-course/`, `/romans-course/#lecture-3`, etc.) will appear automatically in the Umami dashboard.

No cookie consent banner is needed — Umami does not use cookies and is GDPR-compliant by design.

---

## 10. Tailwind Plus Reference Snippets

Joshua has a Tailwind Plus subscription with licensed reference snippets. These snippets are used during development as design reference and inspiration but MUST NOT be committed to the public repository.

**Workflow:** A `snippets/` directory at the repo root is included in `.gitignore`. During development, snippet HTML files can be placed in this directory for local reference. Code written for the site (which uses Tailwind classes inspired by the snippets but is original work) is committed normally. The `.gitignore` entry ensures the licensed files never enter version control.

**`.gitignore` entries:**

```
snippets/
.DS_Store
```

---

## 11. Custom Domain Configuration

The custom domain `joshukraine.com` is registered with Namecheap. The domain previously used GoDaddy nameservers from an old configuration that is no longer active. These should be removed and replaced with Namecheap BasicDNS.

### Steps

1. In Namecheap dashboard → Domain → Nameservers: switch from "Custom DNS" to "Namecheap BasicDNS"
2. Wait for nameserver propagation (up to 24–48 hours, often faster)
3. Go to Advanced DNS tab and add the following records:

**A records** (point apex domain to GitHub Pages):

| Type | Host | Value |
| --- | --- | --- |
| A | @ | 185.199.108.153 |
| A | @ | 185.199.109.153 |
| A | @ | 185.199.110.153 |
| A | @ | 185.199.111.153 |

**CNAME record** (point `www` subdomain):

| Type | Host | Value |
| --- | --- | --- |
| CNAME | www | joshukraine.github.io |

4. Create a **user site repository** (`joshukraine.github.io`) with a `CNAME` file containing `joshukraine.com` and a placeholder `index.html`. The custom domain MUST be configured on the user site repo, not on the `romans-course` project repo. This is because GitHub Pages serves project repos at `/<repo-name>/` under the user site's domain — setting the domain on the project repo itself would serve content at the domain root instead of `/romans-course/`.
5. In the `joshukraine.github.io` repository → Settings → Pages: set custom domain to `joshukraine.com` and enable "Enforce HTTPS"
6. The `romans-course` repository does NOT have a `CNAME` file or custom domain setting — it is served automatically at `joshukraine.com/romans-course/` as a project page under the user site.
7. Wait for DNS propagation and GitHub's automatic SSL certificate provisioning (can take up to an hour)

### Verification

After configuration, verify:

- `https://joshukraine.com/romans-course/` loads the course page
- `https://www.joshukraine.com/romans-course/` redirects correctly
- HTTPS is active (padlock icon in browser)
- The root `https://joshukraine.com/` shows the user site placeholder page (until a root landing page is built — this is expected and acceptable for now)

---

## Resolved Decisions

**Video display: linked thumbnails.** Each lecture shows a YouTube thumbnail image that links to the video, opening in a new tab. This provides visual context without the page weight of embedded iframes. YouTube provides predictable thumbnail URLs given a video ID (e.g., `https://img.youtube.com/vi/{VIDEO_ID}/mqdefault.jpg`). May revisit in favor of embedding if student feedback warrants it.

**Anchor IDs: supported.** Each lecture receives an anchor ID (e.g., `#lecture-12`) enabling direct links to be shared in Viber. Implementation must handle the Alpine.js dynamic rendering timing — scroll to the hash target after content has rendered, not on initial DOM load.

**Date formatting: `Intl.DateTimeFormat`.** Dates stored as ISO 8601 in JSON, formatted at render time using `new Intl.DateTimeFormat('uk-UA', { day: 'numeric', month: 'long', year: 'numeric' })`. No external library needed.

---

## Open Items

**OI-1: Future quiz structure.**
Quizzes are currently planned as Google Forms, linked externally. If the quiz model changes in the future (e.g., self-hosted quizzes, multiple quizzes per lecture), the `quizUrl` field may need to become an array of quiz objects. For now, a single nullable string is sufficient. Revisit when the quiz structure is finalized.
