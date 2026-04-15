---
description: Agent DMs LinkedIn — lit les conversations Konect, applique agent-prompts.md, propose des réponses, envoie après validation (chatId).
---
# LinkedIn Agent — Répondeur DMs

Traite les **messages LinkedIn** avec le **system prompt** configuré dans `memory/operational/agent-prompts.md` + variables `config.md` / `offer.md`.

## Quand activer

- `/linkedin-agent`, « répondre sur LinkedIn », « DMs LinkedIn », « messages non lus »

## Prérequis (lecture obligatoire)

1. `memory/operational/config.md`
2. `memory/operational/agent-prompts.md` — section **LinkedIn**
3. `memory/identity/offer.md`, `memory/identity/persona.md`

## Étape 1 — Conversations (API Konect, pas Airtable)

```bash
curl -s "${KONECT_BASE_URL}/conversations?accountId=${KONECT_ACCOUNT_ID_LINKEDIN}&platform=linkedin&limit=50" \
  -H "Authorization: Bearer ${KONECT_API_KEY}"
```

Prioriser `unread_count > 0` si le champ est présent.

## Étape 2 — Messages d’une conversation

```bash
curl -s "${KONECT_BASE_URL}/conversations/${CHAT_ID}?limit=100" \
  -H "Authorization: Bearer ${KONECT_API_KEY}"
```

⚠️ Cet appel **marque la conversation comme lue**.

## Étape 3 — Génération de réponse

- Résoudre `{{agentName}}`, `{{companyName}}`, `{{offerDescription}}`, `{{goalLink}}`, etc.
- Classifier : vœux simples / question offre / fort intérêt / bas intent / spam
- Respecter **anti-hallucination** et règles prix de `offer.md`
- Style : concis, pas de markdown dans le message final

## Étape 4 — Validation & envoi

Proposer le texte ; après OK :

```bash
curl -s -X POST "${KONECT_BASE_URL}/messages" \
  -H "Authorization: Bearer ${KONECT_API_KEY}" \
  -H "Content-Type: application/json" \
  -d '{
    "accountId": "'"${KONECT_ACCOUNT_ID_LINKEDIN}"'",
    "chatId": "'"${CHAT_ID}"'",
    "content": "REPONSE_VALIDEE"
  }'
```

Réponse typique **202** avec file d’attente — noter `queueId` / `estimatedAt`.

## Étape 5 — CRM

Mettre à jour uniquement la table **Contacts** dans Airtable : `Dernier contact`, `Statut`, `Notes`, et si la base inclut les champs optionnels : `chatId Konect`, `Plateforme chat`, `Dernier aperçu message`, `Unread`.

## Limites

- Semi-auto : ne pas enchaîner > 5 envois sans validation explicite utilisateur.
