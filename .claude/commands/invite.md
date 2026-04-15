---
description: Invitations LinkedIn (ou follow Instagram) en batch via POST /relations/invite — validation obligatoire.
---
# Invite — Invitations & follows

Envoie des **demandes de connexion LinkedIn** ou **follow Instagram** via la file Konect.

## Quand activer

- « invitation LinkedIn », « connect request », « follow Instagram »

## Prérequis

- Compte `connected` pour la plateforme cible
- **`profileId`** pour chaque cible (identifiant provider — le plus fiable après `/profiles` ou résultats de recherche)

## Appel API

```bash
curl -s -X POST "${KONECT_BASE_URL}/relations/invite" \
  -H "Authorization: Bearer ${KONECT_API_KEY}" \
  -H "Content-Type: application/json" \
  -d '{
    "accountId": "'"${KONECT_ACCOUNT_ID_LINKEDIN}"'",
    "profileId": "IDENTIFIANT_PROVIDER",
    "message": "Note optionnelle max 300 caractères (LinkedIn)"
  }'
```

- `message` : optionnel, **300 car max** sur LinkedIn.
- Instagram : follow via le même endpoint (sans message selon cas — suivre doc Konect du compte).

## Workflow

1. Vérifier quotas / warmup (infos compte `GET /accounts`).
2. Préparer la liste (profileId + note personnalisée courte).
3. **Validation utilisateur** avant tout envoi > 5 invitations.
4. Envoyer par vagues ; consigner `queueId` / erreurs.
5. Mettre à jour CRM : statut « Invité » ou équivalent.
6. Suivi : `GET /queue` pour états `completed` / `failed`.
