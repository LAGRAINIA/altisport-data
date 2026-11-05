# altisport-data

Plateforme data d'**Alti Sport** (enseigne fictive d'articles de sport, 40 magasins FR/MA + e-commerce).
Pipeline : **générateur → S3 → Databricks (bronze) → dbt (staging → marts) → dashboard**, orchestré par **Airflow**, livré par **GitHub Actions**.

## Démarrage en 2 commandes

Prérequis : [uv](https://docs.astral.sh/uv/) (installe lui-même Python 3.12).

```bash
uv sync                          # crée .venv avec Python 3.12 + toutes les dépendances figées (uv.lock)
uv run pre-commit install        # active les contrôles à chaque commit
```

Puis `cp .env.example .env` et remplir les valeurs (jamais commité).

## Arborescence

| Dossier | Qui l'utilise, quand |
|---|---|
| `src/altisport_data/` | Code Python packagé : générateur de données (brique 2), ingestion S3 → Databricks (brique 4) |
| `tests/` | Tests Python (pytest), lancés en local et en CI |
| `dbt/` | Projet dbt : les devs en local, Airflow chaque nuit, la CI à chaque PR |
| `orchestration/` | Projet Airflow (Astro CLI), tourne en conteneur |
| `infra/` | Ressources AWS décrites en fichiers (IAM, S3, budget) |
| `docs/adr/` | Décisions d'architecture (pourquoi on a choisi X) |
| `.github/` | CI/CD, CODEOWNERS, template de PR |

## Règles de contribution

- `main` est protégée : tout passe par une **branche + PR**, avec revue.
- La prod n'est déployée **que par la CI** au merge sur `main`.
- Aucun secret dans Git : `pre-commit` (gitleaks) bloque le commit.

## Commandes utiles

```bash
uv run pytest                    # tests Python
uv run pre-commit run --all-files
uv run ruff check . --fix
```
