# Romans Course Page: Project Overview

## Vision

**Romans Course Page** is a static, mobile-friendly website that serves as the central hub for all materials in Joshua Steele's expository course on the Book of Romans, taught at an evangelical church in Lviv, Ukraine. The site gives students a single URL where they can find lecture videos, download handouts, access quizzes, and track the course structure as it develops over 12–14 months.

---

## Users and Personas

### Enrolled Student (primary user)

- Currently attending the Romans course in Lviv
- Wants digital copies of handouts distributed during class
- Needs links to quizzes (Google Forms) after each lecture
- May access the site primarily on a mobile phone via a link shared in the Viber community
- Ukrainian-speaking; varying levels of technical comfort
- Needs to: find the handouts and quiz for a specific lecture, watch or rewatch a lecture video, see what's coming next in the course

### Absent or Remote Learner

- Enrolled in the course but missed one or more sessions, or following along remotely
- Needs to catch up on missed lectures: video, handouts, and any associated quiz
- Navigates the site by unit and lecture number to find what they missed
- Needs to: quickly locate a specific lecture's materials, download all handouts for a session, watch the lecture video

### Prospective Student or Visitor

- Discovered the course through word of mouth, social media, or a shared link
- Wants to understand what the course covers, how it's structured, and what materials are available
- May be evaluating whether to join the course
- Needs to: see the overall course structure, get a sense of the teaching style (via videos), understand how to register or join

---

## Design Principles

- **Single source of truth**: One URL contains everything. Students should never need to search through Viber messages, YouTube playlists, or email threads to find a resource — it's all on the course page.
- **Mobile-first**: The primary access pattern is a Ukrainian student tapping a link on their phone from Viber. The site must look good and work well on mobile screens before anything else.
- **Low maintenance**: Adding a new lecture's materials (video link, handout PDFs, quiz link) should be a quick, predictable task — not a development project. The update workflow should be: edit a file, commit, push, done.
- **Progressive content**: The site grows with the course. It should handle the current state (a few lectures completed) and the eventual state (60+ lectures across seven units) equally well, without requiring structural redesign.
- **Clarity over cleverness**: Clean layout, obvious navigation, readable text. No unnecessary interactivity or visual complexity. The content is the point.

---

## Tech Stack

| Layer | Choice |
| --- | --- |
| Site type | Static HTML (no build step, no SSG) |
| Hosting | GitHub Pages |
| CSS | Tailwind CSS v4 via CDN (`@tailwindcss/browser@4`) |
| UI components | Tailwind Plus Elements via CDN (`@tailwindplus/elements@1`) — disclosures, dropdowns, dialogs |
| Data binding | Alpine.js via CDN (`<script defer>` tag) — JSON fetch, templating, conditional rendering |
| Asset storage | PDF handouts stored directly in the repository |
| Video hosting | YouTube (embedded or linked) |
| Quizzes | Google Forms (linked externally) |
| Domain | joshukraine.com (custom domain pointed at GitHub Pages) |
| Version control | Git / GitHub (public repository) |

### Why These Choices

- **No build step**: The entire site is plain HTML, CSS (via Tailwind CDN), and JS (via Alpine CDN). There is no `npm`, no compilation, no CI pipeline required. Push to `main` and the site is live within minutes.
- **Tailwind CSS v4 CDN**: Provides the utility-class styling workflow Joshua is familiar with, loaded via a single `<script>` tag. v4 uses `@theme` directives in `<style type="text/tailwindcss">` for custom color palettes (replacing the v3 `tailwind.config` JS object). No PostCSS, no config file, no purging — the CDN handles everything.
- **Tailwind Plus Elements CDN**: Provides interactive UI component behavior (disclosures, dropdowns, dialogs) from the Tailwind Plus snippet library, with built-in accessibility (ARIA, keyboard nav, focus management). Loaded via a single `<script type="module">` tag. Requires a Tailwind Plus license.
- **Alpine.js CDN**: Handles data binding — fetching `course.json`, looping over units/lectures, conditional rendering, date formatting. Does not handle UI component behavior (that's Elements). One `<script defer>` tag, declarative HTML attributes, done.
- **GitHub Pages**: Free, reliable, no subscription dependency, automatic deployment on push, custom domain support. The repo is public since all content is already distributed to students.
- **PDFs in the repo**: Handout PDFs are small (typically under 2 MB each). Storing them in the repo keeps everything in one place, versioned, and served directly by GitHub Pages. No external storage service needed.

### What We Are NOT Using

- **FlyonUI or DaisyUI**: FlyonUI requires npm and a build step, which conflicts with the zero-build-step principle. DaisyUI's CDN option exists but adds an unnecessary abstraction layer for a single-page site. Raw Tailwind utilities provide sufficient styling control.
- **Hugo, Jekyll, or any SSG**: The site is simple enough that static HTML is the right tool. An SSG would add build complexity, templating conventions, and deployment configuration that aren't justified by the scope.
- **AWS S3, Cloudflare R2, or external storage**: Not needed while assets are PDFs and links. If the project ever hosts large video files directly (rather than linking to YouTube), this decision should be revisited.
- **Any backend or database**: There is no dynamic content, no user accounts, no server-side logic. Everything is static files served by GitHub Pages.

---

## Content Structure

The course is organized hierarchically:

- **Course** → contains **Units** (7 total)
  - **Unit** → contains **Lectures** (variable count per unit; continuous numbering across the course)
    - **Lecture** → contains:
      - YouTube video link
      - Zero or more PDF handouts
      - Zero or one quiz link (Google Forms)
      - Lecture date
      - Brief description or topic summary

### Content currently available

- **Unit 1: Introduction and Foundations (Romans 1:1–17)** — Lectures 1 and 2 completed
- **Unit 2: The Wrath of God Revealed (Romans 1:18–3:21)** — Lectures 3 and 4 most recently delivered (video editing in progress)

Content will be added progressively as lectures are delivered, roughly two lectures per month (one teaching session with two 45-minute lectures).

---

## Development Conventions

- **Git**: Conventional Commits for messages and branch naming, following the label taxonomy in `~/.claude/docs/label-taxonomy.md`.
- **Code style**: Standard HTML/CSS conventions. Tailwind utility classes for styling. Alpine.js `x-` directives for interactivity.
- **Changes**: Small, incremental, and isolated. Content updates (adding a lecture) should be separate commits from structural changes.
- **CLAUDE.md**: Project-level guidance for Claude Code will live in `CLAUDE.md` at the repo root.

---

## Out of Scope for MVP

- Multi-language support (English version of the site)
- Student registration or enrollment management
- User accounts or authentication
- Comment sections or discussion features
- Automated quiz grading or progress tracking (quizzes are handled entirely by Google Forms)
- Direct video hosting (videos live on YouTube)
- Email notifications for new content
- Search functionality
- The `joshukraine.com` root landing page (separate project/repo; the course page will work at `/romans-course/` regardless of whether a landing page exists)
