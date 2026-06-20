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

## Outstanding
- [ ] 🔒 **Rotate the shared-Postgres admin password** — its DSN was shown in a chat
  transcript during setup.
- [ ] ⛔ **Postgres provisioning untested** — `COOLIFY_SERVER_SSH` still blank in
  config; can't run `pg-provision.sh check` until it's set.
- [ ] 🟡 **App↔DB network** — confirm apps share a Coolify network with the shared
  Postgres so `tkinv…:5432` resolves at runtime.
- [ ] 🟡 **Homepage content** still placeholder (`lib/data.ts`).

## Next steps
- [ ] Set `COOLIFY_SERVER_SSH` in `~/.config/ship-webapp/config.env`, then `pg-provision.sh check`.
- [ ] Confirm DB↔app Coolify network connectivity.
- [ ] (Optional) test-provision a schema end-to-end.
- [ ] Fill real content in homepage `lib/data.ts` (push auto-redeploys).
