# AGENTS.md

## Project Overview
This is a static mirror of Cookie Clicker — pure HTML/JS/CSS with no backend, no build step, and no external dependencies or credentials.

## Running
- Served via `nginx:alpine` in `docker-compose.base44.yml`, binding the repo root to `/usr/share/nginx/html:ro`.
- Web entry point is on host port 3000.
- No build, no migrations, no seeds needed — just `docker compose -f docker-compose.base44.yml up -d`.

## Quirk: Repo root permissions
The sandbox repo root (`/app`) is created with `700` permissions. The nginx worker process runs as a non-root user and cannot traverse a `700` directory, causing a `403 Forbidden`. If that happens, run `chmod 755 /app` and restart the web service.

## Quirk: `LOCAL` / `Game.local` off the official domain
`LOCAL` in `index.html` is true whenever the game is **not** served from `orteil.dashnet.org` (previously it was true only on localhost or inside an iframe). This mirror is never served from the official domain, so it always runs in local mode. That matters because:
- The game has frame-busting code (`if (top!=self && !Game.local) Game.ErrorFrame()`) where `Game.ErrorFrame` is commented out, which crashes the page when loaded in an iframe (the preview). Local mode skips it, plus ads and tracking.
- Several data files (heralds, grandmas, info) are fetched from `https://orteil.dashnet.org/data/...`, which is Cloudflare-protected and CORS-blocked off-site. Local mode loads the bundled copies instead.
- If `Game.local` is ever false here (e.g. a top-level tab on the preview host), the game tries the blocked official URLs and those features silently show "?" / "couldn't be loaded" even though the page itself looks fine. Check `Game.local` first when a data-driven feature breaks.

## Quirk: heralds
The herald count (purple flag in the top bar, +1% CpS each) is live Steam-player data. In local mode `Game.UpdateHeralds` reads the bundled `data/cookieclickersteam.json`, and `getJson` only blocks external (http) URLs so bundled relative paths still load. The file mirrors the live source's format (Steam concurrent players; snapshot 2026-10-09 gives the capped 100 heralds) — edit it to change the count.

## Editing
All source is static files served directly — edits appear immediately on browser refresh (no live-reload dev server). Call `reload_preview` after changes so the user sees them.
