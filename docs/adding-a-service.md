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

## Internal-only service (no Cloudflare)

For a service that must stay private (reached over the VPN only), use the same labels but skip the Cloudflare Tunnel step:

1. Pick an internal hostname, e.g. `myservice.homelab.internal`, and use it in the `Host(...)` rule.
2. On each client machine, add it to the `hosts` file, pointing to the homelab's VPN address:
   ```
   <vpn-address-of-the-homelab>  myservice.homelab.internal
   ```
3. Browse to `http://myservice.homelab.internal:8000/`.

If the container must reach services on the host (database, Traefik itself…) from a custom Docker network, the host firewall needs a rule for that network's subnet.
