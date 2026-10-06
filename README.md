# extra-store - Custom Runtipi App Store

Custom applications for home server setups.

## Apps

### cloudflared
Cloudflare Tunnel for secure ingress without opening ports.
- Uses host networking to reach Traefik on localhost
- Configured via `TUNNEL_TOKEN` from Cloudflare Zero Trust

### calibre-web-kosync
Calibre-Web with Kosync for KOReader OPDS + metadata sync.
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

### calibre-web-kosync
1. Set `APP_HOST` to your public hostname (e.g., `books.yourdomain.com`)
2. Point Cloudflare Tunnel hostname to Traefik (`http://your-tailscale-ip:80`)
3. Traefik routes to this app via Host header
4. KOReader OPDS: `https://books.yourdomain.com/opds/`
5. KOReader Kosync: `https://books.yourdomain.com:9090` (if exposed) or via Tailscale

## Volumes (persisted outside install dir)

```
/path/to/runtipi/app-data/
├── cloudflared/
├── calibre-web-kosync/
│   ├── books/           # Calibre library
│   ├── config/          # app.db, kosync.db
│   └── calibre-binaries/ # ebook-convert, etc.
```

## Traefik Labels (auto-added by Runtipi)

Runtipi manages Traefik labels via `APP_HOST`/`APP_PORT`. For custom routing (e.g., `/opds` only), add user-config override:

```yaml
# ~/path/to/runtipi/user-config/extra-store/calibre-web-kosync/docker-compose.yml
services:
  calibre-web-kosync:
    labels:
      traefik.http.routers.calibre-opds.rule: Host(`books.yourdomain.com`) && PathPrefix(`/opds`)
      traefik.http.routers.calibre-opds.entrypoints: web
      traefik.http.routers.calibre-opds.service: calibre-web-kosync
      traefik.http.routers.calibre-opds.priority: 200
```

## KOReader Setup

### OPDS Catalog
- URL: `https://books.yourdomain.com/opds/`
- No auth (uses calibre-web anonymous access)

### Kosync (metadata sync)
- URL: `https://books.yourdomain.com:9090` (if exposed via tunnel)
- Or via Tailscale: `http://your-tailscale-ip:9090`
- Requires calibre-web-kosync with kosync-mod enabled