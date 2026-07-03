# Portland Event Calendar — Feature Plan

## Overview

Add a full Portland event calendar to The Rose Light: automated event ingestion from external platforms, AI-powered quality scoring and categorization, editor curation tools, and a public-facing calendar UI with list/calendar views and category filters.

This follows the same architectural patterns as the existing RSS news pipeline: **external sources → worker ingestion → AI enrichment → Prisma DB → React Router frontend → editor dashboard**.

---

## 1. Data Model (Prisma Schema)

### New Models

```prisma
enum EventStatus {
  pending    // Ingested, not yet reviewed
  approved   // Visible on public calendar
  rejected   // Hidden (spam, duplicate, irrelevant)
  cancelled  // Was approved, now cancelled
}

model Event {
  id              String       @id @default(cuid())

  // Core fields
  title           String
  description     String?      @db.Text
  shortDescription String?     // AI-generated 1-2 sentence summary

  // Date/time
  startDate       DateTime
  endDate         DateTime?
  isAllDay        Boolean      @default(false)
  recurrenceRule  String?      // iCal RRULE format for recurring events

  // Location
  venueName       String?
  venueAddress    String?
  venueNeighborhood String?    // AI-assigned: Pearl, Alberta, Hawthorne, etc.
  latitude        Float?
  longitude       Float?
  isVirtual       Boolean      @default(false)
  virtualUrl      String?

  // Links & media
  url             String?      // Original event page
  ticketUrl       String?
  imageUrl        String?
  imageData       Bytes?       // Cached image (like posts)
  imageMimeType   String?
  imageFetchAttempted Boolean  @default(false)

  // Pricing
  price           String?      // Free-text: "Free", "$25", "$15-$45", "Donation"
  isFree          Boolean      @default(false)

  // Source tracking
  sourceType      String       // "everout", "willamette_week", "portland_mercury", "pdx_pipeline", "eventbrite", "calagator", "ticketmaster", "manual"
  sourceId        String?      // External ID for deduplication
  sourceUrl       String?      // Where we scraped/fetched from

  // Status & editorial
  status          EventStatus  @default(pending)

  // AI enrichment
  aiScore         Int?         // 0-100 quality/interest score
  aiScoreReason   String?      @db.Text
  aiCategories    Json?        // ["music", "outdoor"] from AI analysis

  // Editor curation
  isFeatured      Boolean      @default(false)  // Editor's pick
  isEditorPick    Boolean      @default(false)  // "Best of" highlight
  editorNote      String?      @db.Text         // Optional editor commentary

  // Relations
  categories      EventCategory[]

  // Timestamps
  createdAt       DateTime     @default(now())
  updatedAt       DateTime     @updatedAt

  @@unique([sourceType, sourceId])  // Prevent duplicate imports
  @@index([startDate])
  @@index([status, startDate])
  @@index([isFeatured])
  @@index([isEditorPick])
  @@index([aiScore])
  @@index([venueNeighborhood])
}

model EventCategory {
  id          String  @id @default(cuid())
  name        String  @unique  // "Music", "Food & Drink", "Comedy", etc.
  slug        String  @unique
  emoji       String? // Optional emoji for UI: "🎵", "🍔", "😂"
  description String?
  displayOrder Int    @default(0)
  events      Event[]

  @@index([slug])
}

model EventSource {
  id          String   @id @default(cuid())
  name        String   // "Eventbrite", "Calagator", etc.
  type        String   @unique // "eventbrite", "calagator", etc.
  apiUrl      String?  // Base URL or feed URL
  isActive    Boolean  @default(true)
  lastSyncAt  DateTime?
  config      Json?    // Source-specific config (API keys, filters, etc.)
  createdAt   DateTime @default(now())
  updatedAt   DateTime @updatedAt
}
```

### Event Categories (seed data)

| Slug | Name | Emoji |
|------|------|-------|
| `music` | Music & Concerts | 🎵 |
| `food-drink` | Food & Drink | 🍔 |
| `comedy` | Comedy | 😂 |
| `theater` | Theater & Performance | 🎭 |
| `art` | Art & Galleries | 🎨 |
| `film` | Film & Screenings | 🎬 |
| `outdoor` | Outdoor & Nature | 🌲 |
| `sports-rec` | Sports & Recreation | ⚽ |
| `community` | Community & Civic | 🏘️ |
| `family` | Family & Kids | 👨‍👩‍👧 |
| `nightlife` | Nightlife & Bars | 🍸 |
| `markets` | Markets & Shopping | 🛍️ |
| `classes` | Classes & Workshops | 📚 |
| `festivals` | Festivals & Fairs | 🎪 |
| `tech` | Tech & Innovation | 💻 |
| `wellness` | Health & Wellness | 🧘 |

---

## 2. Event Sourcing (Worker Pipeline)

### Architecture

New file: `worker/events.ts` — follows the same pattern as `worker/rss.ts`

Registered in `worker/index.ts` on a cron schedule:
- **Event sync**: Every 6 hours (events don't change as fast as news)
- **Stale event cleanup**: Daily at 1am (remove events past their end date by 7+ days)

### Source Adapters

Each source gets an adapter that returns a common `ParsedEvent[]` format:

```typescript
interface ParsedEvent {
  title: string;
  description?: string;
  startDate: Date;
  endDate?: Date;
  isAllDay?: boolean;
  venueName?: string;
  venueAddress?: string;
  latitude?: number;
  longitude?: number;
  isVirtual?: boolean;
  virtualUrl?: string;
  url?: string;
  ticketUrl?: string;
  imageUrl?: string;
  price?: string;
  isFree?: boolean;
  sourceType: string;
  sourceId: string;
  categories?: string[]; // Raw category strings from source
}
```

### Source Tiers & Trust Levels

The key insight: **editorially-curated sources are far higher signal** than raw API feeds. An event listed on Everout or Willamette Week has already passed a human editorial filter — it's almost certainly worth surfacing. Eventbrite, by contrast, is full of MLM webinars, random Zoom calls geotagged to Portland, and spam. The tier system reflects this.

#### Tier 1 — Editorially curated, high trust (auto-approve score 30+)

These sources have human editors picking events. Trust their curation — apply lighter AI scoring.

1. **Everout** (everout.com/portland) — PNW-focused editorial event picks. Excellent taste-level curation across music, food, comedy, art. Scrape their Portland events page or RSS feed. High-quality images and descriptions. **Best single source for Portland events.**
2. **Willamette Week** (wweek.com/events) — Portland's alt-weekly. Strong editorial curation, especially for music, nightlife, food events. RSS feed or scraping. Also runs annual "Best of Portland" which informs venue/event reputation.
3. **Portland Mercury** (portlandmercury.com/events) — Sister alt-weekly. Good coverage of indie music, comedy, art, community events. Scrape event listings page + AI extraction of structured data.
4. **PDX Pipeline** (pdxpipeline.com) — Community-focused Portland blog. WordPress RSS feed at `/category/events/feed/`. Parse event details from post content using AI extraction. Good for markets, festivals, community events.

#### Tier 2 — Structured APIs, high volume, noisy (standard scoring, auto-approve 50+)

These sources have lots of data but need aggressive AI filtering to separate signal from noise.

5. **Eventbrite API** — Richest structured data (dates, venues, images, prices, categories). Search by `location.address=Portland,OR` and `location.within=25mi`. OAuth token required (free). Rate limit: 2000 req/hr. **Warning: very noisy** — expect 60-70% of results to be low-quality or irrelevant.
6. **Calagator** (calagator.org) — Portland's community calendar aggregator. Open source, JSON API at `/events.json`. Free, no auth. Good grassroots/community events but inconsistent data quality. iCal feed also available.
7. **Ticketmaster Discovery API** — Free API key. Search by `city=Portland&stateCode=OR`. Returns concerts, sports, theater with high-quality images and venue data. Rate limit: 5000 calls/day. Limited scope — mostly big-venue events.

#### Tier 3 — Future / nice-to-have

8. **Portland.gov** — City event calendar. Check for iCal/RSS feeds. Good for civic/community events.
9. **Do503** (do503.com) — Nightlife and music focused. Likely needs scraping.
10. **Meetup API** — Requires OAuth, limited access since 2020. GraphQL API. Good for tech/community meetups.
11. **OMSI / Portland Art Museum / cultural institutions** — Individual venue calendars for high-quality cultural events.

### Source Trust & Auto-Approve Thresholds

| Source Tier | AI Score for Auto-Approve | AI Score for Auto-Reject | Rationale |
|-------------|---------------------------|--------------------------|-----------|
| Tier 1 (curated) | 30+ | Below 10 | Editorial filter already applied upstream |
| Tier 2 (APIs) | 50+ | Below 30 | Need AI to filter noise aggressively |
| Tier 3 (future) | 50+ | Below 30 | Same as Tier 2 |

When the same event appears from both Tier 1 and Tier 2 sources, the Tier 1 source should be the "primary" record (better descriptions, editorial context) and the Tier 2 data merged in for any missing structured fields (exact coordinates, ticket URLs, etc.).

### Deduplication Strategy

With 7+ sources covering the same city, dedup is the hardest problem. The same First Thursday event will appear on Everout, Mercury, WW, Eventbrite, and Calagator.

**Layer 1 — Same-source dedup (trivial)**
- `sourceType + sourceId` unique constraint prevents re-importing the same event from the same source.

**Layer 2 — Cross-source fuzzy matching (critical)**
Before inserting a new event, query existing events within the same date window (startDate +/- 4 hours) and run matching:
1. **Title similarity**: Normalize titles (lowercase, strip "Portland", strip venue name) and compute similarity. Threshold: >75% match.
2. **Venue match**: Normalize venue names through an alias table (see below). "Doug Fir" = "Doug Fir Lounge" = "Doug Fir Lounge Portland".
3. **Combined signal**: If title similarity >60% AND venue matches, treat as duplicate.

**Layer 3 — Venue normalization table**
Maintain a `VenueAlias` lookup (can be a simple JSON file initially, DB table later):
```json
{
  "doug fir lounge": ["doug fir", "doug fir lounge portland", "dfl"],
  "revolution hall": ["rev hall", "revolution hall portland"],
  "crystal ballroom": ["crystal", "crystal ballroom portland", "mcmenamins crystal ballroom"],
  "pioneer courthouse square": ["pioneer square", "pioneer sq", "the square"]
}
```
Seed with the top 50-100 Portland venues. AI can suggest additions when it encounters unrecognized venues.

**Layer 4 — Merge strategy for duplicates**
When a duplicate is found across sources:
- **Keep the Tier 1 (curated) version as primary** — better title, description, editorial context
- **Merge in structured data from Tier 2** — exact coordinates, ticket URLs, pricing, images
- **Track all source URLs** — store as `sourceUrls: Json` array so we know where the event was found
- **Use the highest AI score** from any source

---

## 3. AI Enrichment Pipeline

Runs after ingestion, same pattern as `categorizePostsByContent` and `evaluatePostsForFeaturingBatch`.

### Step 1: Categorization
- AI assigns 1-3 EventCategories per event based on title + description
- Uses Claude Haiku (fast, cheap) like existing categorization

### Step 2: Quality Scoring (0-100)
Score based on:
- **Event significance** (40%): Is this a major event, festival, notable performer, or niche meetup?
- **Venue reputation** (20%): Well-known Portland venue vs unknown location
- **Uniqueness** (20%): One-time special event vs weekly recurring
- **Data quality** (20%): Has image, full description, clear time/location

Scoring thresholds (combined with source tier — see Source Trust table in Section 2):
- **80+**: Auto-approve, candidate for "Editor's Pick" suggestion (any source)
- **50-79**: Auto-approve for Tier 2 sources, regular listing
- **30-49**: Auto-approve for Tier 1 (curated) sources; pending review for Tier 2
- **10-29**: Pending for Tier 1; auto-reject for Tier 2
- **0-9**: Auto-reject (spam, irrelevant, completely broken data)

### Step 3: Neighborhood Assignment
AI assigns Portland neighborhood from title/address:
- Pearl District, Alberta Arts, Hawthorne, Division, Mississippi, St. Johns, Sellwood, Lloyd, Hollywood, Downtown, Old Town, Beaverton, Lake Oswego, etc.

### Step 4: Short Description Generation
For events with long descriptions, AI generates a 1-2 sentence summary for card display.

---

## 4. Frontend Routes & UI

### New Routes

| Route | File | Description |
|-------|------|-------------|
| `/events` | `routes/events.tsx` | Main event calendar page |
| `/events/:id` | `routes/events.$id.tsx` | Event detail page |
| `/editor/events` | `routes/editor/events.tsx` | Editor event management |

### Public Calendar Page (`/events`)

**Header**: "Portland Events" with category filter chips (horizontal scroll on mobile)

**View modes** (toggle):
- **List view** (default): Grouped by date ("Today", "Tomorrow", "This Weekend", "Next Week", etc.)
- **Calendar view**: Month grid with event dots, click day to see events

**Filters** (URL params, combinable):
- Category: `?category=music`
- Date range: `?from=2026-03-10&to=2026-03-17`
- Free only: `?free=true`
- Neighborhood: `?neighborhood=pearl`
- Featured/Editor's Picks: `?picks=true`

**"This Week's Picks" section** at top (if any editor picks exist):
- Horizontal scrollable cards with images
- Editor note displayed if present

**Event cards** in list:
- Date/time badge (left)
- Title, venue, neighborhood tag, price
- Category chip(s)
- Thumbnail image (right)
- "Featured" or "Editor's Pick" badge

**Infinite scroll** pagination (same pattern as posts page)

### Event Detail Page (`/events/:id`)

- Hero image
- Title, date/time, venue with map link
- Full description
- Price info + ticket link button
- Category tags
- "Add to Calendar" button (generates .ics download)
- Link back to source
- Related events (same category, nearby dates)

### Navigation
- Add "Events" link to the main site navigation/header alongside existing categories

---

## 5. Editor Dashboard (`/editor/events`)

Follows the pattern of `editor/posts.tsx`.

### Features
- **Event list** with filters: status (pending/approved/rejected), date range, category, source
- **Bulk actions**: Approve, reject, feature selected events
- **Event detail editor**:
  - Override AI category assignments
  - Mark as "Editor's Pick" with optional editor note
  - Override AI score
  - Edit title, description, dates, venue
  - Change status
- **AI suggestions panel**: Shows highest-scored pending events for quick review
- **Stats**: Events by source, approval rate, category distribution

---

## 6. Implementation Phases

### Phase 1: Foundation (Schema + Curated Sources + Basic UI)
1. Add Prisma schema models (Event, EventCategory, EventSource)
2. Run migration, seed EventCategories
3. Build `worker/events.ts` with common ingestion framework
4. Implement Everout scraper (highest signal source)
5. Implement Willamette Week scraper
6. Build AI enrichment pipeline (categorize, score, neighborhood, summarize)
7. Build cross-source deduplication logic (needed early since curated sources overlap)
8. Register event sync in `worker/index.ts`
9. Build `/events` list view with date grouping and category filters
10. Build `/events/:id` detail page
11. Add "Events" to site navigation

### Phase 2: More Curated Sources + Editor Tools
12. Implement Portland Mercury scraper
13. Implement PDX Pipeline RSS adapter
14. Build `/editor/events` dashboard
15. Add "Editor's Pick" and featured event workflows
16. Seed venue alias table (top 50-100 Portland venues)

### Phase 3: Volume Sources (APIs)
17. Implement Eventbrite adapter (high volume, needs aggressive filtering)
18. Implement Ticketmaster adapter
19. Implement Calagator adapter
20. Cross-source merge logic (Tier 2 data supplements Tier 1 records)

### Phase 4: Polish + Advanced Features
21. Calendar grid view (month view)
22. "Add to Calendar" (.ics) generation
23. Neighborhood filter with optional map
24. Event analytics (clicks, popular categories)
25. "This Weekend" summary widget on homepage
26. Venue normalization improvements (AI-suggested aliases)

---

## 7. Key Technical Decisions

| Decision | Choice | Rationale |
|----------|--------|-----------|
| Separate Event model vs reusing Post | **Separate model** | Events have fundamentally different fields (dates, venues, pricing). Mixing into Post would bloat the schema. |
| Event categories vs reusing news Categories | **Separate EventCategory** | News categories (government, crime) don't map to event types (music, comedy). Keep them independent. |
| Sync frequency | **Every 6 hours** | Events don't change as rapidly as news. Saves API quota and compute. |
| Source priority | **Curated sources first** | Everout/WW/Mercury have already done editorial filtering — much higher signal-to-noise than Eventbrite/Ticketmaster. Build on their curation rather than trying to replicate it with AI alone. |
| Auto-approve threshold | **Tier-dependent** | Tier 1 (curated) sources auto-approve at 30+; Tier 2 (APIs) at 50+. Curated sources have an upstream editorial filter we can trust. |
| Image caching | **Same as posts** | Reuse existing `fetchImageData` with sharp resizing to avoid hotlink issues. |
| AI model | **Claude Haiku** | Same as existing categorization — fast and cheap for batch processing. |
| Dedup approach | **Venue alias table + title fuzzy match** | AI-only dedup is expensive at scale. A venue normalization table handles 80% of cases cheaply, with AI as fallback for ambiguous matches. |

---

## 8. Files to Create/Modify

### New Files
- `prisma/schema.prisma` — Add Event, EventCategory, EventSource models
- `worker/events.ts` — Event sync orchestrator
- `worker/events/types.ts` — Shared types (ParsedEvent, etc.)
- `worker/events/everout.ts` — Everout scraper (Tier 1, highest priority)
- `worker/events/willamette-week.ts` — Willamette Week scraper (Tier 1)
- `worker/events/portland-mercury.ts` — Portland Mercury scraper (Tier 1)
- `worker/events/pdx-pipeline.ts` — PDX Pipeline RSS adapter (Tier 1)
- `worker/events/eventbrite.ts` — Eventbrite API adapter (Tier 2)
- `worker/events/calagator.ts` — Calagator JSON/iCal adapter (Tier 2)
- `worker/events/ticketmaster.ts` — Ticketmaster Discovery adapter (Tier 2)
- `worker/events/enrich.ts` — AI categorization, scoring, neighborhood assignment
- `worker/events/dedup.ts` — Cross-source fuzzy deduplication logic
- `worker/events/venues.ts` — Venue normalization and alias table
- `app/routes/events.tsx` — Public event calendar page
- `app/routes/events.$id.tsx` — Event detail page
- `app/routes/editor/events.tsx` — Editor event management
- `prisma/seed-event-categories.ts` — Seed script for event categories

### Modified Files
- `worker/index.ts` — Register event sync cron job
- `app/routes.ts` — Add event routes
- `app/root.tsx` or navigation component — Add "Events" nav link

---

## 9. Open Questions / Risks

1. **Scraping sustainability**: Everout, WW, and Mercury are all scraped sources. They could change their HTML structure at any time. Mitigations: use AI-assisted extraction (more resilient to layout changes), monitor for scraper failures, keep adapters modular for quick fixes.
2. **Scraping etiquette**: Respect robots.txt and rate limits for all scraped sources. Only sync every 6 hours — not aggressive. Consider reaching out to establish data partnerships (especially Everout, which might appreciate the backlinks).
3. **Deduplication accuracy**: Cross-source dedup will never be 100% perfect. A few duplicates slipping through is better than accidentally merging two distinct events. Err on the side of keeping both and letting editors merge manually.
4. **Event volume**: Even with curated-first approach, Portland has 50-200+ events on any given weekend. The UI needs good filtering (category, neighborhood, date) to avoid overwhelming users.
5. **Recurring events**: iCal RRULE parsing adds complexity. Expand recurring events into individual instances at ingestion time rather than handling recurrence in the frontend.
6. **Eventbrite API access**: Need to create an Eventbrite app and get OAuth token. Their API has been somewhat unstable — defer to Phase 3 since curated sources provide better signal anyway.
7. **Calagator availability**: calagator.org has historically had uptime issues. Defer to Phase 3 as a supplementary source, not a primary one.
