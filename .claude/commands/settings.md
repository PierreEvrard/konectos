---
description: Paramètres d’envoi et sync inbox Konect — PATCH /accounts/{id}/settings (timezone, fenêtre, week-end).
---
# Settings — Paramètres compte Konect

Met à jour la **fenêtre d’envoi** et options de sync pour réduire les risques et aligner les envois async avec le fuseau du client.

## Quand activer

- « fenêtre d’envoi », « timezone Konect », « heures d’envoi », `/settings`

## Prérequis

- ID du compte (`GET /accounts`) — noter l’UUID cible (LinkedIn / WhatsApp / Instagram séparément si besoin).

## Lecture implicite

`GET /accounts` ou `GET /accounts/{id}` selon disponibilité pour connaître les valeurs actuelles.

## Mise à jour

```bash
curl -s -X PATCH "${KONECT_BASE_URL}/accounts/${ACCOUNT_UUID}/settings" \
  -H "Authorization: Bearer ${KONECT_API_KEY}" \
  -H "Content-Type: application/json" \
  -d '{
    "timezone": "Europe/Paris",
    "send_start_hour": 9,
    "send_end_hour": 19,
    "send_on_weekends": false,
    "inbox_sync_enabled": true
  }'
```

Champs **tous optionnels** dans le schéma API : n’envoyer que ce qui doit changer.

## Réponses

- `200` : OK — refléter le JSON retourné
- `400` : par ex. aucun champ à mettre à jour
- `404` : mauvais `id`

## Workflow

1. Demander les préférences (fuseau, plage horaire, week-end oui/non, sync inbox).
2. Appliquer par **compte** (répéter pour chaque `accountId` concerné).
3. Mettre à jour `memory/operational/config.md` (section paramètres d’envoi) pour traçabilité.
