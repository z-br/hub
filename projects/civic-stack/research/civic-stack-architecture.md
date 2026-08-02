> Canonical home: this hub. (Moved from Claude cloud session 2026-07-18; cloud copies deleted.)
> NOTE: read alongside `momentum-plan.md`, which SUPERSEDES parts of this — the Recovery Tracker is parked (demand-triggered), and the 13-vector board comes off the Burnside site.

# The Civic Stack: Connecting Rose Light ↔ Research Corpus ↔ Better Burnside (July 2026, v2 — verified against roselight code)

## The frame

| Layer | Asset | What it does | What it lacks |
|---|---|---|---|
| **Sensing** | The Rose Light (React Router v7 + Prisma/PG; node-cron worker syncs 22 RSS sources q15min; Claude Haiku scoring; Resend weekly digest; editor dashboard) | Continuous Portland news ingest + curation | Purpose beyond aggregation; no action layer |
| **Sense-making** | Research corpus (this hub's `research/` — status board, vacancy inventory, verified bridge research) | Turns news into status / ownership / next steps, with verification discipline | Public surface (deliberately parked) |
| **Action** | Better Burnside (campaign site, `src/data/site.ts` content model) | Converts analysis into coalition, evidence, asks | Goes stale without manual edits; no audience engine |

## What the roselight code already has (verified 7/18/26)
- **Claude per-post, already**: content-based categorization per post (authoritative for local/national/world scope), `aiFeatureScore` 0–100 + reason, batch tag categorization (pattern fast-path + one batched Haiku call; ~pennies/sync per `TAG_CATEGORIZATION.md`).
- **Taxonomy machinery**: `Category → Tag → Post` m2m in Prisma; 13 umbrella categories; documented, idempotent add-category process (`worker/categorize.ts`, `scripts/categorize-tags.ts`); `Category.excludeFromMainFeed` flag.
- **List infra**: `Subscriber` model with confirm/unsub tokens **and a `source` field** — origin segmentation supported today.
- **Digest infra**: `Digest` model (draft → locked → sent), editor preview Sun 8am / send 9am PT.
- **Editor scaffolding**: auth + dashboard patterns reusable for future tracker CRUD.
- **Deploy**: Coolify (web Nixpacks, worker Dockerfile.worker), feature-branch + PR workflow.

## Keystone: civic vectors as Tags under a `civic-recovery` Category
- New Category `civic-recovery` (`excludeFromMainFeed: true` — overlay, not nav section).
- The 13 status-board vectors become Tags with **slugs identical to the vector slugs** (`west-burnside`, `waterfront-conservancy`, `office-conversions`, …). Vector slug ↔ tag slug is the entire integration contract.
- Extend the content-categorization prompt: vector definitions + corridor rule for `west-burnside` ("mentions W Burnside between the bridge and NW 24th, or Powell's / Crystal Ballroom / Providence Park / Goose Hollow / Stadium Fred Meyer…"). A prompt rule, not a geocoder. Replicable later (82nd, Old Town, Central Eastside).
- Run the existing idempotent categorize flow. No new tables required for tagging.

## The near-free connections
1. **Topic feeds.** One React Router loader: `/api/topics/:slug.json` (+ RSS) = posts where tags.some.slug = :slug. Burnside site fetches `west-burnside` at Astro build time → permanent "corridor news" section; nightly rebuild via Coolify deploy webhook.
2. **Watchers.** Standing queries surfaced as digest sections: EQRB federal-funding news, corridor closures/openings, council agendas touching vectors.
3. **One email list.** Burnside signup POSTs to roselight subscribe with `source: "betterburnside"`. One list, origin-segmented; the metric that makes the flywheel testable.

## Parked: "Portland Recovery Tracker" inside roselight (demand-triggered)
When a councilor/reporter/partner asks: `Vector` model (slug @unique, title, status enum, owner, nextSteps, sources Json, updatedAt) + public `/tracker` + `/tracker/:slug` + editor CRUD. Tracker page news stream = posts by matching tag slug. Rationale for parking: maintenance IS the product (stale = credibility damage); no measured audience yet; it's the code-at-night default move. The tag layer keeps the option cheap (weeks, not months).

## Trust architecture
Facts flow DOWN from verified research (this hub); signals flow UP from ingest; human approves at the PR boundary. Monthly verification loop: check corridor facts against the month's tagged stories + primary sources → update hub research docs → propose PRs (burnside `site.ts`, later tracker data). Automated layer proposes; cited layer disposes; GZ merges.

## Build order
1. `civic-recovery` category + vector tags + prompt extension + run categorize script (~1 session)
2. `/api/topics/:slug.json` loader (+ RSS) (~1 route)
3. Burnside "corridor news" (build-time fetch) + nightly rebuild webhook
4. Burnside signup → subscribe endpoint with source tag
5. Digest watcher sections
6. (parked) Tracker MVP — on demand only
7. Monthly verification loop → PRs

## Open questions
- Do civic-recovery tags display on story cards from day one (future "context" surface) or stay backend-only until a tracker exists?
- Tracker voice: neutral-curatorial (roselight) vs campaign (burnside) — keep separated for credibility (recommended: yes).
- Other corridors (82nd, Old Town) → template the burnside `site.ts` model when asked, not before.
