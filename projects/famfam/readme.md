# famfam

> **Status:** 🟢 active · **Updated:** 2026-06-14
> Status legend: 🟢 active · 🟡 paused · ⚪ idea · ✅ done · ⚫ retired

**One-liner:** Self-hosted family-history / ancestor wiki engine — family members drop loose memories (text, photos, voice transcripts) into an inbox, and Claude organizes them into a structured, searchable archive of people, places, events, and stories.

## Links
- **Code:** `~/dev/famfam` · [remote](https://github.com/z-br/famfam)
- **Live:** _tbd_

## Stack
Next.js 15 · TypeScript · Tailwind · Postgres + pgvector · Drizzle · Auth.js (magic links) · Cloudflare R2 · Claude (Sonnet 4.6) · TensorFlow/face-api · pg-boss. Coolify-deployable. Node 22.

## Overview
A family wiki engine, **not** related to the FamilyHub task app. Flow: members drop memories into an inbox → Claude extraction returns structured people/places/events + a story draft + reviewer questions → publish creates linked wiki rows. MVP working: text memory → AI extraction → publish → wiki pages (person/places/stories). Not yet built: photo upload UI, voice transcription, family-tree viz, map view, embeddings/search, background queue (schema exists for all).

## Related
- Uses Claude like [[roselight]]; deploys via Coolify.
- Distinct from the FamilyHub suite ([[familyhub]] / [[familyhub-mobile]]).
