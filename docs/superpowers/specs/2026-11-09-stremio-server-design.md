# Stremio Server Runtipi App Design

## Goal

Provide a Runtipi app that runs the official Stremio streaming server and
makes it available to LAN and Tailnet clients over both its assigned HTTP port
and the existing Traefik HTTPS application domain.

## Scope

- Add a server-only Stremio app to the `extra-store` repository.
- Follow the deployed `calibre-web` and `readeck` Runtipi/Traefik pattern.
- Persist Stremio server state in the app data directory.
- Use the current official, multi-architecture image tag `stremio/server:v4.22.5`.
- Route `${APP_DOMAIN}` through Traefik with the existing Cloudflare DNS ACME
  resolver (`myresolver`).

## Non-goals

- Self-hosting the Stremio Web UI.
- Adding or changing a Cloudflare Tunnel.
- Opening WAN firewall ports or adding a public-access authentication layer.
- Transcoding unsupported media formats; Stremio Server does not add browser
  codec support.

## App structure

Create `apps/stremio-server/` with the repository-standard files:

- `config.json`: Runtipi app metadata, `exposable: true`, fixed app port, media
  category, and amd64/arm64 support.
- `docker-compose.yml`: the service definition, persistent volume, Runtipi
  metadata, Traefik labels, and external `tipi_main_network` definition.
- `metadata/description.md` and `metadata/logo.svg`: catalog presentation files.

The service will expose container port `11470` through `${APP_PORT}` for LAN
and Tailnet diagnostics. Its persistent directory is
`${APP_DATA_DIR}/data`, mounted at `/root/.stremio-server`.

## Routing and access

The compose file will use the same source labels as `calibre-web` and
`readeck`, with distinct `stremio-server` service/router/middleware names:

1. The `web` router matches `Host(`${APP_DOMAIN}`)` and redirects to HTTPS.
2. The `websecure` router matches the same host, proxies to port `11470`, and
   uses `myresolver` for TLS.
3. The application remains accessible on its Runtipi-assigned host port over
   LAN and Tailscale.

Cloudflare DNS ACME can issue the certificate without inbound HTTP access. WAN
access remains a network-router/firewall decision outside this app; it must
stay blocked until public exposure is deliberately designed.

## Validation

Validation will check the app metadata and rendered Compose configuration,
then install the app in Runtipi and confirm that the generated compose file
contains the expected `11470` port, main-network attachment, and Traefik HTTPS
router. Runtime verification will check that the service is running, the
certificate is valid for `${APP_DOMAIN}`, and Web Stremio can be configured to
use that HTTPS endpoint.

## Compatibility note

This deployment only supplies the streaming server. Browser playback still
depends on source codecs: H.264/AVC with browser-supported audio is the
reliable option; HEVC, DTS, and some MKV sources can remain unsupported in
Web Stremio.
