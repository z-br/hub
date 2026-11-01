# Roselight — Second-City Launch Analysis

> **Status:** ⚪ idea / analysis only, nothing built · **Written:** 2026-11-01
> Question asked: *"Given all the municipalities in America, what would be the
> best place to launch a separate version of the Rose Light?"*
> Provenance: single-agent research session (codebase read + web search).
> Pittsburgh details come from search-result summaries; the full PublicSource and
> Northwestern/Medill articles couldn't be opened from the session.

## Recommendation

**Seattle first.** **Pittsburgh** is the runner-up — the better bet if
optimizing for reader need and timing over ease of launch.

## Criteria (derived from how the product works)

The Rose Light doesn't produce journalism; it gathers it. So the target is a city
with plenty of outlets that are hard to follow all at once — **not a news
desert**, which gives the worker almost nothing to sync. The ideal city has:

1. **Lots of sources** — 25–40+ local outlets with RSS (dailies, nonprofits, TV,
   public radio, neighborhood blogs, food sites).
2. **Paywall frustration** — the main daily is paywalled, so "free, no paywall"
   still means something.
3. **Engaged readers** — newsletter readers with a strong city identity, like
   Portland's.
4. **No strong free aggregator already there** — daily newsletters (Axios,
   6AM City, City Cast) don't count; they become feeds, not rivals.
5. **Operator fit** — time zone, distance, regional knowledge, and how much of
   the Claude prompts carry over.

## Why Seattle

- **Almost every feed in `prisma/seed.ts` has a Seattle counterpart:**

  | Portland source | Seattle counterpart |
  |---|---|
  | Oregonian | Seattle Times (paywalled) |
  | OPB | KUOW / Cascade PBS |
  | KGW | KING 5 (same owner, Tegna) |
  | Portland Mercury | The Stranger (same original publisher) |
  | Oregon Capital Chronicle | Washington State Standard (both States Newsroom) |
  | Eater PDX | Eater Seattle |
  | City Cast Portland | City Cast Seattle |
  | Next Portland | The Urbanist |
  | NW/SE Examiner, The Bee, St. Johns Review | West Seattle Blog, Capitol Hill Seattle Blog, South Seattle Emerald |

- **The worker barely changes.** Same time zone, so the `America/Los_Angeles`
  digest schedules (`worker/index.ts:73,88`) and the preview-token expiry
  (`worker/digest.ts:100`) stay as they are.
- **Similar audience**, more city to cover, higher incomes (helps if sponsorships
  ever happen). ~3 hours away — close enough to build outlet relationships in
  person, as with the Everout outreach.
- **Name nearly writes itself:** Rose City → Emerald City, e.g. "The Emerald
  Light." Check for confusion with the South Seattle Emerald first.
- **Risk — crowded newsletters:** Axios Seattle, SEAtoday (6AM City), and City
  Cast Seattle (launched 2025). They're short daily picks; the Rose Light offers a
  complete, free, AI-filtered stream and can pull their feeds in as sources.

## Why Pittsburgh is the runner-up

- **Reader habits were broken in 2026.** The Post-Gazette announced in January
  2026 it would close. The Venetoulis Institute (nonprofit behind the Baltimore
  Banner) bought it instead in April (effective May 4) and then cut ~40% of the
  newsroom.
- **New outlets keep appearing:** City Paper came back in March 2026, the
  reader-/worker-owned Pittsburgh Progress launched August 2026, and the
  Tribune-Review is hiring ~a dozen journalists.
- **PublicSource had to publish a guide to 40+ local sources** — basically a
  Rose Light feed list, and proof readers need help finding their news.
- **Against it:** Eastern time, ~2,500 miles away, no local knowledge, Axios and
  City Cast are also there, and the P-G rescue made the gap smaller than it looked
  in January.

## Where not to go

- **News deserts** — too little to aggregate.
- **New York, LA, Chicago** — too big for one editor; Block Club already covers
  Chicago's neighborhoods.
- **Markets with a strong free aggregator** — Salt Lake City (KSL.com), Twin
  Cities (Bring Me The News).

## What a launch takes in the code

As of 2026-11-01: **177 Portland/Oregon references across 21 files.** Most are in
the Claude prompts — `worker/categorize.ts` (87) and `worker/suggest-features.ts`
(32) — which hold the local geography the classifier relies on (counties, teams,
agencies, Mt. Hood). Then `prisma/seed.ts` (23), and a handful each in routes,
emails, and `worker/digest.ts` / `worker/good-news.ts`.

- **Not a find-and-replace.** E.g. `categorize.ts:215` treats "Washington County"
  as local; in Seattle, "Washington" mostly means the state.
- **Do first:** pull all of it into a single per-city config (place names,
  institutions, teams, time zone, branding, starting feeds) so a third city is
  cheap too.

## Cheap test before committing

Run the worker on a Seattle feed list against a scratch database for two weeks,
then compare local-stories-per-day against Portland's numbers. That settles it
better than this analysis can.

## Sources

- [Nieman Lab — Will Pittsburgh become America's most important city without a newspaper?](https://www.niemanlab.org/2026/01/will-pittsburgh-become-americas-most-important-city-without-a-newspaper/)
- [Axios — Post-Gazette to shut down](https://www.axios.com/local/pittsburgh/2026/01/07/post-gazette-shutdown-may-2026)
- [WITF — Post-Gazette stays alive after sale to nonprofit](https://www.witf.org/2026/04/15/pittsburgh-post-gazette-stays-alive-after-sale-to-nonprofit/)
- [Boston Globe — Post-Gazette saved from closure](https://www.bostonglobe.com/2026/04/14/business/pittsburgh-post-gazette-saved-closure/)
- [PublicSource — More than 40 news sources remain in Pittsburgh's fragmented media landscape](https://www.publicsource.org/pittsburgh-media-landscape-local-journalism/)
- [Pittsburgh City Paper — Former Post-Gazette workers launch the Pittsburgh Progress](https://www.pghcitypaper.com/news-2/news/former-post-gazette-workers-strike-a-new-path-with-the-pittsburgh-progress/)
- [In These Times — Former strikers start cooperative news organization](https://inthesetimes.com/article/former-strikers-start-cooperative-news-organization)
- [AP via ClickOnDetroit — Pittsburgh's media has a surprising turnaround](https://www.clickondetroit.com/news/national/2026/04/21/after-facing-the-death-of-its-dominant-newspaper-pittsburghs-media-has-a-surprising-turnaround/)
- [Barrett Media — City Cast expands to Seattle and Twin Cities](https://barrettmedia.com/?p=238150)
- [City Cast Houston — Goodbye](https://houston.citycast.fm/announcements/goodbye)
- [SEAtoday (6AM City)](https://seatoday.6amcity.com/small-business/support-free-unbiased-local-journalism-seattle)
- [Axios Seattle](https://inboxreads.co/n/axios-seattle)
