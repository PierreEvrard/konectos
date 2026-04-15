---
description: Agent DMs Instagram — lit les conversations Konect, applique agent-prompts.md section Instagram, envoie après validation.
---
# Instagram Agent — Répondeur DMs

Identique à **LinkedIn Agent** / **WhatsApp Agent** avec `platform=instagram` et le prompt **Instagram** dans `memory/operational/agent-prompts.md`.

## Quand activer

- « DMs Instagram », « répondre sur Instagram », `/instagram-agent`

## Prérequis

- `KONECT_ACCOUNT_ID_INSTAGRAM`
- `memory/operational/agent-prompts.md` — section **Instagram**
- `memory/identity/offer.md`

## Conversations

```bash
curl -s "${KONECT_BASE_URL}/conversations?accountId=${KONECT_ACCOUNT_ID_INSTAGRAM}&platform=instagram&limit=50" \
  -H "Authorization: Bearer ${KONECT_API_KEY}"
```

## Messages

```bash
curl -s "${KONECT_BASE_URL}/conversations/${CHAT_ID}?limit=100" \
  -H "Authorization: Bearer ${KONECT_API_KEY}"
```

## Envoi (réponse)

```bash
curl -s -X POST "${KONECT_BASE_URL}/messages" \
  -H "Authorization: Bearer ${KONECT_API_KEY}" \
  -H "Content-Type: application/json" \
  -d '{
    "accountId": "'"${KONECT_ACCOUNT_ID_INSTAGRAM}"'",
    "chatId": "'"${CHAT_ID}"'",
    "content": "REPONSE_VALIDEE"
  }'
```

## Premier message (nouveau fil)

Utiliser `to` avec l’identifiant accepté par Konect pour Instagram (voir doc / tests sur compte de dev) :

```bash
curl -s -X POST "${KONECT_BASE_URL}/messages" \
  -H "Authorization: Bearer ${KONECT_API_KEY}" \
  -H "Content-Type: application/json" \
  -d '{
    "accountId": "'"${KONECT_ACCOUNT_ID_INSTAGRAM}"'",
    "to": "IDENTIFIANT_IG",
    "content": "MESSAGE_VALIDE"
  }'
```

## Workflow

1. Prioriser `unread_count` ou previews pertinentes.
2. Lire thread, classifier, générer réponse **courte** (ton IG).
3. Validation utilisateur obligatoire avant envoi.
4. MAJ Airtable.
