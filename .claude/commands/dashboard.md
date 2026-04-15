---
description: Vue d’ensemble — usage Konect GET /usage, file GET /queue, conversations actives, rappels CRM.
---
# Dashboard

Synthèse **Konect + CRM** pour décider quoi faire dans la prochaine session.

## Quand activer

- « dashboard », « vue d’ensemble », « queue », « stats Konect »

## Usage (période)

```bash
curl -s "${KONECT_BASE_URL}/usage" \
  -H "Authorization: Bearer ${KONECT_API_KEY}"
```

Option fenêtre :

```bash
curl -s "${KONECT_BASE_URL}/usage?since=2026-04-01T00:00:00Z&until=2026-04-15T23:59:59Z" \
  -H "Authorization: Bearer ${KONECT_API_KEY}"
```

## File d’attente

```bash
curl -s "${KONECT_BASE_URL}/queue?status=queued&limit=50" \
  -H "Authorization: Bearer ${KONECT_API_KEY}"
```

Autres statuts : `processing`, `completed`, `failed`.

## Annuler une action encore « queued »

```bash
curl -s -X DELETE "${KONECT_BASE_URL}/queue/${QUEUE_ITEM_ID}" \
  -H "Authorization: Bearer ${KONECT_API_KEY}"
```

(`204` attendu ; `409` si déjà en cours.)

## Conversations

Aperçu rapide par plateforme (unread) :

```bash
curl -s "${KONECT_BASE_URL}/conversations?accountId=${KONECT_ACCOUNT_ID_LINKEDIN}&platform=linkedin&limit=20" \
  -H "Authorization: Bearer ${KONECT_API_KEY}"
```

Répéter pour WhatsApp / Instagram si IDs configurés.

## CRM

Si Airtable prêt : compter les contacts par **Statut** (requêtes filtrées ou agrégation côté Claude sur un export récent).

## Sortie attendue

Un **tableau** : comptes / usage / queue / top actions / rappels humains (relances, posts planifiés, DMs non lus).
