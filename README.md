# Valorisation Donnée Météo

Projet Data For Good - Saison 14

![CI](https://github.com/ChanFrancis/DevOpsEx1/actions/workflows/ci.yml/badge.svg)
![Pre-commit](https://github.com/ChanFrancis/DevOpsEx1/actions/workflows/pre-commit.yaml/badge.svg)

## Structure du projet

```text
├── backend/              # API Django/DRF et traitement des données météo
├── frontend/             # Interface utilisateur Nuxt
├── prometheus/           # Configuration Prometheus
├── grafana/              # Dashboards et provisioning Grafana
├── nginx/                # Configuration reverse proxy
├── .github/workflows/    # Pipelines CI/CD
```

## Lancer le projet en local

```bash
cp .env.example .env
docker compose up --build
```

| Service    | URL                        |
|------------|----------------------------|
| Frontend   | http://localhost:3000       |
| API Django | http://localhost:8000       |
| Swagger    | http://localhost:8000/api/docs/ |
| Prometheus | http://localhost:9090       |
| Grafana    | http://localhost:3001       |

Grafana : identifiants par défaut `admin` / `admin`.

## CI/CD

Le pipeline GitHub Actions (`.github/workflows/ci.yml`) s'exécute sur chaque push et pull request :

| Job | Déclencheur | Description |
|-----|-------------|-------------|
| Backend CI | tout push / PR | lint (ruff), tests pytest, rapport JUnit |
| Frontend CI | tout push / PR | lint (eslint), tests unitaires, build, audit npm |
| Security Scan | après backend + frontend | scan Trivy filesystem, rapport JSON |
| Build & Push Docker | push sur `main` uniquement | build et push vers GHCR |

Les images Docker sont publiées sur `ghcr.io/ChanFrancis/DevOpsEx1`.

## Monitoring

- **Prometheus** scrape le backend Django toutes les 5 secondes via l'endpoint `/metrics` (django-prometheus)
- **Grafana** est pré-configuré avec la datasource Prometheus et un dashboard backend (requêtes/s, statuts HTTP, latence p95, requêtes en vol)

## Sécurité

- Scan Trivy à chaque CI, rapport uploadé en artifact
- Fichier `vex.json` (OpenVEX) pour déclarer les vulnérabilités non applicables
- `npm audit --audit-level=high` bloquant dans la CI frontend

## Pour commencer

Consultez les README de chaque sous-projet :

- [Backend](backend/README.md)
- [Frontend](frontend/README.md)

## Workflow de contribution

Lire attentivement nos bonnes pratiques de développement : [Branches et commits : Workflow de contribution](https://outline.services.dataforgood.fr/doc/onboarding-dev-OFGKWOcxOn)

### Paramétrer git

```bash
git config --local pull.rebase merges
git config --local rebase.autostash true
```

### Créer une branche depuis `main`

```bash
git checkout main
git pull origin main
git checkout -b <type>/(<scope>/)?<description-courte>
```

Convention de nommage des branches :

- `feat/scope/nom-feature` : nouvelle fonctionnalité
- `fix/scope/nom-bug` : correction de bug
- `docs/sujet` : documentation
- `refactor/sujet` : refactoring de code
- `chore/sujet` : tâche de maintenance

### Faire des commits atomiques

```bash
git add <fichiers-concernés>
git commit -m "<type>: (<scope>:)? <description>"
```

Exemples : `feat: itn: ajoute le composant carte météo`, `fix: ecarts normales: corrige l'affichage des températures négatives`

### Pousser et créer une Pull Request

```bash
git push origin <nom-de-ta-branche>
```

Puis sur GitHub, créer une PR vers `main` avec un titre clair et des reviewers assignés. Merger avec **Squash and merge**.

### Bonnes pratiques

- Synchroniser régulièrement sa branche avec `main` (`git rebase main`)
- Ne jamais pusher directement sur `main`
- Une PR = une fonctionnalité ou un fix

## Pre-commit hooks

```bash
pip install pre-commit
pre-commit install
```

Exécution manuelle :

```bash
pre-commit run --all-files
```

Outils : **Ruff** (backend), **ESLint + Prettier** (frontend), vérifications communes (conflits, fins de ligne, trailing whitespace).
