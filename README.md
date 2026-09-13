# PlantUML Server

Serveur [PlantUML](https://plantuml.com/) auto-hébergé, utilisé pour générer des diagrammes UML à partir de texte (architecture, séquence, etc.).

## Déploiement

Conçu pour un homelab Docker avec Traefik en reverse proxy. Service strictement interne, jamais exposé publiquement — pas de route publique, pas de port publié sur l'hôte.

```
cp .env.example .env
# éditer .env selon son environnement
docker compose up -d
```

## Réseau

Le conteneur ne publie aucun port. Il rejoint un réseau Docker externe déjà créé par l'instance Traefik du serveur, et est routé via un nom d'hôte interne défini dans `.env` (variable `PLANTUML_HOST`).

Ce nom d'hôte n'existe que sur le réseau local : il doit être ajouté manuellement dans le fichier `hosts` de chaque machine cliente, pointant vers l'adresse du serveur, par exemple :

```
<adresse-du-serveur>  <valeur-de-PLANTUML_HOST>
```

(Windows : `C:\Windows\System32\drivers\etc\hosts`, en admin)

Accès ensuite via `http://<valeur-de-PLANTUML_HOST>:<port-entrypoint-traefik>`, uniquement joignable depuis le réseau privé du serveur (VPN/tailnet).

## Configuration

Pas de secret à gérer (le service n'en a pas besoin). Les valeurs variables sont externalisées dans `.env` :

```
PLANTUML_IMAGE_TAG=jetty
TRAEFIK_NETWORK=traefik-net
TRAEFIK_ENTRYPOINT=web
PLANTUML_HOST=plantuml.internal
PLANTUML_INTERNAL_PORT=8080
```

`.env` est ignoré par git (`.gitignore`) — partir de `.env.example` et l'adapter à son propre environnement (nom de réseau Traefik, nom d'hôte interne, etc.).

## Prérequis côté serveur

- Docker + Docker Compose
- Un réseau Docker externe correspondant à `TRAEFIK_NETWORK`
- Une instance Traefik déjà en place, écoutant sur l'entrypoint défini par `TRAEFIK_ENTRYPOINT`, avec le provider Docker activé (labels)

## Sécurité

Ce dépôt est public : aucun secret, aucune information d'infrastructure réelle (nom de serveur, adresse IP, domaine) n'y figure. Toutes les valeurs concrètes vivent uniquement dans le `.env` local, non versionné.
