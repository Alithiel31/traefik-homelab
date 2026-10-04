# Dépannage

[English version](troubleshooting.md)

| Symptôme | Cause probable | Solution |
| -------- | -------------- | -------- |
| `404 page not found` | Aucun routeur ne correspond à la requête | Vérifie la règle `Host(...)` par rapport au nom d'hôte demandé, et que le conteneur a `traefik.enable=true`. |
| Conteneur absent des logs | Pas sur `traefik-net`, ou `traefik.enable` manquant | Ajoute le réseau et le label, puis `docker compose up -d` sur ce service. |
| `502 Bad Gateway` | Mauvais port ou application non démarrée | Définis `traefik.http.services.<nom>.loadbalancer.server.port` sur le port interne du conteneur ; consulte `docker logs <conteneur>`. |
| `network traefik-net declared as external, but could not be found` | Réseau non créé | `docker network create traefik-net` |
| L'IP client est celle du tunnel | `trustedIPs` ne correspond pas à l'adresse source du tunnel | Voir [cloudflare-tunnel.fr.md](cloudflare-tunnel.fr.md). |
| Deux services entrent en conflit | Même nom de routeur dans deux fichiers compose | Utilise un nom de routeur unique par service. |

Commandes utiles :

```bash
docker logs -f traefik                          # logs Traefik (erreurs de provider et de routage)
docker network inspect traefik-net              # conteneurs attachés
docker inspect <conteneur> --format '{{json .Config.Labels}}'   # labels tels que Traefik les voit
```
