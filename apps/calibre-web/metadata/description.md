# Calibre-Web Automated

Calibre-Web with **universal-calibre** mod for automatic PDF→EPUB conversion.

## Features

- **Web UI**: Browse, search, and download e-books
- **OPDS Support**: Native OPDS catalog for e-readers (KOReader, Kobo, etc.)
- **PDF → EPUB Conversion**: Automatic via universal-calibre mod
- **Multi-user**: User management with roles and permissions
- **Anonymous OPDS**: Configurable public access without authentication

## Configuration

1. Set **External Hostname** (e.g., `books.yourdomain.com`)
2. Point Cloudflare Tunnel to Traefik (`http://your-tailscale-ip:80`)
3. Traefik routes via Host header to this app
4. Configure Calibre library path: `/books`

## Volumes

| Volume | Purpose |
|--------|---------|
| `/books` | Calibre library (metadata.db + book files) |
| `/config` | Calibre-Web config (app.db, settings) |
| `/app/calibre` | Persistent calibre binaries (ebook-convert) |

## KOReader Setup

- **OPDS Catalog**: `https://yourdomain.com/opds/`
- No auth needed (configure `config_anonbrowse=1` in Calibre-Web admin)

## Notes

- Requires Calibre library with `metadata.db` in `/books`
- First run: Calibre-Web will prompt for library location
- Conversion: Place PDFs in library, use Calibre-Web UI → Convert → EPUB