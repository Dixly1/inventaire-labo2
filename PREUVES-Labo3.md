# Preuves — Laboratoire 3 (Déploiement distant)

Dépôt : https://github.com/Dixly1/inventaire-labo2
Images Docker Hub : `dixlyma/inventaire-postgres:1.0`, `dixlyma/inventaire-api:1.0`, `dixlyma/inventaire-api:1.1`
Exécuté le 2026-07-28.

---

## PARTIE A — GitHub Codespaces

Codespace : `expert-space-spoon-wrvpwqw7v4px356w4` (dépôt Dixly1/inventaire-labo2, branche main).
URL publique : `https://expert-space-spoon-wrvpwqw7v4px356w4-5000.app.github.dev`

### Preuve #1 — Docker disponible dans le Codespace
```
$ docker --version
Docker version 29.3.0-1, build 5927d80c76b3ce5cf782be818922966e8a0d87a3
$ docker compose version
Docker Compose version v2.40.3
```

### Preuve #2 — Stack démarrée avec les images Docker Hub
```
$ docker compose -f compose-hub.yml ps
NAME                          IMAGE                             SERVICE    STATUS
inventaire-labo2-api-1        dixlyma/inventaire-api:1.0        api        Up (healthy)
inventaire-labo2-postgres-1   dixlyma/inventaire-postgres:1.0   postgres   Up (healthy)
```

### Preuve #3 — /health via l'URL publique (navigateur externe)
```
GET https://expert-space-spoon-wrvpwqw7v4px356w4-5000.app.github.dev/health
{"db":"connectee","statut":"ok"}
```

### Preuve #4 — /articles (5 articles initiaux) via l'URL publique
```
GET .../articles  → total = 5
  CPU-001 - Processeur AMD Ryzen 7
  RAM-001 - Barrette RAM 16GB DDR5
  SSD-001 - SSD NVMe 1TB
  KBD-001 - Clavier mecanique TKL
  NET-001 - Switch 24 ports Gigabit
```

### Preuve #5 — CRUD complet depuis l'extérieur (URL publique)
```
POST .../articles  {"reference":"CLOUD-001","nom":"Article deploiement distant",
                    "quantite":5,"prix_unitaire":9.99}
→ {"id":6,"message":"cree"}   HTTP 201

GET .../articles → ['CLOUD-001','RAM-001','KBD-001','CPU-001','SSD-001','NET-001']
GET .../stats    → {"nb":6,"stock":117,"valeur":"15838.83"}
```

### Preuve #6 — Accès depuis une autre machine / réseau (coéquipier)
URL publique ouverte hors Codespace → réponse JSON identique, prouvant l'accès Internet.

---

## PARTIE B — Render.com (déployée via Blueprint render.yaml)

URL publique : `https://inventaire-api-n4x1.onrender.com`
Base : `inventaire-db` (PostgreSQL 18, Oregon, plan Free) — base `inventaire_b19t`, user `admin`.

### Preuve #7 — Base PostgreSQL Render (infos de connexion) ✅
Base créée par le Blueprint, statut **Available**. Hostname `dpg-...oregon-postgres.render.com`, port 5432.

### Preuve #8 — Schéma initialisé ✅
```
TABLES: ['articles', 'categories']
ARTICLES: 5
CATEGORIES: 4
```

### Preuve #9 — Web Service Live ✅
`inventaire-api` (image `dixlyma/inventaire-api:1.1`, plan Free) — statut **Live**,
URL `https://inventaire-api-n4x1.onrender.com`.

### Preuve #10 — Tests CRUD via l'URL Render ✅
```
GET  /health   → {"db":"connectee","statut":"ok"}
GET  /articles → 5 articles initiaux
POST /articles {"reference":"RENDER-001",...} → {"id":6,"message":"cree"}  HTTP 201
GET  /articles → ['RENDER-001','RAM-001','KBD-001','CPU-001','SSD-001','NET-001']
GET  /stats    → {"nb":6,"stock":115,"valeur":"15848.85"}
```

### Preuve #13 — Mise à jour v1.1 sur Render ✅
```
GET /version → {"version":"1.1","auteurs":"Adam Aougar"}
```

### Preuve #12 — Persistance
Marqueur `PERSIST-001` créé (id 7). Redémarrage du service à effectuer pour la capture
(données persistées dans la base PostgreSQL Render, indépendante du service Flask).

## Preuve #14 — Commits Labo 3 sur GitHub
```
21de31f feat: labo3 - deploiement distant Codespace et Render
```
Fichiers : `compose-hub.yml`, `services/api/app.py` (route /version), `README.md`.
