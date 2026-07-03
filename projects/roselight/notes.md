# The Rose Light — Notes

> Dated journal: decisions, progress, dead-ends. Newest on top.

## 2026-07-03
- **Events direction adopted** via 4-agent debate + arbitrator: automated
  "This Weekend: 10 Picks" module (homepage + digest), consent-based feed
  sources only, no scrapers, no human curation loop. Full public calendar
  rejected (SEO thesis dead; incumbents too strong); thin `/events` page is a
  conditional Phase 3. → [events-plan](events-plan.md) ·
  [March plan archived](reference/PLAN-event-calendar-2026-03.md) · roselight
  PR #16 closed. Phase 0 = [Everout outreach email](everout-outreach-draft.md)
  (draft, awaiting Greg's send).
- Rebased all 4 design branches onto current main for preview:
  PR #8 (bold editorial) · #48 (rose-first) · #49 (brutalist) · #22 (wacky +
  theme system) → previews at `<PR#>.theroselight.xyz`. Conflicts resolved
  design-side; **evaluation only, do not merge as-is**.

## 2026-07-02
- **Branch triage: 19 → main + parked.** Deleted 7 already-merged + 4
  superseded (archive/* tags kept: coolify, r2-backup-cleanup, deploy-hang,
  jsdoc-backfill, docs-backfill). Landed the orphaned May offline-refresh fix
  (PR #46) and salvaged still-valid lib JSDoc (PR #47).
- **Marketing polish shipped** (PR #32): branded OG share cards, per-post
  ShareButton, and the new tagline — "Every Portland story from N local
  sources — free, no paywall, no national noise" (live feedCount). Pitch-strip
  banner cut per Greg (too loud). Old tagline still in `<title>`/meta + README
  — decide if "Light on what matters" is fully retired.
- Removed a stale `.claude` worktree that was polluting vitest discovery.

## 2026-06-14
- Added to the hub. Stack auto-detected: Vite · React · TypeScript · Prisma.
