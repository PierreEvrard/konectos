---
description: Préparer la prospection Instagram — followers Konect, imports de handles, enrichissement, ciblage DM.
---
# Instagram prospect — Cibles DM

Konect ne fournit pas d’équivalent public à **`/linkedin/search`** pour Instagram. La prospection IG se combine avec :

- la liste **followers** (ou relations) du compte connecté ;
- des **listes importées** (CSV / manuel) validées par l’utilisateur ;
- le parcours **posts → commentaires** géré manuellement ou via `/engage` quand tu disposes des **post IDs** Konect.

## Quand activer

- « trouver des comptes Instagram », « cibles DM Instagram »

## Prérequis

- `KONECT_ACCOUNT_ID_INSTAGRAM` connecté

## Followers Instagram (source interne)

```bash
curl -s "${KONECT_BASE_URL}/relations/followers?accountId=${KONECT_ACCOUNT_ID_INSTAGRAM}&limit=50" \
  -H "Authorization: Bearer ${KONECT_API_KEY}"
```

Paginer avec `cursor` si présent dans la réponse.

## Profil (vérifier un handle / ID)

```bash
curl -s "${KONECT_BASE_URL}/profiles/HANDLE_OU_ID?accountId=${KONECT_ACCOUNT_ID_INSTAGRAM}" \
  -H "Authorization: Bearer ${KONECT_API_KEY}"
```

## Workflow

1. Définir l’angle ICP (`persona.md`) : niche, taille, langue.
2. Collecter des candidats (followers + imports utilisateur).
3. Filtrer / scorer localement (règles simples).
4. Sortie : table markdown ou lignes prêtes pour **Airtable Contacts** (`Plateforme source` = Instagram).
5. Suite : `/icebreaker` (canal Instagram) ou `/instagram-agent`.

## Éthique & conformité

Ne pas scraper hors API ; respecter les règles d’usage Konect / Meta et la validation utilisateur avant messages massifs.
