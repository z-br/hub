# 🌹 The Rose Light

> **Status:** 🟢 active · **Updated:** 2026-06-14
> Status legend: 🟢 active · 🟡 paused · ⚪ idea · ✅ done · ⚫ retired

**One-liner:** Self-curating, newspaper-style aggregator for local Portland news — Claude scores every story for local relevance; the front page and a weekly email digest assemble themselves. *"Light on what matters for Portland."*

## Links
- **Code:** `~/dev/theroselight.org` · [remote](https://github.com/z-br/theroselight.org) _(renamed from `dreampdx`; npm pkg `roselight`)_
- **Live:** [theroselight.org](https://theroselight.org)

## Stack
React Router v7 · React 19 · Tailwind v4 · PostgreSQL/Prisma 7 · node-cron worker · Claude (Haiku 4.5) · Resend email · Sentry. Node 24+.

## Overview
Two processes share one Postgres DB: a **web app** (newspaper UI + editor dashboard) and a **background worker**. The worker syncs RSS every 15 min, uses Claude to categorize and score posts 0–100 (`aiFeatureScore`), aggregates analytics nightly, and emails a curated weekly digest (Sun 8am editor preview, 9am subscriber send, PT). Editors can override featuring and prune the digest.

Deployed on the self-hosted **Coolify** lab (Mac mini): web via Nixpacks, worker via `Dockerfile.worker`, behind a Cloudflare Tunnel. Migrated off DigitalOcean 2026-06-21 (prod DB copied into the shared Coolify Postgres; DO retired). Repo uses feature-branch + PR workflow.

## Related
- Family of personal sites: [[homepage]], [[blog]]
