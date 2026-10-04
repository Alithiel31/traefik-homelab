# Troubleshooting

[Version française](troubleshooting.fr.md)

| Symptom | Likely cause | Fix |
| ------- | ------------ | --- |
| `404 page not found` | No router matches the request | Check the `Host(...)` rule against the requested hostname, and that the container has `traefik.enable=true`. |
| Container missing from the dashboard/logs | Not on `traefik-net`, or `traefik.enable` missing | Add the network and label, then `docker compose up -d` on that service. |
| `502 Bad Gateway` | Wrong backend port or app not started | Set `traefik.http.services.<name>.loadbalancer.server.port` to the container's internal port; check `docker logs <container>`. |
| `network traefik-net declared as external, but could not be found` | Network not created | `docker network create traefik-net` |
| Client IP shows the tunnel's IP | `trustedIPs` doesn't match the tunnel's source address | See [cloudflare-tunnel.md](cloudflare-tunnel.md). |
| Two services collide | Same router name in two compose files | Use a unique router name per service. |

Useful commands:

```bash
docker logs -f traefik                          # Traefik logs (provider and routing errors)
docker network inspect traefik-net              # which containers are attached
docker inspect <container> --format '{{json .Config.Labels}}'   # labels as Traefik sees them
```
