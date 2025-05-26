---
title: "Firewall"
description: "A list of ports that need to be open on your firewall for Coolify to work properly."
---

# Firewall

## Self-hosted version

For self-hosting Coolify, you need to allow some ports on your firewall:

- **Coolify**:  
  `8000` (HTTP), `6001` (WebSocket), `6002` (Terminal), and `22` (SSH or custom port) — required for direct access  
- **Reverse Proxy**:  
  `80`, `443` — optional (can be blocked when using a Cloudflare Tunnel)

> **Tip:**  
> You can block ports `8000`, `6001`, and `6002` entirely when using Coolify through a domain with the built-in reverse proxy (Traefik or Caddy). These ports are only needed for internal container communication.

---

## 🔒 Blocking internal ports (`8000`, `6001`, `6002`)

To disable public access to internal services, override the port bindings in `/data/coolify/source/docker-compose.custom.yml`:

```yaml
services:
  coolify:  # blocks external access to port 8000
    ports: !reset []

  soketi:   # blocks external access to ports 6001 and 6002
    ports: !reset []
```
## Restricting Traefik to localhost

By default, Coolify's built-in Traefik proxy exposes ports `80`, `443`, and `8080` on all interfaces. If you're using a private ingress method like **Cloudflare Tunnel**, you can prevent public access by binding these ports to `localhost` only.

To do this, create or edit the following file:

**Path:** `/data/coolify/proxy/docker-compose.override.yml`

```
services:
  traefik:
    ports: !override
      - "127.0.0.1:80:80"        # HTTP
      - "127.0.0.1:443:443"      # HTTPS
      - "127.0.0.1:8080:8080"    # Traefik dashboard (optional)
      - "127.0.0.1:443:443/udp"  # Optional: enable HTTP/3 (UDP)
```
This configuration ensures that Traefik is only accessible from the local machine, blocking direct access from the public internet. External access must go through a tunnel (e.g. Cloudflared).
- [Cloudflared Guide](https://docs.github.com/en/authentication/keeping-your-account-and-data-secure/about-githubs-ip-addresses).

::: warning Caution
  If you are using `Oracle Cloud Free ARM Server`, you need to allow these ports
  inside Oracle's Dashboard, otherwise you cannot reach your instance from the
  internet after installation.
:::

### GitHub integration
- [Detailed Guide](https://docs.github.com/en/authentication/keeping-your-account-and-data-secure/about-githubs-ip-addresses).

#### Webhooks
You need to allow TCP port `80` or `443` for GitHub webhooks.

To specify the IP addresses (optional), you can use the following API endpoint to get them:

- https://api.github.com/meta - Check `hooks` section.

### Terminal

Since 4.0.0-beta.336, you need to allow TCP port `6002` for terminal access on `/terminal` endpoint.

::: success Tip
  If you are using the integrated reverse proxy (Traefik or Caddy), the terminal is accessible on `https://your-domain.com/terminal` with dynamic proxy configuration.
:::


## Cloud version

If you need the public facing IPs to allow inbound connections to your servers, here is an up-to-date list of IPs that you can use to whitelist:

- https://coolify.io/ipv4.txt
- https://coolify.io/ipv6.txt
