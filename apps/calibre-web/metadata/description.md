# Calibre-Web Automated

Calibre-Web Automated includes Calibre book management, conversion, and KOReader Kosync.

## Features

- **Web UI**: Browse, search, and download e-books
- **OPDS Support**: Native OPDS catalog for e-readers (KOReader, Kobo, etc.)
- **PDF → EPUB Conversion**: Built in
- **KOReader Kosync**: Built in at `/kosync`
- **Multi-user**: User management with roles and permissions
- **Anonymous OPDS**: Configurable public access without authentication

## Configuration

1. Set **External Hostname** (e.g., `books.yourdomain.com`)
2. Point Cloudflare Tunnel to Traefik (`http://your-tailscale-ip:80`)
3. Traefik routes via Host header to this app
4. CWA automatically creates and manages the Calibre library at `/calibre-library`.

## Volumes

| Volume | Purpose |
|--------|---------|
| `/config` | CWA configuration and application data |
| `/cwa-book-ingest` | Drop files here for automatic import; processed files are removed |
| `/calibre-library` | Managed Calibre library (metadata.db + book files) |

## KOReader Setup

- **OPDS Catalog**: `https://yourdomain.com/opds/`
- **Kosync**: `https://yourdomain.com/kosync`

## Notes

- On first run CWA creates a library if none exists.
- Put files in the ingest directory; CWA imports and catalogs them automatically.
