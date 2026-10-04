# Ajouter un service

[English version](adding-a-service.md)

Traefik ne route que les conteneurs qui le demandent (`exposedbydefault=false`). Un service a besoin de trois choses : le réseau `traefik-net`, `traefik.enable=true` et une règle de routeur.

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

Notes :

- Le nom du routeur (`whoami`) doit être unique parmi tous tes conteneurs.
- `entrypoints=web` est le seul entrypoint (port `8000`). Le TLS est géré par Cloudflare : n'ajoute pas d'option `tls` ici.
- `loadbalancer.server.port` est le port sur lequel l'application écoute *dans* le conteneur. Il est obligatoire si l'image expose plusieurs ports ou aucun.
- Ajoute un nom d'hôte public pour le même domaine dans le Cloudflare Tunnel (voir [cloudflare-tunnel.fr.md](cloudflare-tunnel.fr.md)).

## Service interne uniquement (sans Cloudflare)

Pour un service qui doit rester privé (accessible via le VPN uniquement), utilise les mêmes labels mais saute l'étape du Cloudflare Tunnel :

1. Choisis un nom d'hôte interne, ex. `monservice.homelab.internal`, et utilise-le dans la règle `Host(...)`.
2. Sur chaque poste client, ajoute-le au fichier `hosts`, pointant vers l'adresse VPN du homelab :
   ```
   <adresse-vpn-du-homelab>  monservice.homelab.internal
   ```
3. Ouvre `http://monservice.homelab.internal:8000/`.

Si le conteneur doit joindre des services de l'hôte (base de données, Traefik lui-même…) depuis un réseau Docker personnalisé, le pare-feu de l'hôte nécessite une règle pour le sous-réseau de ce réseau.
