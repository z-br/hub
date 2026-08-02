# Convention: the hub is the single source of truth (civic-stack context)

GZ's civic work (Rose Light, Better Burnside, civic research) spans local repos, cloud
Claude sessions, and this hub. **Everything durable lives in the hub.** Nothing is stored
long-term in Claude's cloud. Any agent (Claude Code locally, Cowork/cloud sessions,
anything else) that touches these projects follows this.

## Where truth lives

| Kind of thing | Canonical home |
|---|---|
| **State**: status, decisions, next actions, contacts, meeting outcomes | `projects/civic-stack/` + each project's `readme/todo/notes` |
| **Research**: cited docs (bridge facts, vacancy inventory, lessons, plans) | `projects/civic-stack/research/` |
| **Code** | Each repo (`~/dev/burnside`, `~/dev/theroselight.org`), with `WORKLOG.md` |

## The rules

1. **Cloud sessions are workers, not archives.** A Cowork/cloud session may draft and
   research freely, but every durable output lands in the hub via the device bridge
   before the session ends. The cloud's attached project holds at most a pointer doc
   ("canonical in ~/dev/hub") — never content.
2. **Bridge offline = deliver, don't store.** If the device bridge is down at session end,
   the session delivers files to the chat (download) and tells GZ exactly where they
   belong in the hub; it does NOT leave them in cloud storage as the plan of record.
3. **End-of-session sync.** Any session that changes project state updates the hub before
   finishing: dated `notes.md` entry, `todo.md` checkboxes, `readme.md` Updated line.
4. **Starting civic work, read the hub first.** Local Claude Code: start at
   `projects/civic-stack/readme.md`. Cloud sessions: ask GZ to connect `~/dev/hub`
   (and stage `projects/civic-stack/`) before doing civic work; do not reconstruct
   context from memory or cloud leftovers.
5. **Research docs carry their own trust markers**: "as of" dates, sources, correction
   log. A doc without a date line is treated as unverified.
6. **Tasks live in hub todos only.** Root `todo.md` stays the hand-curated rollup.

## Why
- Local Claude Code — where code work happens — can't read Claude's cloud storage.
- The hub is git-tracked, greppable, portable, and vendor-independent.
- One brain, no drift. The cost: cloud sessions must have the hub folder connected to be
  fully useful — that's the trade GZ chose deliberately.
