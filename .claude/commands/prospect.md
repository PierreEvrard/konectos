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

## Workflow (CRM-sync obligatoire)

1. Construire `query` + filtres à partir de l’ICP.
2. Exécuter la recherche ; normaliser les résultats (nom, headline, URL,
   identifiant public si présent).
3. **Dédupliquer contre Airtable `Contacts`** (URL de profil, handle,
   téléphone selon plateforme). Ne JAMAIS créer deux lignes pour le
   même lead.
4. **Pousser immédiatement** les nouveaux leads (non-dupes) dans
   `Contacts` avec :
   - `Statut = "New"`
   - `Plateforme source` (LinkedIn / Instagram / WhatsApp)
   - `Source` (ex. `LinkedIn Search`, `LinkedIn Sales Navigator`,
     `LinkedIn Followers`, `Instagram Search`, …)
   - `ID` → provider ID Konect du lead (indispensable pour les
     invitations LinkedIn et le ré-appariement des conversations)
   - `Relation` → degré LinkedIn (`1` / `2` / `3` / `3+`) si
     disponible dans le résultat de recherche
   - `Localisation` → ville du lead si fournie par la recherche
   - `LinkedIn URL` / `Instagram` / `WhatsApp` selon le canal
   - `Score ICP` initial (si calculable), `Dernier contact` vide.

   Ne pas se contenter d'un export markdown : tant que ce n'est pas
   dans Airtable, ça n'existe pas pour le système.
5. Prochaine étape : `/enrich` (profils manquants) puis `/icebreaker`.
