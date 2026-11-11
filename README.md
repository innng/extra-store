# extra-store

A custom [Runtipi](https://runtipi.io) app store with apps that aren't in the
official catalogue, configured the way this server uses them.

Runtipi v4 scans each `apps/<id>/` directory directly (a root index is not
used). Every app ships a `config.json` validated against
`apps/app-info-schema.json`, a `docker-compose.yml`, and app metadata under
`metadata/` (a square `logo.jpg` plus a `description.md`).

## Apps

| App | What it is | Notes |
|-----|------------|-------|
| [`calibre-web`](apps/calibre-web) | Calibre-Web Automated (pinned `v4.0.8`) with built-in KOReader Kosync | Official-style split library layout; universal-calibre conversion |
| [`cloudflared`](apps/cloudflared) | Cloudflare Tunnel for ingress without open ports | Host networking; reaches Traefik on localhost |
| [`readeck`](apps/readeck) | Private bookmark manager and read-later service | Image pinned to a Readeck release |
| [`stremio-server`](apps/stremio-server) | Stremio streaming server for Web Stremio | Root redirects to the Web Stremio shell pointed back at this server |

## Installing this store

In Runtipi: **Settings → App Stores → Add Store**

- **Name:** `extra-store`
- **URL:** `https://github.com/innng/extra-store`
- **Branch:** `main`

Then **Apps → Browse → extra-store** and install what you need.

## App setup

### calibre-web

1. Expose the app and point its hostname at your domain.
2. Library uses Calibre-Web's separate-book-files mode:
   - `${ROOT_FOLDER_HOST}/media/data/calibre` → `/calibre-library` (metadata.db)
   - `${ROOT_FOLDER_HOST}/media/data/books` → `/books` (book files)
   - `${APP_DATA_DIR}/data/config` → `/config` (app data)
   - `${APP_DATA_DIR}/data/ingest` → `/cwa-book-ingest` (drop folder for auto-import)
3. KOReader OPDS: `https://<your-domain>/opds/`
4. KOReader Kosync: `https://<your-domain>/kosync` (GET/PUT API built into CWA)

### cloudflared

1. Create a tunnel in Cloudflare Zero Trust and copy its token.
2. Enter the token in the app's form field (`TUNNEL_TOKEN`, stored as a secret).
3. Route hostnames in the Cloudflare dashboard; the container runs with host
   networking so it reaches Traefik on localhost.

### readeck

1. Expose the app and point its hostname at your domain.
2. Data persists under `${APP_DATA_DIR}/data`.

### stremio-server

1. Expose the app and point its hostname at your domain.
2. Visiting the root serves a redirect to the official Web Stremio shell
   (`app.strem.io`) with `streamingServer` set to this instance.
3. A response header middleware sends
   `Access-Control-Allow-Private-Network: true` so browsers reaching the
   server over a private network (e.g. a tailnet) can play from it.

## Notes

- Runtipi injects its own Traefik routers for exposed apps, so apps that ship
  their own router labels end up with both (harmless duplicates).
- Logos must be `metadata/logo.jpg` — Runtipi v4 hardcodes that filename and
  ignores SVG.
- App data lives under Runtipi's `app-data/<store>/<app>/` and survives
  restarts and app updates — but **not uninstall** (uninstalling deletes the
  app's data directory; back up first).

## License

[MIT](LICENSE)
