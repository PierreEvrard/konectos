---
description: Rédiger et publier (ou planifier) un post LinkedIn via POST /posts Konect.
---
# Post — LinkedIn

Crée le texte du post, valide avec l’utilisateur, publie ou planifie avec Konect.

## Quand activer

- « post LinkedIn », « publier sur LinkedIn », « planifier un post »

## Prérequis

- `KONECT_ACCOUNT_ID_LINKEDIN`
- `memory/identity/brand.md` pour le ton

## Publication immédiate

```bash
curl -s -X POST "${KONECT_BASE_URL}/posts" \
  -H "Authorization: Bearer ${KONECT_API_KEY}" \
  -H "Content-Type: application/json" \
  -d '{
    "accountId": "'"${KONECT_ACCOUNT_ID_LINKEDIN}"'",
    "content": "TEXTE_DU_POST"
  }'
```

## Planification

```bash
curl -s -X POST "${KONECT_BASE_URL}/posts" \
  -H "Authorization: Bearer ${KONECT_API_KEY}" \
  -H "Content-Type: application/json" \
  -d '{
    "accountId": "'"${KONECT_ACCOUNT_ID_LINKEDIN}"'",
    "content": "TEXTE_DU_POST",
    "scheduledAt": "2026-05-01T09:00:00.000Z"
  }'
```

`scheduledAt` : **ISO 8601** UTC recommandé.

## Média optionnel

Si supporté par ton instance Konect : champ `mediaUrl` (URL publique du média).

## Workflow

1. Brief : angle, CTA, longueur.
2. Proposer 1–2 versions ; itérer.
3. Après validation : `POST /posts` ; noter `queueId`.
4. Créer / mettre à jour ligne **Contenu** dans Airtable (statut Planifié / Publié selon cas).
