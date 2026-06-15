# FamilyHub

> **Status:** 🟢 active · **Updated:** 2026-06-14
> Status legend: 🟢 active · 🟡 paused · ⚪ idea · ✅ done · ⚫ retired

**One-liner:** Family task-management app — a monorepo with a Next.js web app and a native SwiftUI iOS app sharing one backend.

## Links
- **Code:** `~/dev/Familyhub` · [remote](https://github.com/z-br/Familyhub)
- **Live:** _tbd_

## Stack
Monorepo (`apps/web`, `apps/ios`) · Next.js · NextAuth · Prisma · Postgres · Zod · **native SwiftUI** (iOS)

## Overview
Shared family task management (to-dos / chores / coordination). Pivoted from a separate React Native app to a **monorepo**: `apps/web` (Next.js + NextAuth + Prisma) serves the API and web UI; `apps/ios` is a **native SwiftUI** client. API routes accept Bearer tokens so the iOS app can authenticate. Docs live in the repo (`README.md`, `STACK.md`, `CLAUDE.md`).

> ⚠️ **Repo state:** currently on branch `claude/cool-davinci-FXdiJ` (no `main`), with ~438 uncommitted changes (mostly the old React Native app being deleted). Clean this up — commit/branch to `main` — before more work. See [todo](todo.md).

## History
- The standalone **FamilyHubMobile** (React Native/Expo) repo was **retired and deleted** 2026-06-14 — superseded by the SwiftUI app in `apps/ios`.

## Related
- _Not_ related to [[famfam]] (a family-history wiki, different project).
