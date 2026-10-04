# traefik-homelab

[English version](README.md)

Reverse proxy Traefik v3.6 pour un homelab Raspberry Pi 5. Il route les services internes via les labels Docker et se place derrière un Cloudflare Tunnel (`cloudflared`) : aucun port n'est exposé directement sur Internet.

## Fonctionnement

- Entrypoint `web` sur le port `8000` (HTTP seul ; le TLS est terminé par Cloudflare).
- Provider Docker avec `exposedbydefault=false` : un conteneur n'est routé que s'il porte `traefik.enable=true` et ses propres labels de routeur.
- Les conteneurs routés doivent rejoindre le réseau externe `traefik-net`.
- `forwardedHeaders.trustedIPs` est limité à l'IP de la passerelle du tunnel pour éviter l'usurpation d'IP client.

## Utilisation

```bash
docker network create traefik-net   # une seule fois
docker compose up -d
```

## Notes de sécurité

- Le socket Docker est monté en lecture seule (`:ro`). Cela limite les écritures mais expose quand même les métadonnées des conteneurs ; un socket proxy serait plus strict.
- Adapter `trustedIPs` à l'adresse de ton tunnel/passerelle.

## Licence

MIT
