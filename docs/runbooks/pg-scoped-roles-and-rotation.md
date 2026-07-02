# Runbook: scoped PG roles + admin password rotation

> **Why:** the shared-Postgres admin DSN was exposed in chat transcripts, and
> recon (07/02/2026, via Coolify API) found **all three DB-using apps connect as
> the `postgres` admin role** — cookthebooks api (schema `cookthebooks`),
> roselight web + worker (schema `public`). Nothing uses the scoped-role model
> yet. Fix both, in this order: scoped roles first, rotation second — then no
> user-facing app depends on admin creds at rotation time.
>
> **Prereq:** must be on the Mac mini's LAN (`ssh -i ~/.ssh/id_ed25519
> cosmo@192.168.50.138` — unreachable from other networks; that's what blocked
> this on 07/02/2026). No secrets in this file — generate passwords at
> execution time.

## Facts

| Piece | Value |
|---|---|
| PG container / internal host | `tkinv01f2fpidwklct4n9a8o`, db `postgres`, internal-only |
| Host docker | `export DOCKER_HOST=unix:///Users/cosmo/.colima/default/docker.sock DOCKER_CONFIG=/tmp/dkrcfg; /opt/homebrew/bin/docker …` |
| cookthebooks api | Coolify app `rkcylgmt8cc2n9d1e5ej527r` — envs `DB_USER`/`DB_PASSWORD`, `DB_SCHEMA=cookthebooks` (in-app `CREATE SCHEMA` + `search_path`) |
| roselight web | app `ckgxbv0nlhgh0v52jxh2ul07` — `DATABASE_URL` (Prisma, schema `public`, no `?schema=` param) |
| roselight worker | app `m6zinqoryw8iivs2vcptxusa` — same `DATABASE_URL`; heartbeat → healthchecks.io (~15m grace) |
| Coolify API | `https://ccc.zebraproject.org`, token in `~/.config/ship-webapp/config.env` |
| Backup job | id `wsok6jma1m44thioe1ae009t`, daily 07:05 UTC → S3; runs *inside Coolify* with the stored DB resource password — must be updated on rotation |

## 1. Create scoped roles

`psql` in: `docker exec -it tkinv01f2fpidwklct4n9a8o psql -U postgres -d postgres`

```sql
-- generate two strong passwords first (e.g. openssl rand -base64 24)
CREATE ROLE cookthebooks_app LOGIN PASSWORD '<pw1>' NOSUPERUSER NOCREATEDB NOCREATEROLE;
CREATE ROLE roselight_app    LOGIN PASSWORD '<pw2>' NOSUPERUSER NOCREATEDB NOCREATEROLE;

-- cookthebooks: own its schema + everything in it
ALTER SCHEMA cookthebooks OWNER TO cookthebooks_app;
-- per-object ownership (run \dt cookthebooks.* first; loop tables/sequences):
--   SELECT format('ALTER TABLE %I.%I OWNER TO cookthebooks_app;', schemaname, tablename)
--   FROM pg_tables WHERE schemaname='cookthebooks'; \gexec
--   (repeat for sequences via pg_sequences, views via pg_views)
ALTER ROLE cookthebooks_app SET search_path = cookthebooks;

-- roselight: owns the objects in public (Prisma migrations need ownership)
--   same \gexec pattern over schemaname='public' → OWNER TO roselight_app;
ALTER ROLE roselight_app SET search_path = public;
GRANT USAGE, CREATE ON SCHEMA public TO roselight_app;   -- Prisma migrate needs CREATE

-- isolation
REVOKE ALL ON SCHEMA cookthebooks FROM PUBLIC;
REVOKE CREATE ON SCHEMA public FROM PUBLIC;
```

Sanity: `\du`, `\dn+`, then `psql -U cookthebooks_app` / `-U roselight_app` and
confirm each sees only its schema (`\dt` / `SELECT current_schemas(true);`).

## 2. Switch apps to scoped roles (Coolify API, one at a time)

For each app: PATCH env → deploy → verify → next.

- **cookthebooks** (`rkcylgmt8cc2n9d1e5ej527r`): set `DB_USER=cookthebooks_app`,
  `DB_PASSWORD=<pw1>`. Deploy. Verify `https://cookthebooks.app` +
  `https://api.cookthebooks.app` 200 and a search works (exercises DB).
- **roselight web** (`ckgxbv0nlhgh0v52jxh2ul07`): `DATABASE_URL=
  postgres://roselight_app:<pw2>@tkinv01f2fpidwklct4n9a8o:5432/postgres?schema=public`
  (make the schema explicit while touching it). Deploy. Verify
  `https://lab.theroselight.org` 200 with stories rendering.
- **roselight worker** (`m6zinqoryw8iivs2vcptxusa`): same URL. Deploy. Verify the
  healthchecks.io heartbeat stays green through the next ~15m window (worker is
  the sole RSS ingester).

Env PATCH shape: `PATCH /api/v1/applications/<uuid>/envs` with
`{"key":"...","value":"..."}`; deploy via `POST /api/v1/deploy?uuid=<uuid>`;
poll `deploy-status`. (Or `~/.claude/skills/ship-webapp/scripts/coolify.sh`.)

## 3. Rotate the admin password

Only after all three apps are healthy on scoped roles.

```sql
ALTER USER postgres PASSWORD '<new-admin-pw>';
```

Then, same sitting:
1. **Coolify DB resource** — update the stored password on `standalone-postgresql`
   (UI: resource → password field; this is what the daily S3 backup job uses).
2. **`~/.config/ship-webapp/config.env`** — nothing to change (it stores no
   password; keep it that way). Put the new admin password in a password
   manager, not in a repo/transcript.
3. **Old creds are dead:** confirm `psql -U postgres` with the old password fails.

## 4. Verify + close out

- All apps `running:healthy`; cookthebooks search OK; roselight front page fresh;
  worker heartbeat green.
- Next 07:05 UTC backup run shows `success` + `s3_uploaded` (check the morning
  after; it's the one consumer of the rotated password besides humans).
- Hub `docs/deployment.md`: check off the two Outstanding items (rotation,
  scoped role), correct the claim that schema-per-project isolation was in
  effect (roselight was on admin/public), and add the **wallacecourts** app
  (`becbj1x0ufc3hxjt09nitc4n`, no DB) to the Deployed apps table — found
  deployed but undocumented.
- Worklog entries in `theroselight.org` and `cookthebooks` repos.
