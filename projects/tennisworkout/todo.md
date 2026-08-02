# Tennis Workout Builder — TODO

> Source of truth for this project's tasks. Promote the important/cross-cutting
> ones to the root [`/todo.md`](../../todo.md) rollup.

## Now
- [ ] 🔒 **Scoped DB role** — the app connects to the shared Postgres as the **admin `postgres` role** (`pg-provision.sh` needs `COOLIFY_SERVER_SSH`, which is unset, and the Mac mini was unreachable from the deploy network). Same lab shortcut as cookthebooks. When on the home network: provision role `tennisworkout_app` owning schema `tennisworkout`, swap `DATABASE_URL`, redeploy.
- [ ] Delete the duplicate preview-env `ADMIN_TOKEN` row on the Coolify app (uuid `s11u6ysr4fi2kd99hi3820om`) — two rows made API token lookup ambiguous during seeding
- [ ] Injury `"high"` load exclusion is currently vacuous — no library entry uses `"high"`, so injury flags only re-rank. Revisit when growing the library (final-review note)

## Next (post-v1 backlog)
- [ ] Grow exercise library from ~59 entries to 80–120
- [ ] AI-assisted plan refinement step in the wizard
- [ ] PWA polish (offline install)
- [ ] "Worse next morning" gate input + explicit "gate stayed green?" phase-advance prompt (spec §6 v1 simplifications)
- [ ] Remix name-length guard (remix `' (remix)'` suffix can push a plan name past the 80-char limit, degrading to a publish-time error banner)

## Done
- [x] Designed + built full app in one session: spec + 18-task implementation plan, all reviews clean (2026-08-01)
- [x] Production `start` script runs migrations on boot; `README.md` and `WORKLOG.md` written for the shipped app
- [x] Added to the hub (2026-08-01)
- [x] Merged to `main` + pushed (fast-forward, 28 commits) (2026-08-01)
- [x] Deployed via ship-webapp: Coolify project + Nixpacks app, `ADMIN_TOKEN` + `DATABASE_URL` envs, tunnel route + DNS — live at https://tennis.zebraproject.org (2026-08-01)
- [x] Fixed deploy blocker: lockfile regenerated (missing Linux-side `@emnapi/runtime` optional dep) + `.nvmrc` node 24 (2026-08-01)
- [x] Seeded + featured the founding plan (`/p/dJl8iS9qtp`) via the live app's own publish/admin actions — no SSH needed (2026-08-01)
- [x] Updated hub `docs/deployment.md` Deployed apps table (2026-08-01)
