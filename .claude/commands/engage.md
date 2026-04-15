---
description: Engagement sur posts — commentaires et réactions via POST /posts/{id}/comments et /react (LinkedIn & Instagram selon compte).
---
# Engage — Commentaires & réactions

Utilise les endpoints **comment** et **react** sur un **`post_id`** déjà connu (format attendu par Konect, ex. identifiant `social_id` côté LinkedIn selon doc).

## Quand activer

- « liker un post », « commenter », « engagement LinkedIn », « engagement Instagram »

## Prérequis

- `post_id` valide pour la plateforme
- `accountId` du compte qui agit (LinkedIn ou Instagram)

## Réaction

```bash
curl -s -X POST "${KONECT_BASE_URL}/posts/${POST_ID}/react" \
  -H "Authorization: Bearer ${KONECT_API_KEY}" \
  -H "Content-Type: application/json" \
  -d '{
    "accountId": "'"${ACCOUNT_ID}"'",
    "reaction": "like"
  }'
```

Réactions LinkedIn : `like`, `celebrate`, `support`, `love`, `insightful`, `funny`.  
Instagram : **`like`** (selon doc).

## Commentaire

```bash
curl -s -X POST "${KONECT_BASE_URL}/posts/${POST_ID}/comments" \
  -H "Authorization: Bearer ${KONECT_API_KEY}" \
  -H "Content-Type: application/json" \
  -d '{
    "accountId": "'"${ACCOUNT_ID}"'",
    "content": "COMMENTAIRE_COURT_VALIDE"
  }'
```

## Workflow

1. L’utilisateur fournit la liste `(post_id, plateforme, intention)` ou tu la construis depuis une veille manuelle.
2. Rédiger des commentaires **authentiques**, courts, sans spam ni promesse irréaliste.
3. **Validation** utilisateur avant exécution batch.
4. Exécuter ; consigner `queueId` pour chaque action.
5. Option `/memory-save` : ce qui performe comme type de commentaire.

## Limite

Sans `post_id` fiable, ne pas inventer d’IDs — demander la source (URL, export, etc.).
