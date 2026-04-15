---
description: Relances basées sur conversations Konect (since, unread) et statuts CRM — pas d’envoi sans validation.
---
# Followup — Relances

Identifie les **conversations à relancer** (pas de réponse inbound récente, ou statut CRM) et propose des messages courts.

## Quand activer

- « relance », « follow-up », « pas de réponse »

## Prérequis

- Account IDs + `memory/operational/templates.md` / `agent-prompts.md` (section relance)
- Airtable optionnel pour croiser statuts

## Lister l’activité récente

```bash
curl -s "${KONECT_BASE_URL}/conversations?accountId=${KONECT_ACCOUNT_ID_LINKEDIN}&platform=linkedin&limit=50" \
  -H "Authorization: Bearer ${KONECT_API_KEY}"
```

Utiliser `since` (ISO date-time) pour limiter aux échanges récents :

```
.../conversations?accountId=...&platform=linkedin&since=2026-04-01T00:00:00Z&limit=50
```

## Détail d’une conversation

```bash
curl -s "${KONECT_BASE_URL}/conversations/${CHAT_ID}?limit=100" \
  -H "Authorization: Bearer ${KONECT_API_KEY}"
```

Analyser `direction` : `inbound` / `outbound` et dates.

## Envoi relance

Toujours **`chatId`** (conversation existante) :

```bash
curl -s -X POST "${KONECT_BASE_URL}/messages" \
  -H "Authorization: Bearer ${KONECT_API_KEY}" \
  -H "Content-Type: application/json" \
  -d '{
    "accountId": "'"${KONECT_ACCOUNT_ID_LINKEDIN}"'",
    "chatId": "'"${CHAT_ID}"'",
    "content": "RELANCE_VALIDEE"
  }'
```

## Workflow

1. Choisir plateforme (`linkedin`, `whatsapp`, `instagram`).
2. Lister conversations pertinentes ; détecter « notre dernier message sans réponse » depuis N jours.
3. Proposer textes de relance (1 question max si style WhatsApp).
4. Valider avec l’utilisateur puis envoyer par petits lots.
5. MAJ CRM.
