---
description: Rédiger et publier (ou planifier) un post Instagram via POST /posts avec le compte Instagram Konect.
---
# Instagram post

Même endpoint **`POST /posts`** que LinkedIn, avec `accountId` Instagram.

## Quand activer

- « post Instagram », « publier sur IG », « planifier un reel caption » (texte)

## Prérequis

- `KONECT_ACCOUNT_ID_INSTAGRAM`
- `memory/identity/brand.md`

## Exemple

```bash
curl -s -X POST "${KONECT_BASE_URL}/posts" \
  -H "Authorization: Bearer ${KONECT_API_KEY}" \
  -H "Content-Type: application/json" \
  -d '{
    "accountId": "'"${KONECT_ACCOUNT_ID_INSTAGRAM}"'",
    "content": "LÉGENDE_INSTAGRAM",
    "mediaUrl": "https://example.com/image.jpg",
    "scheduledAt": "2026-05-01T18:00:00.000Z"
  }'
```

- `mediaUrl` : fortement recommandé pour IG quand le post n’est pas texte seul.
- `scheduledAt` : optionnel (publication immédiate si omis selon API).

## Workflow

1. Brief : objectif, hooks 1re ligne, hashtags (avec modération).
2. Proposer légende ; itérer.
3. Valider média (URL publique accessible).
4. `POST /posts` ; noter `queueId`.
5. Airtable **Contenus** — plateforme Instagram.
