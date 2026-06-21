# cookthebooks

> **Status:** 🟢 active · **Updated:** 2026-06-21
> Status legend: 🟢 active · 🟡 paused · ⚪ idea · ✅ done · ⚫ retired

**One-liner:** A recipe / cookbook app written in Go — *not* a finance tool (the name is a pun).

## Links
- **Code:** `~/dev/cookthebooks` · [remote](https://github.com/z-br/cookthebooks)
- **Live (prod):** https://cookthebooks.app (web) · https://api.cookthebooks.app (API) — on Coolify since 2026-06-20
- **Ops/backup:** repo `docs/operations.md` · **Infra:** [[deployment]]

## Stack
Go API · Next.js web (`web/`) · OpenSearch (search) · Postgres · AWS S3 + CloudFront (images)

## Status (2026-06-21)
**Live on prod, fully on the Coolify home cloud.** Docker-Compose stack
(api + web + opensearch) on the shared Postgres (schema `cookthebooks`), behind the
Cloudflare tunnel. Cut over from Vercel/DO on 2026-06-20–21; **DigitalOcean, Neon,
and Vercel decommissioned** and stale DNS cleaned up (no dangling CNAMEs). Kept AWS
S3 + CloudFront (`cdn-images`) for images.
- **Remaining (hygiene only):** scoped S3 upload keys; a scoped DB role (currently
  connects as the shared-PG admin); rotate the GCP key. See `notes.md` (2026-06-21).

## Overview
Recipe/cookbook manager — stores and searches recipes (OpenSearch-backed), with a `kitchen/` domain layer and a `web/` frontend. The most substantial of the older repos: **509 commits**, your earliest work here (authored as `Greg Passmore`). Dormant since Sep 2024 until the 2026-06 Coolify migration.

## Related
-
