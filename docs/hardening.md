# Self-hosted infra hardening (Coolify · Mac mini · Colima)

> Security posture + checklist for the home-hosted Coolify box that runs all the
> Coolify projects ([[roselight]], [[cookthebooks]], [[homepage]],
> [[wallacecourts]], [[famfam]]). See [[deployment]] for the deploy mechanics.
> **Updated:** 2026-06-21.

## The setup
- **Host:** Mac mini running **Colima** (a Linux VM) for Docker; Coolify orchestrates everything. Currently on home **WiFi** (`192.168.50.138`); plan is to move it behind a **MikroTik hEX (E50UG)** for network isolation.
- **Ingress:** Cloudflare **Tunnel** (`958ef22e-5c9f-45ca-952a-ccfd2098cbd5`) → Coolify Traefik on `localhost:80`. The tunnel is **outbound-only** — there are **no inbound ports forwarded** from the internet.
- **Coolify dashboard:** `https://ccc.zebraproject.org` (also via the tunnel).
- **Database:** one shared Postgres (`tkinv01f2fpidwklct4n9a8o`), **internal-only** (no published port), schema-per-project. Daily 07:05 UTC → S3 backup.

## Current posture

**Good (better baseline than most home hosting):**
- Tunnel is outbound-only → internet **cannot scan/reach** SSH, Docker, or Postgres. Eliminates the #1 home-hosting risk (no port-forwarding).
- Postgres has no public port.
- Colima adds a **VM boundary** — a container breakout lands in the Linux VM, not directly on macOS.

**Residual risks, ranked:**
1. ~~Coolify dashboard wide open to the internet~~ → **mitigated** (Cloudflare Access, below).
2. **Flat home network** — the mini sits on WiFi next to laptops/phones/IoT. A container→host breakout could pivot to home devices (and vice-versa). → mitigate with the **hEX segmentation** below.
3. **Multi-tenant shared Postgres** — needs per-project role/schema isolation so one app can't read another's data.
4. **Single secret blast radius** — every project's secrets live in Coolify; several were pasted into chat sessions during the DO→Coolify migration (rotate them).

## Done — Cloudflare Access on the Coolify dashboard (2026-06-21)

The dashboard is gated; webhooks + API are bypassed so deploys and automation keep working. Cloudflare matches the **most-specific path first**, so the `/webhooks` and `/api` apps override the broad one.

| Access application | Destination | Policy | Why |
|---|---|---|---|
| `ccc` (GP Only) | `ccc.zebraproject.org` | **Allow** `gregp777@gmail.com` | gates the dashboard UI to one email |
| `ccc` (webhooks) | `ccc.zebraproject.org/webhooks/*` | **Bypass** | GitHub auto-deploy webhook (`/webhooks/source/github/events`) — still auth'd by GitHub's signing secret |
| `ccc` (api) | `ccc.zebraproject.org/api/*` | **Bypass** | Coolify API — still auth'd by the Coolify bearer token |

Verified: dashboard `/` → 302 to `cloudflareaccess.com`; `POST /webhooks/source/github/events` → 200; `/api/v1/version` → 401 without token / 200 with token.

- **MFA:** currently Cloudflare **One-time PIN** (email code). For a true second factor, add **Google** as an identity provider (Zero Trust → Settings → Authentication) so login = your Google account + its 2FA; then a policy can require MFA.
- **Tip:** `/webhooks/horses`-style 404s appear to show the Access login — that's a redirect chain (Coolify 302s unknown routes to `/login`, which *is* gated). The bypass itself is fine; test with the real endpoint or `curl -I` (look for `cloudflareaccess.com` vs `ccc.zebraproject.org/login` in `location`).

## To do — prioritized

1. **Network segmentation via the MikroTik hEX.** Put the mini on its own subnet behind the hEX:
   - hEX WAN → home router; hEX LAN → mini (e.g. `192.168.88.10`, DHCP reservation).
   - Firewall: **allow `mini → internet`** (all the tunnel/updates/S3/image-pulls need), **DROP `mini → 192.168.0.0/16 · 10.0.0.0/8 · 172.16.0.0/12`** (home LAN + other RFC1918). Now a compromised container can't pivot into the house.
2. **Tailscale on the mini** for admin/API access. Decouples *reachability* from *topology*: keep the hEX rules strict (no inbound, no LAN), and still reach SSH + Coolify from anywhere over the tailnet. Lets you eventually move the Coolify UI/API **off the public hostname entirely** (Tailscale-only), leaving only app + webhook traffic public.
3. **Rotate secrets exposed during the migration:** Resend API key, `DIGEST_TOKEN_SECRET`, `SESSION_SECRET`, the Coolify API token, the Cloudflare API token, the healthchecks.io ping URL, and the mini SSH key. (The DigitalOcean DB creds are dead — skip.)
4. **Confirm no app container mounts `/var/run/docker.sock`** — that's a root-equivalent host breakout. Coolify's own containers need it; *app* containers must not.
5. **Audit Postgres tenancy** — each project's login role scoped to its own schema (search_path locked, no grants on `public`/other schemas). The `pg-provision` flow does this; verify once.
6. **Host basics on the mini** — FileVault on, macOS firewall on, SSH **key-only** (`PasswordAuthentication no`), keep macOS / Colima / Docker / Coolify / base images patched (rebuild periodically for image CVEs).
7. **Backups offsite + tested** — shared-PG daily→S3 is good; keep an independent copy and do a test restore. (e.g. `do-prod-defaultdb-*.dump` from the migration is local-only on `~/roselight-backups`.)
8. **Edge protection** on public apps — Cloudflare WAF managed rules + rate limiting; consider Access on app admin routes (e.g. roselight `/editor`).

## Operational notes
- **Coolify API automation** (`coolify.sh`, agent helpers) works because `/api/*` is **Bypass** (Coolify bearer token still required). If you ever tighten that bypass to a stricter policy, automation must send a Cloudflare **Service Token** (`CF-Access-Client-Id` / `CF-Access-Client-Secret`) or run over **Tailscale**.
- **GitHub auto-deploy** posts to `/webhooks/source/github/events` (in the `handsome-hummingbird` GitHub App config). Keep `/webhooks/*` bypassed or deploys stop.
- **Host access** to the internal DB / containers is LAN-only today (`ssh -i ~/.ssh/id_ed25519 cosmo@192.168.50.138`, then `/opt/homebrew/bin/docker` with the Colima socket). After the hEX move, use **Tailscale** instead.
