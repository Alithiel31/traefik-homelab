# traefik-homelab

[Version française](README.fr.md)

Traefik v3.6 reverse proxy for a Raspberry Pi 5 homelab. It routes internal services by Docker labels and sits behind a Cloudflare Tunnel (`cloudflared`), so no port is exposed directly to the internet.

## How it works

- Entrypoint `web` on port `8000` (HTTP only; TLS is terminated by Cloudflare).
- Docker provider with `exposedbydefault=false`: a container is routed only if it has `traefik.enable=true` and its own router labels.
- Routed containers must join the external `traefik-net` network.
- `forwardedHeaders.trustedIPs` is limited to the tunnel's gateway IP so client IPs can't be spoofed.

## Usage

```bash
docker network create traefik-net   # once
docker compose up -d
```

## Security notes

- The Docker socket is mounted read-only (`:ro`). This limits writes but still exposes container metadata; a socket proxy would be stricter.
- Adjust `trustedIPs` to your own tunnel/gateway address.
