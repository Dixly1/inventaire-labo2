# Laboratoire 2 — Inventaire Docker

API Flask + PostgreSQL, conteneurisée et publiée sur Docker Hub.

## Prérequis
- Docker et Docker Compose installés
- Copier `.env.example` en `.env` et remplir `POSTGRES_PASSWORD`

## Déploiement depuis le code source
```bash
git clone https://github.com/Dixly1/inventaire-labo2.git
cd inventaire-labo2
cp .env.example .env   # remplir POSTGRES_PASSWORD
docker compose up --build -d
```

## Images Docker Hub
- postgres : `dixlyma/inventaire-postgres:1.0`
- api      : `dixlyma/inventaire-api:1.0`

## Déploiement depuis Docker Hub (sans code source)
Voir la Partie C du laboratoire. Créer un dossier vide, y placer un `.env`
et un `docker-compose.yml` utilisant `image:` au lieu de `build:`, puis :
```bash
docker compose up -d
```

## Tester
```bash
curl http://localhost:5000/health
curl http://localhost:5000/articles
curl http://localhost:5000/stats
```

## Endpoints
| Méthode | Route             | Description                       |
|---------|-------------------|-----------------------------------|
| GET     | `/health`         | État du service et de la BD       |
| GET     | `/articles`       | Liste des articles actifs         |
| GET     | `/articles/<id>`  | Détail d'un article               |
| POST    | `/articles`       | Créer un article                  |
| PATCH   | `/articles/<id>`  | Modifier un article               |
| DELETE  | `/articles/<id>`  | Suppression logique (actif=FALSE) |
| GET     | `/version`        | Version de l'API et auteurs       |
| GET     | `/stats`          | Statistiques de l'inventaire      |

---

# Déploiement distant

## URLs publiques
- API (Render)  : `https://inventaire-api-XXXX.onrender.com`
- Health        : `https://inventaire-api-XXXX.onrender.com/health`
- Articles      : `https://inventaire-api-XXXX.onrender.com/articles`
- URL Codespace : `https://XXXX-5000.app.github.dev` (temporaire, change à chaque session)

## Images Docker Hub
- postgres  : `dixlyma/inventaire-postgres:1.0`
- api v1.0  : `dixlyma/inventaire-api:1.0`
- api v1.1  : `dixlyma/inventaire-api:1.1`

## Partie A — Reproduire le déploiement Codespace
1. Ouvrir un Codespace sur ce dépôt (bouton **Code → Codespaces → Create codespace on main**).
2. `cp .env.example .env` puis remplir `POSTGRES_PASSWORD`.
3. `docker compose -f compose-hub.yml up -d`
4. Onglet **PORTS** → publier le port **5000** en **Public**.
5. Tester : `https://XXXX-5000.app.github.dev/health`

## Partie B — Reproduire le déploiement Render

### Option rapide — Blueprint (`render.yaml`, recommandé)
1. Dashboard Render → **New + → Blueprint**.
2. Connecter le dépôt `Dixly1/inventaire-labo2` → **Apply**.
   Render crée la base PostgreSQL + le Web Service (image `dixlyma/inventaire-api:1.1`)
   avec toutes les variables d'environnement câblées automatiquement.
3. Charger le schéma : coller `services/postgres/init/01_schema.sql` dans le **Shell psql** Render.
4. Tester : `https://inventaire-api-XXXX.onrender.com/health`

### Option manuelle
1. Créer une base **PostgreSQL** sur render.com (plan Free).
2. Exécuter `services/postgres/init/01_schema.sql` dans le shell psql Render.
3. Créer un **Web Service** avec l'image `dixlyma/inventaire-api:1.1`
   (Deploy an existing image from a registry).
4. Configurer les variables d'environnement (voir `.env.example` + `POSTGRES_HOST`
   = External Host de la base Render).
5. Tester : `https://inventaire-api-XXXX.onrender.com/health`

## Mettre à jour l'API (nouvelle version)
```bash
docker build -t dixlyma/inventaire-api:1.2 ./services/api
docker push dixlyma/inventaire-api:1.2
# Puis changer l'Image URL dans les Settings du Web Service Render
```
