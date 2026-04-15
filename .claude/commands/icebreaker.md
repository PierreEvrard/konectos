---
description: Générer des messages d’approche (LinkedIn ou Instagram) à partir de templates et de l’ICP — validation avant envoi Konect.
---
# Icebreaker — Messages d’approche

Produit des **premiers messages** personnalisés ; l’envoi se fait via **`POST /messages`** avec `to` (nouvelle conversation).

## Quand activer

- « icebreaker », « message d’approche », « premier message »

## Prérequis

Lire :

- `memory/identity/persona.md`, `memory/identity/offer.md`, `memory/identity/brand.md`
- `memory/operational/templates.md`
- `memory/operational/agent-prompts.md` (section « premier message » du canal choisi)
- `memory/operational/config.md` — bons `accountId`

## Envoi (après validation utilisateur)

**LinkedIn (nouveau fil)**

```bash
curl -s -X POST "${KONECT_BASE_URL}/messages" \
  -H "Authorization: Bearer ${KONECT_API_KEY}" \
  -H "Content-Type: application/json" \
  -d '{
    "accountId": "'"${KONECT_ACCOUNT_ID_LINKEDIN}"'",
    "to": "https://www.linkedin.com/in/USERNAME",
    "content": "MESSAGE_VALIDE"
  }'
```

**Instagram** : même schéma avec `KONECT_ACCOUNT_ID_INSTAGRAM` et identifiant `to` accepté par Konect pour IG.

## Workflow

1. Liste de cibles + contexte (`{context_note}`).
2. Générer 1 message par cible (ton brand + règles agent).
3. **Pause validation** si > 5 envois.
4. Après envoi : noter `queueId` ; MAJ Airtable **Contacts** + **Conversations** si `chat_id` renvoyé dans les flux suivants.
5. Suite : `/followup` ou `/linkedin-agent`.
