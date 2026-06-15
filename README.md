# 🧭 Hub

My markdown-centric command center for all ongoing projects. **Code lives in its own repos; this is the brain** — status, notes, ideas, and the cross-project task view.

- **Cross-project tasks:** [`todo.md`](todo.md) — the rollup
- **Quick capture:** [`inbox.md`](inbox.md) — dump now, sort later
- **Start a project:** copy [`projects/_TEMPLATE/`](projects/_TEMPLATE) → `projects/<name>/`

Status legend: 🟢 active · 🟡 paused · ⚪ idea · ✅ done · ⚫ retired

## Projects

### 🟢 Active
| Project | What | Code |
|---|---|---|
| [The Rose Light](projects/roselight/readme.md) | Self-curating Portland news aggregator (React Router/Claude) | `~/dev/theroselight.org` |
| [Homepage](projects/homepage/readme.md) | Personal landing — "Greg's Workshop" (Next.js) | `~/dev/homepage` |
| [FamilyHub (web)](projects/familyhub/readme.md) | Family task-management app + API (Next.js) | `~/dev/Familyhub` |
| [FamilyHub Mobile](projects/familyhub-mobile/readme.md) | Mobile (iOS) client for FamilyHub (Expo/RN) | `~/dev/FamilyHubMobile` |
| [famfam](projects/famfam/readme.md) | Family-history wiki engine — Claude-organized memories (Next.js) | `~/dev/famfam` |

### 🟡 Paused
| Project | What | Code |
|---|---|---|
| [Wallace Park Tennis](projects/wallacecourts/readme.md) | Tennis courts app (Vite/React) | `~/dev/wallacecourts` |
| [HRV / Sleep-Health](projects/hrv/readme.md) | HRV + VPC/APC from Polar H10 ECG; CPAP reference repos | `~/dev/hrv` |

### ⚪ Older but relevant
| Project | What | Code |
|---|---|---|
| [menagerie](projects/menagerie/readme.md) | Remix app — purpose TBD | `~/dev/menagerie` |
| [storyshop](projects/storyshop/readme.md) | Remix/Prisma web app | `~/dev/storyshop` |
| [cookthebooks](projects/cookthebooks/readme.md) | Go finance/accounting tool | `~/dev/cookthebooks` |
| [blog](projects/blog/readme.md) | Personal blog (Astro) | `~/dev/blog` |

### ⚫ Retired
| Project | What | Code |
|---|---|---|
| [project-hub](projects/project-hub/readme.md) | Empty predecessor to this hub — safe to delete | `~/dev/project-hub` |

## How this works
- Each project folder has four files: `readme.md` (overview + status + links), `todo.md` (its tasks), `notes.md` (dated journal), `ideas.md` (backlog).
- A project's `todo.md` is the **source of truth** for its tasks. The root [`todo.md`](todo.md) is a **hand-curated rollup** of the cross-cutting / high-priority ones, linking back.
- Tasks use GitHub task lists (`- [ ]`) so they render as checkboxes on GitHub and in editors.
- Link between projects with `[[project-slug]]` wiki-style references.
