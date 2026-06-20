# 🧭 Hub

My markdown-centric command center for all ongoing projects. **Code lives in its own repos; this is the brain** — status, notes, ideas, and the cross-project task view.

- **Cross-project tasks:** [`todo.md`](todo.md) — the rollup
- **Quick capture:** [`inbox.md`](inbox.md) — dump now, sort later
- **Deployment:** [`docs/deployment.md`](docs/deployment.md) — shared Coolify + Cloudflare Tunnel infra
- **Start a project:** copy [`projects/_TEMPLATE/`](projects/_TEMPLATE) → `projects/<name>/`

Status legend: 🟢 active · 🟡 paused · ⚪ idea · ✅ done · ⚫ retired

## Projects

### 🟢 Active
| Project | What | Code |
|---|---|---|
| [The Rose Light](projects/roselight/readme.md) | Self-curating Portland news aggregator (React Router/Claude) | `~/dev/theroselight.org` |
| [Homepage](projects/homepage/readme.md) | Personal landing — "Greg's Workshop", Y2K/synthwave (Next.js) | `~/dev/homepage` |
| [Houseboard](projects/houseboard/readme.md) | Family task app — monorepo, Next.js web + SwiftUI iOS (was FamilyHub) | `~/dev/Houseboard` |
| [famfam](projects/famfam/readme.md) | Family-history wiki engine — Claude-organized memories (Next.js) | `~/dev/famfam` |

### 🟡 Paused
| Project | What | Code |
|---|---|---|
| [Wallace Park Tennis](projects/wallacecourts/readme.md) | Wallace Park tennis courts site (Vite/React) | `~/dev/wallacecourts` |
| [HRV / Sleep-Health](projects/hrv/readme.md) | HRV + VPC/APC from Polar H10 ECG; CPAP reference repos | `~/dev/hrv` |

### ⚪ Older / ideas
| Project | What | Code |
|---|---|---|
| [cookthebooks](projects/cookthebooks/readme.md) | Go recipe/cookbook app (Elasticsearch search) | `~/dev/cookthebooks` |
| [blog](projects/blog/readme.md) | Astro blog — scaffolded, not built out | `~/dev/blog` |

> Removed/retired 2026-06-14: **FamilyHubMobile** (deleted — superseded by Houseboard's SwiftUI iOS app), **project-hub** (deleted — predecessor to this hub), **menagerie** & **storyshop** (dropped from tracking), **databaseball_v1** (not mine — deleted).

## How this works
- Each project folder has four files: `readme.md` (overview + status + links), `todo.md` (its tasks), `notes.md` (dated journal), `ideas.md` (backlog).
- A project's `todo.md` is the **source of truth** for its tasks. The root [`todo.md`](todo.md) is a **hand-curated rollup** of the cross-cutting / high-priority ones, linking back.
- Tasks use GitHub task lists (`- [ ]`) so they render as checkboxes on GitHub and in editors.
- Link between projects with `[[project-slug]]` wiki-style references.
