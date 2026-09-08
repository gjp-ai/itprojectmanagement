# UOB IT PMO Kanban Board (Demo)

A single-file Kanban board demo built for an internal IT PMO training/demo use case. Everything — markup, styles, and logic — lives in [index.html](index.html).

**Live demo:** https://gjp-ai.github.io/itprojectmanagement/

## Running it

No build step, no server, no dependencies. Just open the file:

```bash
open index.html
```

or double-click it in Finder/Explorer.

## What it does

- Drag-and-drop Kanban board with four fixed columns: Backlog, In Progress, Blocked, Done
- Add, move, and delete tasks; each task has a project, assignee, priority, and due date
- Priority-driven card styling (Critical/High/Medium/Low)
- Overdue tasks are flagged automatically (due date in the past and not Done)
- Client-side filtering by project, assignee, and priority
- Optional email notification on new task creation via [FormSubmit](https://formsubmit.co) (no backend required)

## Constraints

- **Vanilla only** — plain HTML/CSS/JS, no frameworks, no build tooling, no npm packages
- **No external resources** — no CDN scripts, no web fonts, no image files
- **No persistence** — board state lives only in memory; refreshing the page resets it to the seeded demo data
- **No real UOB branding** — this is an internal demo, not an official system

See [CLAUDE.md](CLAUDE.md) for full architecture notes.

## Deployment

Pushing to `main` triggers the GitHub Actions workflow in [.github/workflows/pages.yml](.github/workflows/pages.yml), which deploys the repo root to GitHub Pages.
