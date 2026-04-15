---
description: Recherche de leads LinkedIn via Konect POST /linkedin/search + filtres ICP, export vers mémoire ou Airtable.
---
# Prospect — Recherche LinkedIn

Utilise **`POST /linkedin/search`** (résultats synchrones, pas de file).

## Quand activer

- « trouver des leads », « recherche LinkedIn », « prospecter »

## Prérequis

- `KONECT_ACCOUNT_ID_LINKEDIN` dans `.env` + `memory/operational/config.md`
- `memory/identity/persona.md` (ICP)

## Appel API

```bash
curl -s -X POST "${KONECT_BASE_URL}/linkedin/search" \
  -H "Authorization: Bearer ${KONECT_API_KEY}" \
  -H "Content-Type: application/json" \
  -d '{
    "accountId": "'"${KONECT_ACCOUNT_ID_LINKEDIN}"'",
    "query": "TA_RECHERCHE",
    "category": "people",
    "limit": 25,
    "filters": {
      "network": [2, 3],
      "location": "France",
      "industry": "",
      "title": ""
    }
  }'
```

`category` : `people` | `companies` | `jobs` | `posts`  
`filters.network` : `1`, `2`, `3` (distance réseau, si supporté par le compte)

## Workflow

1. Construire `query` + filtres à partir de l’ICP.
2. Exécuter la recherche ; normaliser les résultats (nom, headline, URL, identifiant public si présent).
3. Dédupliquer vs Airtable **Contacts** si base configurée.
4. Proposer import (batch max raisonnable) ou export markdown dans `generated/`.
5. Prochaine étape : `/enrich` ou `/icebreaker`.
