# Tennis Workout Builder

> **Status:** 🟡 built, not yet deployed · **Updated:** 2026-08-01
> Status legend: 🟢 active · 🟡 paused · ⚪ idea · ✅ done · ⚫ retired

**One-liner:** Free, no-accounts tennis workout builder — wizard, remix, phone-first companion mode, printables, share links + gallery.

## Links
- **Code:** `~/dev/tennisworkout` · [remote](https://github.com/z-br/tennisworkout)
- **Live:** https://tennis.zebraproject.org (planned — not deployed yet)
- **Other:** design spec at `docs/superpowers/plans/` and implementation plan in-repo (`docs/superpowers/plans/2026-07-31-tennis-workout-builder.md`)

## Stack
React Router (framework mode, SSR) · TypeScript · Tailwind CSS v4 · Zod · postgres.js · idb (IndexedDB) · Vitest · Playwright

## Overview
Wizard-driven plan generator with remix, a phone-first "today" companion view with session tracking, print-friendly plan/warm-up-card views, and public share links into a moderated gallery. No accounts — plans and session history are local-first (IndexedDB, export/import); Postgres is used only for the publish/gallery/moderation surface.

Built in one session (2026-07-31 → 2026-08-01) via an 18-task SDD plan executed through subagents: exercise library, plan-generation/ramp logic, local-first store, moderation, publish server modules, 7 routes, a seeded founding plan, and a 103-test suite (92 unit + 11 Postgres-backed) plus a Playwright smoke test — all green. Not yet pushed/deployed; that's next.

## Related
_None yet._

## Worklog
_The code repo keeps a timestamped `WORKLOG.md` of agent activity at its root._
