# Tennis Workout Builder — Notes

> Dated journal: decisions, progress, dead-ends. Newest on top.

## 2026-08-01 (deploy)
- **Shipped to prod:** fast-forward merged to `main` (28 commits), pushed, deployed via ship-webapp — Coolify project `vyx0nfg1j0lvcf95bxeb3vjn`, app `nsxys5fchjnqxcgnhippepaj` (Nixpacks, port 3000), tunnel route + proxied CNAME. Live: https://tennis.zebraproject.org.
- **Deploy gotchas hit:** (1) first build failed — `npm ci` rejected the macOS-generated lockfile (missing Linux-side optional dep `@emnapi/runtime` from `@resvg/resvg-js`); fixed by regenerating `package-lock.json` from scratch + `.nvmrc`=24 (nixpacks had picked node 22.19 < react-router 8's required 22.22). (2) `pg-provision.sh` unusable — `COOLIFY_SERVER_SSH` unset AND the mini unreachable from this network; wired `DATABASE_URL` from the Coolify API's internal DB URL instead (admin `postgres` role — scoped-role follow-up in todo). (3) Coolify env POST created BOTH prod and preview rows for `ADMIN_TOKEN`; naive jq lookup captured both lines → token mismatch until filtered `is_preview==false`.
- **Seeding without SSH:** no exec endpoint on this Coolify version, so the founding plan was published through the live app's own publish action (`POST /plan/x/edit` with the doc as form data) and featured via the admin action. Live at `/p/dJl8iS9qtp`, leads the gallery with the Featured badge. Boot-time migrations (`npm run start` → `db:migrate`) created schema + table on first deploy, as designed.

## 2026-08-01
- Designed and built the whole app in one session: brainstorm → design spec → 18-task implementation plan, executed end-to-end via subagents (2026-07-31 through 2026-08-01). All 18 tasks report clean reviews (a few noted-but-accepted minor issues, tracked in `todo.md`'s Next section). Spec and plan are committed in-repo under `docs/superpowers/`. 103 Vitest tests (92 unit, 11 Postgres-backed) plus a Playwright smoke test, all green; `npm run build` passes. Not yet pushed to GitHub or deployed — that's queued as the next step.
- Added to the hub. Stack: React Router (framework mode) · TypeScript · Tailwind CSS v4 · Zod · postgres.js · idb · Vitest · Playwright.
