# Cookie Clicker (static mirror)

## Overview
Static HTML/JS/CSS game (Cookie Clicker mirror). No build step, no package manager, no backend. All files are served directly.

## Running
- `docker compose -f docker-compose.base44.yml up -d` serves the site on port 3000 via Python HTTP server.
- No dependencies to install; no migrations or seeds needed.

## Key files
- `index.html` — entry point, loads `env.js`, `main.js`, `style.css`, `base64.js`, etc.
- `main.js` — the entire game (ES5, ~976KB, single file). Very large; avoid reading it in full.
- `img/`, `snd/`, `loc/`, `cf-fonts/` — static assets.

## Sandbox overrides
- `env.js` is generated at container startup by the compose command, writing `window.BASE44_PREVIEW_MODE` from the environment. When `BASE44_PREVIEW_MODE` is `"1"`, `index.html` sets `LOCAL=true`, which prevents the game from making CORS-blocked AJAX calls to `orteil.dashnet.org` (heralds, grandmas, news data). When the flag is absent, original behavior is preserved.
- The volume mount is writable (not `:ro`) so the startup command can create `env.js` in the served directory.

## Notes
- The game is pure client-side JS; "failed to start" in this context means nothing served on port 3000, not a build error.
- Cookie Monster mod (v2.053.10, from dev branch) is loaded locally via `CookieMonster.js`. It's loaded AFTER game init (not as PRESETMOD) to avoid 2.053/2.058 incompatibility — loading as PRESETMOD breaks building rendering. The post-init loader script in `index.html` polls for `Game.ready === 1` then calls `Game.LoadMod('CookieMonster.js')`.
