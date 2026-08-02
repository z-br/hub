# Civic Stack (Portland)

> **Status:** 🟢 active · **Updated:** 2026-07-18
> Status legend: 🟢 active · 🟡 paused · ⚪ idea · ✅ done · ⚫ retired

**One-liner:** The umbrella for GZ's Portland civic work — three assets, one loop: The Rose Light (sensing) → Portland Policy research (sense-making/verification) → Better Burnside (action). This folder is the cross-project map and state; per-asset detail lives in each project.

## The map
- **[[roselight]]** — theroselight.org. Eyes: 22-source ingest, Claude scoring, weekly digest. Gets: `civic-recovery` tag layer, topic feeds, signup piping (see todo).
- **[[burnside]]** — Better Burnside campaign (repo `~/dev/burnside`). Hands: corridor evidence + coalition + asks. Show-to-champions DRAFT until consent.
- **`research/` (in this folder)** — Memory: ALL verified research docs live HERE (lessons, vacancy inventory, bridge research, architecture, momentum plan, website plan). Cloud sessions read/write them via the device bridge; nothing canonical in Claude's cloud.
- **Recovery Tracker** — ⚪ deliberately parked. Demand-driven trigger: built the month a councilor/reporter/partner asks. Plumbing ships with roselight tag layer.

## Operating rules (from momentum plan — see research/momentum-plan.md)
- **Sequence rule: consent → publicity → product.** Nothing public before champions bless it.
- **3 hrs/week cap; displacement rule** — new recurring commitments must replace one.
- Builds are bounded one-off weekends; the weekly block is for conversations.
- Only field-verified claims appear anywhere public; correction channel always visible.

## Current next actions (detail in [[burnside]] todo)
- [ ] Email Ryan Hashagen (Better Block PDX / Icicle Tricycles) — propose corridor walk
- [ ] Calendar re-anchor: April vacancy-tax outcome? EQRB 60% design status?
- [ ] Pull Rose Light real subscriber/open-rate numbers (baseline)
- [ ] Saturday hardening walk: verify all 16 inventory addresses
- [ ] Weekend build 1: roselight tag layer + topic API + signup source tag
- [ ] Weekend build 2: Claude-drafted digest + EQRB watcher + nightly Burnside rebuild

## Sync
This folder follows [`docs/conventions/context-sync.md`](../../docs/conventions/context-sync.md):
**the hub is the single source of truth** — state here, research in `research/`, code in
repos. Cloud sessions are workers, not archives; they read from and write back to the hub.

## Worklog
Cross-project civic decisions get dated entries in `notes.md` here; per-repo work goes in
each repo's `WORKLOG.md`.
