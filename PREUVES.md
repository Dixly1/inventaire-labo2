# Preuves — Laboratoire 1 (Pipeline Inventaire Docker)

Dépôt GitHub : https://github.com/Dixly1/inventaire-labo2
Images Docker Hub : `dixlyma/inventaire-postgres` et `dixlyma/inventaire-api` (tags `1.0` et `latest`, publics)

Toutes les étapes du pipeline ont été exécutées et validées le 2026-07-13.

---

## Preuve #1 — Dépôt GitHub (structure)
Contenu en ligne : `README.md`, `.gitignore`, `.env.example`, `docker-compose.yml`, `services/`
(`.env` correctement exclu par le `.gitignore`).
📸 Screenshot : page https://github.com/Dixly1/inventaire-labo2

## Preuve #2 — Schéma SQL auto-initialisé (psql)
```
$ docker compose exec -T postgres psql -U admin -d inventaire -c '\dt'
 Schema |    Name    | Type  | Owner
--------+------------+-------+-------
 public | articles   | table | admin
 public | categories | table | admin
(2 rows)

$ ... -c 'SELECT COUNT(*) FROM articles;'
 count
-------
     5
```

## Preuves #3, #4, #7 — Commits et push GitHub
```
9b2f7b7  Adam            docs: URL reelle du depot GitHub
f58c99c  Adam            feat: docker-compose, README
067db11  Adam - api      feat: service api Flask CRUD inventaire
58b5b74  Adam - postgres feat: service postgres avec schema SQL initial
2a36d97  Adam            chore: init projet inventaire + arborescence et fichiers de base
```
Deux auteurs distincts, un par service (travail solo : les deux rôles assumés par Adam).
📸 Screenshot : onglet Commits du dépôt

## Preuve #5 — `docker compose ps` local : deux services Up (healthy)
```
NAME                          SERVICE    STATUS
inventaire-labo2-api-1        api        Up (healthy)   0.0.0.0:5000->5000/tcp
inventaire-labo2-postgres-1   postgres   Up (healthy)   5432/tcp (interne, non exposé)
```

## Preuve #6 — Les 6 endpoints CRUD (tests locaux)
```
1) GET  /health      -> {"db":"connectee","statut":"ok"}
2) GET  /articles    -> 5 articles, total:5
3) POST /articles    -> {"id":6,"message":"cree"}            [HTTP 201]
4) PATCH /articles/6 -> {"id":6,"message":"mis a jour"}      [HTTP 200]
5) DELETE /articles/6-> {"id":6,"message":"supprime"}        [HTTP 200]
   vérif : TEST-001 absent de la liste active (suppression logique)
6) GET  /stats       -> {"nb":5,"stock":112,"valeur":"15788.88"}
```

## Preuve #8 — Images taguées
```
$ docker images | grep inventaire
dixlyma/inventaire-api:1.0
dixlyma/inventaire-api:latest
dixlyma/inventaire-postgres:1.0
dixlyma/inventaire-postgres:latest
```

## Preuve #9 — Publication Docker Hub (dépôts publics)
```
dixlyma/inventaire-postgres : tags 1.0 + latest (pushed 2026-07-13T15:04)
dixlyma/inventaire-api      : tags 1.0 + latest (pushed 2026-07-13T15:04)
is_private: False pour les deux
```
📸 Screenshots : https://hub.docker.com/r/dixlyma/inventaire-postgres/tags
                 https://hub.docker.com/r/dixlyma/inventaire-api/tags

## Preuve #10 — Pull depuis Docker Hub (machine vierge)
Toutes les images locales `inventaire*` supprimées (`docker images | grep inventaire` → aucune ligne),
puis depuis un dossier vide (`test-depuis-hub/` : uniquement `.env` + `docker-compose.yml` en mode `image:`) :
```
$ docker compose up -d
 Image dixlyma/inventaire-api:1.0 Pulling
 Image dixlyma/inventaire-postgres:1.0 Pulling
 ...
 Image dixlyma/inventaire-postgres:1.0 Pulled
 Image dixlyma/inventaire-api:1.0 Pulled
```

## Preuve #11 — Validation distante (compose ps + 3 curl)
```
test-depuis-hub-api-1        dixlyma/inventaire-api:1.0        Up (healthy)  0.0.0.0:5000->5000/tcp
test-depuis-hub-postgres-1   dixlyma/inventaire-postgres:1.0   Up (healthy)  5432/tcp

GET /health   -> {"db":"connectee","statut":"ok"}
GET /articles -> total: 5
GET /stats    -> {"nb":5,"stock":112,"valeur":"15788.88"}
```

## Preuve #12 — CRUD distant
```
POST /articles {"reference":"HUB-001","nom":"Test depuis Docker Hub",...}
 -> {"id":6,"message":"cree"} [HTTP 201]
GET /articles -> refs: [..., 'HUB-001']  ✅ HUB-001 présent
```

## Preuve #13 — Persistance
```
docker compose down      (volume pgdata CONSERVÉ)
docker compose up -d
GET /articles -> HUB-001 TOUJOURS PRÉSENT  ✅
```

---

## Reproduire les preuves pour les screenshots

```bash
# Stack depuis les sources (Preuves #2, #5, #6)
cd C:\Users\Adam\Desktop\Labdock\inventaire-labo2
docker compose up --build -d
docker compose ps
docker compose exec postgres psql -U admin -d inventaire -c '\dt'
curl http://localhost:5000/health

# Stack depuis Docker Hub (Preuves #10 à #13) — arrêter l'autre stack d'abord
cd C:\Users\Adam\Desktop\Labdock\test-depuis-hub
docker compose up -d
docker compose ps
```
