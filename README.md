# Laboratoire 2 — Inventaire Docker

API Flask + PostgreSQL, conteneurisée et publiée sur Docker Hub.

## Prérequis
- Docker et Docker Compose installés
- Copier `.env.example` en `.env` et remplir `POSTGRES_PASSWORD`

## Déploiement depuis le code source
```bash
git clone https://github.com/VOTRE_USER/inventaire-labo2.git
cd inventaire-labo2
cp .env.example .env   # remplir POSTGRES_PASSWORD
docker compose up --build -d
```

## Images Docker Hub
- postgres : `ETUDIANT1/inventaire-postgres:1.0`
- api      : `ETUDIANT2/inventaire-api:1.0`

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
| GET     | `/stats`          | Statistiques de l'inventaire      |
