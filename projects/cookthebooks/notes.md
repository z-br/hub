# cookthebooks — Notes

> Dated journal: decisions, progress, dead-ends. Newest on top.

## 2026-06-21 — old infra decommissioned
- Deleted **Neon** (Postgres), **Vercel** (web), **DigitalOcean** (droplet OpenSearch
  + App Platform `api2`). Stale DNS (`search`/`api2`/`app`/`dev-api`/`ssr`) removed —
  no dangling CNAMEs. Coolify now deploys from `main`. **Kept:** AWS S3 + CloudFront
  (`cdn-images`) for images. Remaining: S3 upload keys, scoped DB role, rotate GCP key.

## 2026-06-20 — PROD cutover complete
- Flipped `cookthebooks.app` + `www` (was Vercel) and `api.cookthebooks.app` (new;
  old prod API was `api2` on DO App Platform) → the Cloudflare tunnel → Coolify.
  Verified: apex serves Coolify Next.js (no `x-vercel`), API + Postgres + OpenSearch
  all 200. Web rebuilt with `NEXT_PUBLIC_API=https://api.cookthebooks.app`.
- **Remaining:** test OAuth login; decommission Vercel + DO (`api2`, droplet) + Neon;
  S3 upload keys; scoped DB role; rotate leaked/chat-shared secrets; retire lab domains.

## 2026-06-20 — migrated to Coolify home cloud
- Moved off DigitalOcean → **Coolify Docker-Compose** stack (Go API + Next.js web
  + OpenSearch) on lab domains `lab` / `api-lab.cookthebooks.app`.
- **Data migrated:** OpenSearch indices (`prod_common/recipes/cookbooks`) from the
  DO droplet via `elasticdump`; Postgres from **Neon** (`cookthebooks` db) into the
  shared Coolify PG `cookthebooks` schema via a scratch-DB schema-rename. Verified
  search + Postgres-backed endpoints end-to-end.
- **Code changes** (branch `claude/infallible-mahavira-14b332`): ES client honors
  endpoint scheme + optional auth; DB `DB_SSLMODE` + `DB_SCHEMA` (in-app schema
  isolation); Dockerfiles + `docker-compose.yml`; ops/backup docs.
- **Backups:** shared PG is on Coolify's daily 07:05 UTC → S3 backup (covers the
  `cookthebooks` schema). ⚠️ data loaded after that day's run, so first backup with
  data = next run. OpenSearch volume not backed up (rebuildable from PG). Details
  in repo `docs/operations.md`.
- **Open:** real secrets (running on placeholders), scoped `cookthebooks_app` DB
  role (using admin), Route 53→Cloudflare DNS gaps, then **prod cutover**.
- **Leaked secret:** a Google service-account key is committed in the repo — rotate
  + purge (separate task).

## 2026-06-14
- Added to the hub. Stack auto-detected: Go.
