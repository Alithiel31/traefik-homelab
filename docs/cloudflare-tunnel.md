# Cloudflare Tunnel

[Version française](cloudflare-tunnel.fr.md)

Traffic path: `Internet → Cloudflare (TLS) → cloudflared → Traefik :8000 → container`.

## Tunnel configuration

In the Cloudflare Zero Trust dashboard (Networks → Tunnels → your tunnel → Public hostname), add one hostname per service and point it at Traefik:

| Field     | Value                                                             |
| --------- | ----------------------------------------------------------------- |
| Hostname  | `whoami.example.com`                                              |
| Service   | `HTTP` → `localhost:8000` (or `traefik:8000` if `cloudflared` is on `traefik-net`) |

The hostname must match the `Host(...)` rule of the container's router.

## Finding the right `trustedIPs`

`forwardedHeaders.trustedIPs` must contain the address Traefik sees as the *source* of the tunnel's requests. Only requests from that address have their `X-Forwarded-For` header trusted.

- If `cloudflared` runs on the host, this is usually the `traefik-net` gateway: `docker network inspect traefik-net --format '{{ (index .IPAM.Config 0).Gateway }}'`.
- If `cloudflared` runs in a container on `traefik-net`, use that container's IP (or give the network a fixed subnet).

Update the value in `docker-compose.yml` (currently `192.168.64.1/32`) and run `docker compose up -d`. Too broad a range lets clients spoof their IP.
