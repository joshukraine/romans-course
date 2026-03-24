# Romans Course Page — Product Requirements Document

This directory contains the modular PRD for the Romans Course Page. Each file is a self-contained specification that can be referenced independently during implementation.

## How to Use This PRD

**For Claude Code / AI-assisted implementation:** Reference the specific PRD section relevant to your current task. Start here to find the right file, then consult the referenced section for requirements.

**For human reviewers:** Read `01-overview.md` for context, then `02-page-layout-and-content.md` for the feature spec.

## File Index

| File | Contents | Consult When... |
| --- | --- | --- |
| `CHANGELOG.md` | Material deviations from the PRD discovered during implementation | Reviewing or recording a "never silently deviate" decision |
| `ROADMAP.md` | Implementation phases, progress tracking | Planning sprints, checking phase boundaries |
| `01-overview.md` | Vision, personas, design principles, tech stack | Onboarding to the project, understanding goals |
| `02-page-layout-and-content.md` | Page layout, navigation, data model (`course.json` schema), content update workflow, responsive behavior, visual design | Building any part of the page, adding or modifying content structure |

## Conventions

- **"MUST", "SHALL", "REQUIRED"** — non-negotiable for MVP launch.
- **"SHOULD"** — expected for MVP unless a specific technical constraint prevents it.
- **"MAY"** — nice to have; implement if straightforward, otherwise defer.
- **"TBD"** — explicitly unresolved; document in an open items section with context and resolution guidance.
- **Cross-references** use the format `→ See 02-page-layout-and-content.md §3 "Navigation Behavior"` to point to other PRD sections. Always include the filename and quoted heading.
