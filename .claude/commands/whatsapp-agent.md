---
description: Agent conversations WhatsApp — lit Konect, applique le prompt WhatsApp (qualification), propose réponses courtes, envoie après validation.
---
# WhatsApp Agent — Répondeur

Même architecture que **LinkedIn Agent**, avec le **bloc WhatsApp** de `memory/operational/agent-prompts.md` (messages courts, qualification Situation → Objectif → Contexte, règles lien / prix).

## Quand activer

- « WhatsApp », « répondre sur WhatsApp », `/whatsapp-agent`

## Prérequis

1. `memory/operational/config.md` — `KONECT_ACCOUNT_ID_WHATSAPP`
2. `memory/operational/agent-prompts.md` — section **WhatsApp**
3. `memory/identity/offer.md` (CTA, règles prix)

## Conversations (API Konect)

```bash
curl -s "${KONECT_BASE_URL}/conversations?accountId=${KONECT_ACCOUNT_ID_WHATSAPP}&platform=whatsapp&limit=50" \
  -H "Authorization: Bearer ${KONECT_API_KEY}"
```

## Messages

```bash
curl -s "${KONECT_BASE_URL}/conversations/${CHAT_ID}?limit=100" \
  -H "Authorization: Bearer ${KONECT_API_KEY}"
```

## Envoi

```bash
curl -s -X POST "${KONECT_BASE_URL}/messages" \
  -H "Authorization: Bearer ${KONECT_API_KEY}" \
  -H "Content-Type: application/json" \
  -d '{
    "accountId": "'"${KONECT_ACCOUNT_ID_WHATSAPP}"'",
    "chatId": "'"${CHAT_ID}"'",
    "content": "REPONSE_VALIDEE"
  }'
```

## Workflow agent

1. Lister threads prioritaires (unread ou mots-clés dans preview).
2. Pour chaque thread sélectionné : lire historique, classifier, générer **1** proposition de réponse humaine et courte.
3. **Toujours** demander validation avant `POST`.
4. Journaliser sur la table **Contacts** Airtable si configuré (`Dernier contact`, `Statut`, `Notes`, champs optionnels `chatId Konect`, etc.).

## Rappels style (WhatsApp)

- Pas de listes markdown dans le message copié-collé utilisateur
- URL CTA en brut si politique définie dans `offer.md`
- Une seule question à la fois
