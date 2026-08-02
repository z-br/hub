> Canonical home: this hub. (Moved from Claude cloud session 2026-07-18; cloud copies deleted.)
> SUPERSEDED IN PART by `momentum-plan.md` and `civic-stack-architecture.md`: the status-board site (Site 2) is now the parked, demand-triggered Recovery Tracker inside roselight, and the 13-vector board comes OFF the Burnside site.

# Website Plan: Burnside Campaign Site + (parked) Status Board (July 2026)

Decision: two separate properties, one shared sensibility. The Burnside effort needs its own banner/domain to recruit institutions and be hand-off-able to the coalition. Built 7/18/26 as `~/dev/burnside` (Astro 5/TS, static, content in `src/data/site.ts`).

## Site 1 — Burnside campaign site (BUILT as prototype; DRAFT until champions consent)
1. **Home** — thesis + CTA (email list; pipes to roselight Subscriber, source="betterburnside").
2. **The Plan** — Bridgeless Interval made legible; corridor map bridge→stadium; parks string; Burnside Triangle heritage angle.
3. **The Evidence** — vacancy inventory as corridor map + table (field-verified claims only, as-of dates, correction channel).
4. **The Countdown → "The Window"** — EQRB status (postponed indefinitely; prep-years framing) + bridge-funding watcher.
5. **Coalition** — at-the-table vs needed; "name TBD by founding members."
6. **Act** — asks by audience (residents / businesses / institutions / officials).
- REMOVED per momentum plan: the 13-vector board section.

## Site 2 — Recovery Tracker (PARKED)
Demand-triggered; lives inside roselight when built (see civic-stack-architecture.md). Signature: the "no owner" flag.

## Build/deploy
Astro static, CSS inlined, `serve` start; Coolify/Nixpacks per hub deployment doc; repo `~/dev/burnside` → GitHub z-br/burnside; nightly rebuild webhook once corridor news feed exists. Naming: check betterburnside.org etc. — but the founding members choose.
