# AGENTS.md

## Project Overview
This is a static mirror of Cookie Clicker — pure HTML/JS/CSS with no backend, no build step, and no external dependencies or credentials.

## Running
- Served via `nginx:alpine` in `docker-compose.base44.yml`, binding the repo root to `/usr/share/nginx/html:ro`.
- Web entry point is on host port 3000.
- No build, no migrations, no seeds needed — just `docker compose -f docker-compose.base44.yml up -d`.

## Quirk: Repo root permissions
The sandbox repo root (`/app`) is created with `700` permissions. The nginx worker process runs as a non-root user and cannot traverse a `700` directory, causing a `403 Forbidden`. If that happens, run `chmod 755 /app` and restart the web service.

## Quirk: iframe frame-busting
The game has frame-busting code (`if (top!=self && !Game.local) Game.ErrorFrame()`) where `Game.ErrorFrame` is commented out, causing a crash when loaded in an iframe (which the preview is). The `LOCAL` flag in `index.html` was extended to also be true when `top!=self`, so the game treats the preview as a local mirror and skips frame-busting, ads, and tracking — consistent with how the game runs on localhost.

## Quirk: heralds
The herald count (purple flag in the top bar, +1% CpS each) is live data from `https://orteil.dashnet.org/data/cookieclickersteam.json`; that host is Cloudflare-protected and not CORS-accessible, so a local mirror can never load it. When `Game.local` is set, `Game.UpdateHeralds` now reads the bundled `data/cookieclickersteam.json` instead, and `getJson` only blocks external (http) URLs. The file mirrors the live source's format (Steam concurrent players; snapshot 2026-10-09 gives the capped 100 heralds) — edit it to change the count.

## Editing
All source is static files served directly — edits appear immediately on browser refresh (no live-reload dev server). Call `reload_preview` after changes so the user sees them.
