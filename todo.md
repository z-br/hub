# ✅ Cross-Project TODO — Rollup

> Hand-curated. The **important / cross-cutting** tasks across all projects, each
> linking back to its project. Per-project source of truth lives in each
> `projects/<name>/todo.md`. Keep this short — if everything's here, nothing is.

## 🔥 Now
- [ ] 🔒 **Rotate the shared-Postgres admin password** — its DSN was exposed in a chat transcript during setup → [deployment](docs/deployment.md)
- [ ] **Deploy infra:** set `COOLIFY_SERVER_SSH` in `~/.config/ship-webapp/config.env`, then `pg-provision.sh check`; confirm DB↔app Coolify network → [deployment](docs/deployment.md)
- [ ] **The Rose Light:** review the 4 rebased design previews (PRs #8/#48/#49/#22 → `<PR#>.theroselight.xyz`) — pick a direction or close them → [roselight](projects/roselight/todo.md)
- [ ] **The Rose Light events, Phase 0:** review + send the [Everout outreach email](projects/roselight/everout-outreach-draft.md), then greenlight shadow ingestion → [events-plan](projects/roselight/events-plan.md)

## 📌 Soon
- [ ] Pin down **Wallace Park Tennis** purpose (schedule? ladder? booking?) → [wallacecourts](projects/wallacecourts/readme.md)
- [ ] Decide: build **blog** or fold it into **homepage** → [blog](projects/blog/readme.md)
- [ ] Triage [`inbox.md`](inbox.md)

## 🧊 Someday
- [ ] Revisit **cookthebooks** (recipe app, 509 commits, dormant) — resurrect or archive?

## 🌐 Cross-cutting / themes
- **Houseboard:** one product — Next.js web + native SwiftUI iOS in a monorepo (RN app retired).
- **famfam:** separate — a family-history wiki engine, unrelated to Houseboard.
- **Health:** HRV + the CPAP reference repos — one body of work.
- **Claude-powered:** The Rose Light · famfam (both use the Anthropic API).
- **Public-facing:** homepage · blog · The Rose Light.
- **Deploy:** shared Coolify + Cloudflare Tunnel infra → [`docs/deployment.md`](docs/deployment.md). homepage 🟢 live; roselight/famfam are next candidates.
