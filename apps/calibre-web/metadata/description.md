# Calibre-Web Automated

Calibre-Web Automated (CWA) wraps Calibre-Web with automatic book management
(ingest, metadata enforcement, EPUB fixing) plus PDF → EPUB conversion and
built-in KOReader Kosync.

## Features

- **Web UI**: Browse, search, and read e-books
- **OPDS Support**: Native OPDS catalog for e-readers (KOReader, Kobo, etc.)
- **Auto-ingest**: Drop files into the ingest folder and CWA imports them
- **Conversion**: Built-in Calibre PDF → EPUB conversion
- **KOReader Kosync**: Built-in at `/kosync`
- **Multi-user**: User management with roles and permissions

## Volumes

| Volume | Purpose |
|--------|---------|
| `/config` | CWA configuration and application data |
| `/cwa-book-ingest` | Drop files here for automatic import; processed files are removed |
| `/calibre-library` | Calibre database (`metadata.db` + `.calnotes`) — `media/data/calibre` |
| `/books` | Book files (separate book files mode) — `media/data/books` |

## Library layout

This app uses Calibre-Web's **separate book files** mode:

- `metadata.db` lives in `media/data/calibre` and is mounted at `/calibre-library`.
- Book files (`.epub`, covers, `metadata.opf`) live in `media/data/books` and are
  mounted at `/books`.

The `Calibre Library Location` in CWA settings must be `/calibre-library`, and
`Separate Book Files from Library` must point at `/books`.

## Networking

Do **not** configure an external hostname in the app. Enable
**Expose app on the internet** in Runtipi and set the subdomain (e.g. `books`),
which routes `${APP_DOMAIN}` through Traefik.

## KOReader Setup

- **OPDS Catalog**: `https://yourdomain.com/opds/`
- **Kosync**: `https://yourdomain.com/kosync` (enable KOReader Sync in CWA Settings)
