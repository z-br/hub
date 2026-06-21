# 🚀 Deployment (Coolify + Cloudflare Tunnel)

Shared hosting setup for all projects. **Code lives in each project's repo; this
documents the common infra.** Automated by the `ship-webapp` Claude skill
(`~/.claude/skills/ship-webapp/`). No secrets here — those live in
`~/.config/ship-webapp/config.env` (outside any repo).

Status legend: 🟢 done · 🟡 in progress · ⛔ blocked

## Infrastructure
| Piece | Value |
|---|---|
| Coolify API | `https://ccc.zebraproject.org` |
| Server | `localhost` · `pf5pdmdxtiuuwqj563yl4l04` · `10.0.1.1` (single host) |
| Build | Nixpacks (`next build` / `next start`, no Dockerfile) |
| Source | GitHub (`z-br/*`), deploys from `main` via Coolify GitHub App `handsome-hummingbird` (installation `117895640`, all-repo access) |
| Tunnel | Cloudflare **`TheRoseLight`** (`958ef22e-…`), remotely-managed, on the Coolify host → Traefik `localhost:80` |
| DNS/TLS | Cloudflare terminates TLS at edge; apps use **`http://` domains** in Coolify |
| Shared DB | `standalone-postgresql`, container/host `tkinv01f2fpidwklct4n9a8o`, db `postgres`, **internal-only** |

## Decisions
- **One Coolify project per deploy** (not a shared bucket).
- **Cloudflare via API** (no SSH / no local `config.yml`).
- **App domains are `http://`** — `https://` makes Traefik chase a Let's Encrypt
  cert it can't get behind the tunnel, and the app never goes healthy.
- **DB = schema-per-project** on the shared Postgres: schema `<project>` + login
  role `<project>_app`, isolated via `search_path` + no cross-schema access.
- **DB admin over SSH + `docker exec`** (DB isn't public); `DATABASE_URL` is
  auto-wired into the app env + redeploy.
- **`GET /` healthcheck on by default** for new apps.

## Per-app flow (the skill)
1. push to `main`
2. `coolify.sh create-project <app>` → dedicated project
3. `coolify.sh create-app … --fqdn http://<sub>.<domain> --port <p>` (healthcheck on)
4. *(if it needs a DB)* `pg-provision.sh provision <app> --app <uuid>`
5. `coolify.sh deploy <uuid>` → poll `deploy-status`
6. `cf-tunnel.sh route --fqdn <sub>.<domain> --service http://localhost:80`
7. verify `curl https://<sub>.<domain>` → 200

## Deployed apps
| App | URL | Project / App uuid | State |
|---|---|---|---|
| [[homepage]] | https://home.zebraproject.org | `y5nolfv9…` / `j1qzqnjl…` | 🟢 live, `running:healthy`, `/` healthcheck |
| [[cookthebooks]] | https://cookthebooks.app (web) · https://api.cookthebooks.app (API) | `p9rf8cepkn1dhgh0yqs3zml0` / `rkcylgmt8cc2n9d1e5ej527r` | 🟢 **live on PROD** (cut over from Vercel/DO 2026-06-20; Go API + Next.js web + OpenSearch + shared PG). Lab domains still route as fallback. |

### cookthebooks — a second deploy pattern (Docker Compose from git)
homepage uses the Nixpacks per-app flow above; cookthebooks needed a multi-service
stack, so it uses Coolify's **Docker Compose** build pack instead:
- One git repo (`z-br/cookthebooks`) with `Dockerfile`, `web/Dockerfile`,
  `docker-compose.yml` → one Coolify "Docker Compose" application = opensearch +
  api + web. Per-service routing via **explicit Traefik labels** (the
  `SERVICE_FQDN_*` magic kept regenerating sslip domains).
- **Shared Postgres without SSH/`pg-provision.sh`:** the compose `api` service
  joins the **external `coolify` network**, so it resolves
  `tkinv01f2fpidwklct4n9a8o:5432` directly. Schema isolation is done **in-app**
  (`DB_SCHEMA` → `CREATE SCHEMA` + `search_path` on boot) instead of pre-provisioning.
  ⟹ This **answers the old "App↔DB network" open question: join the `coolify`
  network.** Connection uses `sslmode=disable` (internal, plain).
- **`cookthebooks.app` is a separate Cloudflare zone** (not `zebraproject.org`);
  `CF_API_TOKEN` can't see it, so its DNS records are added by hand (or need a
  scoped token). Tunnel **ingress** edits still work (account-level).

### Host access (for ops / data migration, no Coolify UI)
The Coolify host is **Colima on a Mac mini**: `ssh -i ~/.ssh/id_ed25519
cosmo@192.168.50.138`, then use the Mac's `/opt/homebrew/bin/docker` (colima
context) directly — `colima ssh` fails non-interactively (lima not on PATH).
Override for scripts: `DOCKER_HOST=unix:///Users/cosmo/.colima/default/docker.sock`
and `DOCKER_CONFIG=/tmp/dkrcfg` (empty `{}`, to skip the `credsStore=desktop` helper).

## Backups
- **Shared Postgres: Coolify scheduled backup** id `wsok6jma1m44thioe1ae009t` —
  `5 7 * * *` (**daily 07:05 UTC**), dumps db `postgres` (**all schemas, incl.
  every project's**) → **S3** (`s3_storage_id=1`), retention **7 / 7 days**,
  local disabled. Last run verified `success` + `s3_uploaded`. This covers all
  schema-per-project apps automatically.
- **Service volumes are NOT backed up** (Coolify backs up databases only). e.g.
  cookthebooks' OpenSearch volume — treat such data as derived/rebuildable, or
  add a separate dump job.
- **Coolify's own config/secrets ARE backed up too** (separate from the app-data
  job): `coolify-db` (resource configs, env vars, encrypted secrets) is dumped
  **daily → S3, ~520 KB, success** (confirmed in the UI; it's an internal backup,
  not shown in `/api/v1/databases`). ⚠️ The dump's secrets are encrypted with
  **`APP_KEY`** (`/data/coolify/source/.env`) — keep that off-box or a restore
  onto a fresh Coolify can't decrypt them. (Applies to all projects on this host.)

## Outstanding
- [ ] 🔒 **Rotate the shared-Postgres admin password** — its DSN was shown in a chat
  transcript during setup. (cookthebooks currently connects as this admin role —
  see below.)
- [x] ✅ **App↔DB network** — resolved: join the external **`coolify`** network;
  the shared PG resolves at `tkinv01f2fpidwklct4n9a8o:5432` (cookthebooks proves it).
- [ ] 🟡 **Homepage content** still placeholder (`lib/data.ts`).
- [ ] 🟡 **cookthebooks → scoped DB role** — it uses the PG **admin** role as a lab
  shortcut; create a `cookthebooks_app` role owning only its schema.
- [ ] 🟡 **cookthebooks cutover** — move off **lab** domains to prod (real secrets,
  prod DNS/ingress, OAuth redirect URIs), then decommission DO droplet + Neon PG.
- [x] ✅ **cookthebooks DNS gaps (Route 53 → Cloudflare)** — fixed 2026-06-20:
  ACM cert-validation CNAME, 3× SES DKIM, Postmark DKIM, `cdn-images` →
  `dpxmcvxvm8wor.cloudfront.net` (grey), `pm-bounces` set grey. CDN image URLs
  verified 200 from CloudFront.

## Notes
- `COOLIFY_SERVER_SSH` is still blank in config (the `ship-webapp` scripts'
  `pg-provision.sh` path is untested), **but** the Docker-Compose pattern doesn't
  need it — see "Host access" above for the working Colima route.
