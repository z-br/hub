# USTA Plus

> **Status:** 🟢 active · **Updated:** 2026-11-07
> Status legend: 🟢 active · 🟡 paused · ⚪ idea · ✅ done · ⚫ retired

**One-liner:** Userscript that turns playtennis.usta.com tournament draws into a phone-friendly "My matches" view with next match and calendar export.

## Links
- **Code:** `~/dev/usta-plus` (local only, no remote yet)
- **Live:** none. It's installed per browser (Tampermonkey desktop, Userscripts app on iOS, Firefox on Android).
- **Other:** install + how-it-works in the repo `README.md`

## Stack
Plain JavaScript userscript · no build · Node built-in test runner (`npm test`)

## Overview
Prototype built 2026-11-07. It runs on USTA draw pages in your own signed-in browser, parses the rendered bracket (rounds, times, venue, scores, winner, RET/Wd), remembers every draw you open for a tournament, and adds a floating **🎾 My matches** panel: next-match card, all your matches with results, a compact whole-draw list, and an `.ics` export. Verified live on the MAC Mixed Doubles Club Championship (6.0 main + consolation) at desktop and phone widths.

**Why a userscript:** USTA has no API for individuals. USTA Connect is a vetted partner program and doesn't cover draws. On PlayTennis, draws and schedules only show when signed in. Running in the user's own browser avoids handling anyone's USTA login. USTA's terms forbid scraping, so this stays a personal tool.

## Related
- [[tennisworkout]]: the other tennis project; a future "my matches" view could live on tennis.zebraproject.org.

## Worklog
_The code repo keeps a timestamped `WORKLOG.md` of agent activity at its root._
