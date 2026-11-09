# Stremio Server Runtipi App Implementation Plan

> **For agentic workers:** REQUIRED SUB-SKILL: Use superpowers:subagent-driven-development (recommended) or superpowers:executing-plans to implement this plan task-by-task. Steps use checkbox (`- [ ]`) syntax for tracking.

**Goal:** Add a server-only Stremio streaming-server app to `extra-store`, exposed through the existing Runtipi/Traefik HTTPS pattern.

**Architecture:** The app follows the deployed Calibre-Web and Readeck structure: Runtipi assigns and publishes the application port, the container joins `tipi_main_network`, and Traefik labels redirect HTTP to HTTPS and proxy `${APP_DOMAIN}` to port 11470. State is retained below `${APP_DATA_DIR}` and the server image is version-pinned.

**Tech Stack:** Runtipi app store metadata, Docker Compose, Traefik v3 labels, `stremio/server:v4.22.5`.

**Spec:** `docs/superpowers/specs/2026-11-09-stremio-server-design.md`

## Global Constraints

- Use `stremio/server:v4.22.5`; do not use a floating `latest` tag.
- Mount `${APP_DATA_DIR}/data` at `/root/.stremio-server`.
- Use container port `11470` for both `${APP_PORT}` and the Traefik service.
- Follow the source-label pattern in `apps/calibre-web/docker-compose.yml`.
- Do not change Cloudflare Tunnel, Runtipi core, Traefik core, router, or firewall configuration.
- Support both `amd64` and `arm64`.

## Review Focus

- Image tag changes: validate the pinned image remains multi-architecture before upgrades.
- Data persistence: confirm server state survives a container recreation.
- Routing: confirm HTTPS requests for `${APP_DOMAIN}` reach the Stremio container rather than a Traefik 404.
- Network boundary: confirm WAN port forwarding remains disabled before adding a public Tunnel route.
- Codec limitation: distinguish a browser-decoder error from a streaming-server reachability failure.

---

### Task 1: Add the Stremio Server app definition

**Files:**
- Create: `apps/stremio-server/config.json`
- Create: `apps/stremio-server/docker-compose.yml`
- Create: `apps/stremio-server/metadata/description.md`
- Create: `apps/stremio-server/metadata/logo.svg`
- Test: temporary Compose/metadata validation command

**Interfaces:**
- Consumes: Runtipi variables `${APP_PORT}`, `${APP_DATA_DIR}`, `${APP_DOMAIN}` and the external `tipi_main_network`.
- Produces: an installable `stremio-server` app, proxyable by existing Traefik configuration.

- [ ] **Step 1: Write the failing configuration validation**

Create a temporary validation that asserts the app directory and metadata files
exist, checks `config.json` is valid JSON with `id: stremio-server`, and checks
the Compose source contains `stremio/server:v4.22.5`, the `11470` mappings,
the persistent mount, the main network, and the HTTPS router labels.

- [ ] **Step 2: Run the validation to verify it fails**

Run the validation before app files exist.

Expected: failure because `apps/stremio-server/config.json` is missing.

- [ ] **Step 3: Add the Runtipi metadata and Compose definition**

Model the files on `apps/calibre-web`, renaming all Traefik resources to
`stremio-server`, replacing its internal port with `11470`, and mounting the
server state directory. Set `exposable` to `true`, `dynamic_config` to `true`,
and include the standard `x-runtipi` main-service declaration.

- [ ] **Step 4: Run the metadata and Compose validation**

Run the temporary validation and render the Compose file with values for
`APP_PORT`, `APP_DATA_DIR`, and `APP_DOMAIN`.

Expected: both pass; the rendered service exposes port 11470, carries the
HTTPS router labels, and retains the data mount.

- [ ] **Step 5: Commit**

```bash
git add apps/stremio-server docs/superpowers
git commit -m "feat: add Stremio Server Runtipi app"
```

### Task 2: Install and verify the app in Runtipi

**Files:**
- Modify: Runtipi runtime app state only through the Runtipi UI/API
- Test: generated Compose and runtime service checks

**Interfaces:**
- Consumes: the extra-store app definition from Task 1 and existing Traefik
  `myresolver`.
- Produces: a running service accessible using its Runtipi port and
  `https://${APP_DOMAIN}`.

- [ ] **Step 1: Install `stremio-server` from `extra-store` in Runtipi**

Use the Runtipi UI/API rather than manually editing generated files.

- [ ] **Step 2: Verify generated configuration and container state**

Inspect Runtipi's generated compose configuration and container logs.

Expected: service is running, maps the assigned app port to 11470, and is on
`runtipi_tipi_main_network`.

- [ ] **Step 3: Verify HTTPS routing from a LAN or Tailnet client**

Request `https://${APP_DOMAIN}` and inspect Traefik/service logs.

Expected: a valid certificate and a non-Traefik response from Stremio Server.

- [ ] **Step 4: Configure and verify Web Stremio**

Set the streaming-server endpoint in Web Stremio to
`https://${APP_DOMAIN}` and try a known browser-compatible H.264/AAC stream.

Expected: the stream reaches the server; codec-specific browser errors are
reported separately from connection/routing errors.
