# cookthebooks

> **Status:** 🟢 active · **Updated:** 2026-06-20
> Status legend: 🟢 active · 🟡 paused · ⚪ idea · ✅ done · ⚫ retired

**One-liner:** A recipe / cookbook app written in Go — *not* a finance tool (the name is a pun).

## Links
- **Code:** `~/dev/cookthebooks` · [remote](https://github.com/z-br/cookthebooks)
- **Live (prod):** https://cookthebooks.app (web) · https://api.cookthebooks.app (API) — on Coolify since 2026-06-20
- **Ops/backup:** repo `docs/operations.md` · **Infra:** [[deployment]]

## Stack
Go API · Next.js web (`web/`) · OpenSearch (search) · Postgres · AWS S3 + CloudFront (images)

## Status (2026-06-20)
**Migrated off DigitalOcean onto the Coolify home cloud** as a Docker-Compose
stack (api + web + opensearch), shared Postgres (schema `cookthebooks`), behind
the Cloudflare tunnel on temporary **lab** domains. All data migrated
(OpenSearch from the DO droplet, Postgres from **Neon**) and verified end-to-end.
- **Next:** confirm backups (PG covered daily; see `docs/operations.md`), then
  **cut over prod** to this instance and decommission DO + Neon.
- **DNS cleanup:** several `cookthebooks.app` records didn't survive the Route 53
  → Cloudflare move (ACM validation, SES/Postmark DKIM, `cdn-images`); see
  [[deployment]] Outstanding.

## Overview
Recipe/cookbook manager — stores and searches recipes (OpenSearch-backed), with a `kitchen/` domain layer and a `web/` frontend. The most substantial of the older repos: **509 commits**, your earliest work here (authored as `Greg Passmore`). Dormant since Sep 2024 until the 2026-06 Coolify migration.

## Related
-
