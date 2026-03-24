# Romans Course Page

A static, mobile-friendly website serving as the central hub for all materials in Joshua Steele's expository course on the Book of Romans, taught at an evangelical church in Lviv, Ukraine. Students use the site to find lecture videos, download handouts, and access quizzes.

**Tech stack:** Plain HTML, Tailwind CSS (CDN), Alpine.js (CDN), hosted on GitHub Pages. No build step — push to `main` and the site is live.

**Live site:** [joshukraine.com/romans-course/](https://joshukraine.com/romans-course/)

## Content Structure

The course is organized hierarchically:

- **Course** → 7 units
  - **Unit** → multiple lectures (numbered continuously across the course)
    - **Lecture** → YouTube video, PDF handouts, optional quiz link

All content metadata lives in `data/course.json`. The HTML page reads this file and renders everything dynamically with Alpine.js.

## Adding a New Lecture

1. Drop handout PDFs into `handouts/`, following the naming convention `lecture-{NN}-{descriptive-slug}.pdf`
2. Edit `data/course.json` — add a lecture object to the appropriate unit's `lectures` array with title, date, video URL, handout references, and quiz URL
3. Commit and push to `main`
4. Verify on the live site — the new lecture appears automatically

See `docs/prd/02-page-layout-and-content.md` §5 for the full content update workflow.

## Local Development

Requires [Node.js](https://nodejs.org/) and `live-server` installed globally:

```bash
npm install -g live-server
```

Then from the project root:

```bash
live-server --port=8000
```

This opens the site in your default browser and automatically reloads when files change. No project-level dependencies to install — the site uses CDN-loaded scripts only.

## Documentation

Detailed product requirements and implementation roadmap live in `docs/prd/`. Start with [docs/prd/README.md](docs/prd/README.md) for the file index.
