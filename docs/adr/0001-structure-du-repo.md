# ADR 0001 — Monorepo, uv et src layout

- **Statut** : accepté — 2026-11-05

## Contexte
Le pipeline a plusieurs composants (code Python, dbt, Airflow, infra, CI) maintenus par la même équipe et versionnés ensemble.

## Décision
1. **Monorepo** : une PR peut modifier l'ingestion et le modèle dbt qui en dépend, testés ensemble.
2. **uv** pour l'environnement Python : un seul `uv.lock` reproductible, Python 3.12 figé (`.python-version` + `requires-python <3.13`).
3. **Groupes de dépendances** : `dev` (outillage), `dbt` (transformations). Le conteneur Airflow et la CI n'installent que ce dont ils ont besoin.
4. **src layout** (`src/altisport_data/`) : impossible d'importer le code « par accident » depuis la racine ; les tests utilisent le package installé, comme en prod.
5. **dbt et Airflow en sous-dossiers frères, jamais imbriqués** : `dbt/` n'est PAS dans `orchestration/`.
   Chacun a son cycle de vie : avec Astronomer, l'image Airflow est construite depuis `orchestration/` (`astro deploy`)
   et le projet dbt est déployé à part (`astro dbt deploy`). Modifier un modèle dbt ne reconstruit pas l'image.

## Conséquences
- Versions de dbt bloquées au mineur (`>=1.12,<1.13`) : toute montée de version est une PR explicite.
- Brique 7 : en local, `orchestration/docker-compose.override.yml` montera `../dbt` dans les conteneurs Astro ;
  sur Astro, la CI lancera `astro dbt deploy` en plus de `astro deploy`. À vérifier dans la doc Astronomer à ce moment-là.
