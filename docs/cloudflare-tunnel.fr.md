# Cloudflare Tunnel

[English version](cloudflare-tunnel.md)

Chemin du trafic : `Internet → Cloudflare (TLS) → cloudflared → Traefik :8000 → conteneur`.

## Configuration du tunnel

Dans le tableau de bord Cloudflare Zero Trust (Networks → Tunnels → ton tunnel → Public hostname), ajoute un nom d'hôte par service et pointe-le vers Traefik :

| Champ    | Valeur                                                            |
| -------- | ----------------------------------------------------------------- |
| Hostname | `whoami.example.com`                                              |
| Service  | `HTTP` → `localhost:8000` (ou `traefik:8000` si `cloudflared` est sur `traefik-net`) |

Le nom d'hôte doit correspondre à la règle `Host(...)` du routeur du conteneur.

## Trouver les bons `trustedIPs`

`forwardedHeaders.trustedIPs` doit contenir l'adresse que Traefik voit comme *source* des requêtes du tunnel. Seules les requêtes venant de cette adresse voient leur en-tête `X-Forwarded-For` accepté.

- Si `cloudflared` tourne sur l'hôte, c'est en général la passerelle de `traefik-net` : `docker network inspect traefik-net --format '{{ (index .IPAM.Config 0).Gateway }}'`.
- Si `cloudflared` tourne dans un conteneur sur `traefik-net`, utilise l'IP de ce conteneur (ou fixe le sous-réseau du réseau).

Modifie la valeur dans `docker-compose.yml` (actuellement `192.168.64.1/32`) puis lance `docker compose up -d`. Une plage trop large permet aux clients d'usurper leur IP.
