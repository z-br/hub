# USTA Plus — Notes

> Dated journal: decisions, progress, dead-ends. Newest on top.

## 2026-11-07
- Started tracking in the hub; built the prototype the same day.
- **Data access findings:** no official route for individuals. USTA Connect (ustaconnect@usta.com) is a vetted partner program covering account SSO, WTN/NTRP ratings, Safe Play status and facilities, not draws. The WTN GraphQL API docs are public at docs.worldtennisnumber.com, but credentials are partner-only. PlayTennis is ClubSpark-hosted. Tournament overview and entry lists are public. Draws and Schedule tabs show "Sign in to view".
- **Approach:** read the rendered draw DOM in the user's own signed-in browser. Tried intercepting the page's own data requests instead, but the draw data didn't come through a wrapped `fetch` after page load, and the site blocks being framed. DOM parsing on element structure (not hashed class names) was reliable.
- **Gotcha:** on phones USTA only renders two bracket columns at a time, so scans are merged per draw+stage keyed by round+slot.
- **Gotcha:** the Schedule tab only lists completed matches; upcoming times come from the draws.
- **Options considered for a hosted/mobile web app:** a PWA on our own domain can't read USTA pages (cross-site), so a hosted version would need users' USTA logins, which we're not doing. If wanted later: a bookmarklet/userscript "send to app" handoff through the URL `#` fragment into a local-only PWA.
