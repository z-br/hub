# Tennis Workout Builder — TODO

> Source of truth for this project's tasks. Promote the important/cross-cutting
> ones to the root [`/todo.md`](../../todo.md) rollup.

## Now
- [ ] Merge `claude/concept-website-plan-f8dcb1` branch to `main` (deploys track `main`)
- [ ] Deploy via the `ship-webapp` skill: Coolify project `tennisworkout`, Nixpacks app, FQDN `http://tennis.zebraproject.org`, port 3000, `GET /` healthcheck
- [ ] `pg-provision` schema `tennisworkout` + role `tennisworkout_app` on the shared Postgres (DATABASE_URL auto-wired)
- [ ] Set `ADMIN_TOKEN` env on the Coolify app (generate: `openssl rand -hex 24`)
- [ ] Add Cloudflare Tunnel route `tennis.zebraproject.org` → `http://localhost:80`; verify `curl -s -o /dev/null -w '%{http_code}' https://tennis.zebraproject.org` → `200`
- [ ] Run `scripts/seed-founding-plan.ts` against the prod `DATABASE_URL`; verify the founding plan leads the live gallery
- [ ] Update hub `docs/deployment.md` Deployed apps table once live

## Next (post-v1 backlog)
- [ ] Grow exercise library from ~59 entries to 80–120
- [ ] AI-assisted plan refinement step in the wizard
- [ ] PWA polish (offline install)
- [ ] Per-protocol "done" tracking (today multiple daily protocols share a single date-keyed done flag — known limitation from task 12)
- [ ] Remix name-length guard (remix `' (remix)'` suffix can push a plan name past the 80-char limit, degrading to a publish-time error banner — known issue from task 13)

## Done
- [x] Designed + built full app in one session: spec + 18-task implementation plan, all reviews clean (2026-08-01)
- [x] Production `start` script runs migrations on boot; `README.md` and `WORKLOG.md` written for the shipped app
- [x] Added to the hub (2026-08-01)
