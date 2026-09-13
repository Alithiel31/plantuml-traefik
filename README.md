# PlantUML Server

🇫🇷 [Français](#français) | 🇬🇧 [English](#english)

---

## Français

Serveur [PlantUML](https://plantuml.com/) auto-hébergé, utilisé pour générer des diagrammes UML à partir de texte (architecture, séquence, etc.).

### Déploiement

Conçu pour un homelab Docker avec Traefik en reverse proxy. Service strictement interne, jamais exposé publiquement — pas de route publique, pas de port publié sur l'hôte.

```
cp .env.example .env
# éditer .env selon son environnement
docker compose up -d
```

### Réseau

Le conteneur ne publie aucun port. Il rejoint un réseau Docker externe déjà créé par l'instance Traefik du serveur, et est routé via un nom d'hôte interne défini dans `.env` (variable `PLANTUML_HOST`).

Ce nom d'hôte n'existe que sur le réseau local : il doit être ajouté manuellement dans le fichier `hosts` de chaque machine cliente, pointant vers l'adresse du serveur, par exemple :

```
<adresse-du-serveur>  <valeur-de-PLANTUML_HOST>
```

(Windows : `C:\Windows\System32\drivers\etc\hosts`, en admin)

Accès ensuite via `http://<valeur-de-PLANTUML_HOST>:<port-entrypoint-traefik>`, uniquement joignable depuis le réseau privé du serveur (VPN/tailnet).

### Configuration

Pas de secret à gérer (le service n'en a pas besoin). Les valeurs variables sont externalisées dans `.env` :

```
PLANTUML_IMAGE_TAG=jetty
TRAEFIK_NETWORK=traefik-net
TRAEFIK_ENTRYPOINT=web
PLANTUML_HOST=plantuml.internal
PLANTUML_INTERNAL_PORT=8080
```

`.env` est ignoré par git (`.gitignore`) — partir de `.env.example` et l'adapter à son propre environnement (nom de réseau Traefik, nom d'hôte interne, etc.).

### Prérequis côté serveur

- Docker + Docker Compose
- Un réseau Docker externe correspondant à `TRAEFIK_NETWORK`
- Une instance Traefik déjà en place, écoutant sur l'entrypoint défini par `TRAEFIK_ENTRYPOINT`, avec le provider Docker activé (labels)

### Sécurité

Ce dépôt est public : aucun secret, aucune information d'infrastructure réelle (nom de serveur, adresse IP, domaine) n'y figure. Toutes les valeurs concrètes vivent uniquement dans le `.env` local, non versionné.

---

## English

Self-hosted [PlantUML](https://plantuml.com/) server, used to generate UML diagrams from text (architecture, sequence, etc.).

### Deployment

Designed for a Docker homelab with Traefik as reverse proxy. Strictly internal service, never exposed publicly — no public route, no port published on the host.

```
cp .env.example .env
# edit .env for your own environment
docker compose up -d
```

### Networking

The container doesn't publish any port. It joins an external Docker network already created by the server's Traefik instance, and is routed via an internal hostname defined in `.env` (`PLANTUML_HOST`).

This hostname only exists on the local network: it must be added manually to the `hosts` file of every client machine, pointing to the server's address, e.g.:

```
<server-address>  <PLANTUML_HOST-value>
```

(Windows: `C:\Windows\System32\drivers\etc\hosts`, run as admin)

Then reachable at `http://<PLANTUML_HOST-value>:<traefik-entrypoint-port>`, only from the server's private network (VPN/tailnet).

### Configuration

No secrets to manage (the service doesn't need any). Variable values are externalized in `.env`:

```
PLANTUML_IMAGE_TAG=jetty
TRAEFIK_NETWORK=traefik-net
TRAEFIK_ENTRYPOINT=web
PLANTUML_HOST=plantuml.internal
PLANTUML_INTERNAL_PORT=8080
```

`.env` is git-ignored (`.gitignore`) — start from `.env.example` and adapt it to your own environment (Traefik network name, internal hostname, etc.).

### Server requirements

- Docker + Docker Compose
- An external Docker network matching `TRAEFIK_NETWORK`
- A running Traefik instance, listening on the entrypoint defined by `TRAEFIK_ENTRYPOINT`, with the Docker provider enabled (labels)

### Security

This repository is public: it contains no secrets and no real infrastructure information (server name, IP address, domain). All actual values live only in the local `.env` file, which is not version-controlled.
