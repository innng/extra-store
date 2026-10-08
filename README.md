# extra-store - Custom Runtipi App Store

Custom applications for home server setups.

## Apps

### cloudflared
Cloudflare Tunnel for secure ingress without opening ports.
- Uses host networking to reach Traefik on localhost
- Configured via `TUNNEL_TOKEN` from Cloudflare Zero Trust

### calibre-web
The official Calibre-Web layout with conversion and Kosync mods added.
- Includes universal-calibre mod for PDF→EPUB conversion
- Includes kosync-mod for KOReader sync API on port 9090
- Persistent calibre binaries volume

## Adding to Runtipi

1. Push this repo to GitHub (e.g., `github.com/youruser/extra-store`)
2. In Runtipi: **Settings → App Stores → Add Store**
   - Name: `extra-store`
   - URL: `https://github.com/youruser/extra-store`
   - Branch: `main`
3. **Apps → Browse → extra-store** → Install apps

## Configuration

### cloudflared
1. Get tunnel token: Cloudflare Zero Trust → Tunnels → your tunnel → "Token"
2. Enter token in app config
3. Tunnel will connect and route hostnames per Cloudflare dashboard

### calibre-web
1. Configure the app hostname as `books.yourdomain.com`.
2. Create a second tunnel hostname, such as `sync.yourdomain.com`, pointing to Traefik on port 80.
3. KOReader OPDS: `https://books.yourdomain.com/opds/`
4. KOReader Kosync: `https://sync.yourdomain.com/`

## Volumes (persisted outside install dir)

```
/path/to/runtipi/app-data/
├── cloudflared/
├── media/data/calibre/  # Calibre database (metadata.db)
├── media/data/books/    # separate ebook files
```

## Traefik Labels (auto-added by Runtipi)

Calibre-Web and Kosync use separate hostnames; neither needs a path-specific override or a published Kosync port.

## KOReader Setup

### OPDS Catalog
- URL: `https://books.yourdomain.com/opds/`
- No auth (uses calibre-web anonymous access)

### Kosync (metadata sync)
- URL: `https://sync.yourdomain.com/`
- Requires calibre-web-kosync with kosync-mod enabled
