# Calibre-Web + Kosync

Calibre-Web is a web interface for browsing, reading, and downloading e-books from a Calibre library. This version includes **Kosync** for seamless KOReader synchronization.

## Features

- **Web UI**: Browse, search, and download books from any browser
- **OPDS Support**: Native OPDS catalog for e-readers (KOReader, Kobo, etc.)
- **PDF → EPUB Conversion**: Automatic conversion via universal-calibre mod
- **KOReader Sync (Kosync)**: Sync reading progress, annotations, highlights, and metadata
- **Multi-user**: User management with roles and permissions
- **Anonymous Access**: Configurable public OPDS without authentication

## Configuration

1. Set **External Hostname** (e.g., `books.yourdomain.com`)
2. Point Cloudflare Tunnel to Traefik (`http://your-tailscale-ip:80`)
3. Traefik routes via Host header to this app
4. Configure Calibre library path: `/books`

## KOReader Setup

### OPDS Catalog
- URL: `https://yourdomain.com/opds/`
- No auth needed (uses calibre-web anonymous access)

### Kosync (Metadata + Progress Sync)
- URL: `https://yourdomain.com:9090` (if exposed via tunnel)
- Or via Tailscale: `http://your-tailscale-ip:9090`
- Enable in KOReader: **Settings → Synchronization → Kosync**

## Volumes

| Volume | Purpose |
|--------|---------|
| `/books` | Calibre library (metadata.db + book files) |
| `/config` | Calibre-Web config (app.db) + Kosync DB |
| `/app/calibre` | Persistent calibre binaries (ebook-convert) |

## Notes

- Requires Calibre library with `metadata.db` in `/books`
- First run: Calibre-Web will prompt for library location
- Kosync mod enables `/kosync` API endpoints
- Anonymous OPDS: Set `config_anonbrowse=1` in Calibre-Web admin