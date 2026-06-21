# Convention: per-repo agent worklog (`WORKLOG.md`)

Every project tracked in this hub keeps a running log of **agent activity and
work performed** — with timestamps — inside its own code repo. This is the
canonical definition; per-repo agent instructions point back here.

## Where it lives

- A `WORKLOG.md` at the **root of the code repo** (not in the hub).
- The hub holds status/notes/ideas; the repo holds the worklog. Keeping it in the
  repo means the log travels with the code, shows up in diffs/PRs, and is in front
  of whatever agent is working there.

## Why not `CHANGELOG.md`?

A `CHANGELOG.md` conventionally means user-facing **release notes** (Keep a
Changelog / semver). This is different: it's a development **activity log** — what
an agent changed and why, session by session. We name it `WORKLOG.md` to avoid
that collision. A repo can still have a real `CHANGELOG.md` for releases.

## Format

Newest on top. Each entry is a single timestamped bullet — `MM/DD/YYYY HH:MM`.

```markdown
# <Project> — Worklog

> Running log of agent activity and work performed. Newest on top.
> Convention: ~/dev/hub/docs/conventions/worklog.md

- 06/20/2026 21:40 — Switched session auth to httpOnly cookies; fixed logout not clearing state.
- 06/20/2026 14:05 — Added Prisma index on `article.publishedAt` (list query was doing a full scan).
- 06/19/2026 18:22 — Scaffolded the worker queue; wired it to the digest cron.
```

- **Timestamp:** `MM/DD/YYYY HH:MM`, local time, at the start of each bullet.
- **Entry:** what changed and *why*, in one line. Link files/PRs when useful.
- **Attribution (optional):** add `(agent)` or a tool name if it helps later.

## When to write an entry

- After any **substantive change** — code, config, schema, infra, or docs —
  append an entry **before finishing the session**.
- **Skip** read-only or exploration-only sessions. This logs work performed, not
  every visit.
- Don't batch a week of work into one line; one entry per meaningful change is the
  target.

## How a repo enforces this

The instruction lives in the repo's agent file so whatever agent opens the repo
sees it:

- `AGENTS.md` carries a `## Worklog discipline` section (covers Codex and other
  agents).
- `CLAUDE.md` contains `@AGENTS.md` so Claude Code — which reads `CLAUDE.md` —
  picks up the same instruction.

New projects inherit this: see [`projects/_TEMPLATE/`](../../projects/_TEMPLATE).
