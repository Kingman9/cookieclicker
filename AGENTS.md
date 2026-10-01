# Cookie Clicker (static mirror)

## Overview
Static HTML/JS/CSS game (Cookie Clicker mirror). No build step, no package manager, no backend. All files are served directly.

## Running
- `docker compose -f docker-compose.base44.yml up -d` serves the site on port 3000 via nginx.
- No dependencies to install; no migrations or seeds needed.

## Key files
- `index.html` — entry point, loads `main.js`, `style.css`, `base64.js`, etc.
- `main.js` — the entire game (ES5, ~976KB, single file). Very large; avoid reading it in full.
- `img/`, `snd/`, `loc/`, `cf-fonts/` — static assets.

## Notes
- The game is pure client-side JS; "failed to start" in this context means nothing served on port 3000, not a build error.
- Commit 202bed5 injected a Cookie Monster mod script (`https://cookiemonsterteam.github.io/CookieMonster/dist/CookieMonster.js`) into `Game.Init`. This is an external dependency that loads at runtime; if it fails to load (network/CORS) the game still runs but the mod won't activate.
