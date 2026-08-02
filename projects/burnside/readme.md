# Better Burnside

> **Status:** 🟢 active · **Updated:** 2026-07-18
> Status legend: 🟢 active · 🟡 paused · ⚪ idea · ✅ done · ⚫ retired

**One-liner:** Campaign site + citizen coalition for the West Burnside corridor (bridge → Providence Park) — evidence, plan, and calls to action built around the Earthquake Ready Burnside Bridge closure window.

## Links
- **Code:** `~/dev/burnside` · [remote](https://github.com/z-br/burnside) (pushed 2026-07-18)
- **Live:** not deployed yet — target `http://burnside.zebraproject.org` via ship-webapp flow
- **Other:** Claude Project "Portland Policy" holds the research docs (vacancy inventory, bridge deep-research, downtown status board)

## Stack
Astro 5 · TypeScript · static output, CSS inlined · `serve` for prod (Nixpacks: build → start)

## Overview
One-page campaign prototype: 16-address storefront vacancy inventory + SVG corridor map,
verified bridge-closure timeline (closure postponed indefinitely Oct 2025 — the "prep years"
framing), 13-vector downtown status board, coalition roster (PSU/Better Block + 3 NAs at the
table; Goose Hollow, Timbers, MAC, Powell's needed), and audience-specific CTAs.
All content in `src/data/site.ts`. Signup form not wired. Name/domain TBD.

## Related
[[roselight]] (Portland news), [[wallacecourts]] (park civic site)

## Worklog
The code repo keeps a timestamped `WORKLOG.md` at its root (seeded), per
[`docs/conventions/worklog.md`](../../docs/conventions/worklog.md); `AGENTS.md` +
`CLAUDE.md` are set up.
