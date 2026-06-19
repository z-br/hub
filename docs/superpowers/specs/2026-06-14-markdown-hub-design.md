# Markdown Hub — Design

**Date:** 2026-06-14
**Repo:** `~/dev/hub` (z-br/hub)

## Purpose
A markdown-centric command center for all ongoing projects, with a cross-project
TODO view. Plain markdown only — no tooling, renders on GitHub and in any editor.

## Model: Hybrid
The hub is the "brain." Code lives in its own repos elsewhere under `~/dev`; the
hub holds overview, notes, ideas, and the aggregated task view, linking out to code.

## Structure
```
/
├── README.md            # Landing page + project index (grouped by status)
├── todo.md              # Root ROLLUP of cross-cutting / high-priority tasks
├── inbox.md             # Quick capture, triaged later
├── docs/superpowers/specs/   # design docs
└── projects/
    ├── _TEMPLATE/       # copy to start a project: readme/todo/notes/ideas.md
    └── <slug>/          # one folder per project, same 4 files
```

## Conventions
- **Folders:** lowercase-kebab (`familyhub-mobile`).
- **Status:** each `readme.md` opens with a status line — 🟢 active · 🟡 paused ·
  ⚪ idea · ✅ done · ⚫ retired — surfaced in the root index.
- **Task source of truth:** each project's `todo.md`. Root `todo.md` is a
  hand-curated rollup linking back. Kept in sync by hand.
- **Task format:** GitHub task lists (`- [ ]`).
- **Cross-links:** `[[slug]]` wiki-style between projects.

## Seeded projects
- **Active:** dreampdx, homepage, familyhub, familyhub-mobile, famfam
- **Paused:** wallacecourts, hrv (absorbs CPAP reference repos OSCAR-code /
  CPAP-Exporter / cpap-lib as related tools, not separate projects)
- **Older but relevant:** menagerie, storyshop, cookthebooks, blog
- **Retired:** project-hub (empty predecessor, safe to delete)

Project pages are seeded with auto-detected stack + a one-liner, marked
"refine me" — the owner fills in real status/notes over time.

## Out of scope (YAGNI)
No build step, no scripts, no front-matter database, no automation to sync the
rollup. If a sync pain emerges later, revisit then.
