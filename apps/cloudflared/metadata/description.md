# Cloudflare Tunnel

Cloudflare Tunnel (cloudflared) creates a secure, encrypted tunnel between your local services and Cloudflare's global network, allowing you to expose services without opening ports on your firewall.

## Features

- **Zero Trust Security**: All traffic passes through Cloudflare's network with DDoS protection, WAF, and access policies
- **No Port Forwarding**: Works behind CGNAT, firewalls, and restrictive networks
- **Global Performance**: Traffic routed through Cloudflare's 300+ data centers
- **Identity-Aware**: Integrate with Cloudflare Access for authentication/authorization

## Configuration

1. Create a tunnel in **Cloudflare Zero Trust → Tunnels**
2. Copy the **Tunnel Token**
3. Enter the token in the app configuration
4. Add public hostnames in Cloudflare dashboard pointing to your Traefik IP:port

## Use Cases

- Expose home server services (dashboards, apps) securely
- Replace VPN for remote access
- Share local development servers temporarily
- Secure IoT device access

## Notes

- Uses host networking to reach Traefik on localhost
- Tunnel token is stored securely in app config
- Automatically reconnects on network changes