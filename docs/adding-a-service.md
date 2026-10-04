# Adding a service

[Version française](adding-a-service.fr.md)

Traefik only routes containers that opt in (`exposedbydefault=false`). A service needs three things: the `traefik-net` network, `traefik.enable=true`, and a router rule.

```yaml
services:
  whoami:
    image: traefik/whoami
    restart: unless-stopped
    networks:
      - traefik-net
    labels:
      - "traefik.enable=true"
      - "traefik.http.routers.whoami.rule=Host(`whoami.example.com`)"
      - "traefik.http.routers.whoami.entrypoints=web"
      - "traefik.http.services.whoami.loadbalancer.server.port=80"

networks:
  traefik-net:
    external: true
```

Notes:

- The router name (`whoami`) must be unique across all your containers.
- `entrypoints=web` is the only entrypoint (port `8000`). TLS is handled by Cloudflare, so don't add a `tls` option here.
- `loadbalancer.server.port` is the port the application listens on *inside* the container. It is required if the image exposes several ports or none.
- Add a public hostname for the same domain in the Cloudflare Tunnel (see [cloudflare-tunnel.md](cloudflare-tunnel.md)).
