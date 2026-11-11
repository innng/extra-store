# Readeck

Private bookmark manager and read-later service. Readeck archives article
content locally, so bookmarks stay readable even if the original page
disappears.

## Setup

1. Install from this store and toggle **Expose app**, then set the app's
   hostname (e.g. `readeck.example.com`) in the app's config panel.
2. Data persists under `app-data/extra-store/readeck/data`.
3. On first visit, create your admin account at `/login`.

## Notes

- The image tag is pinned to a Readeck release (`0.23.5`); bump `version` in
  `config.json` and the compose file together when updating.
- Readeck serves everything on port 8000; Runtipi's Traefik integration
  handles TLS routing via the `Host` rule.
