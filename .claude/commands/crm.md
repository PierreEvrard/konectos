---
description: Consulter et mettre à jour le CRM Airtable (tables Contacts et Contenus uniquement).
---
# CRM — Airtable

Opérations **lecture / filtre / mise à jour** sur la base Airtable **KonnectOS**.

## Quand activer

- « CRM », « mes contacts », « airtable », « statut lead »

## Prérequis

- `AIRTABLE_API_KEY`, `AIRTABLE_BASE_ID` dans `.env`
- IDs des tables dans `memory/operational/config.md`

## API Airtable (rappel)

```bash
curl -s "https://api.airtable.com/v0/${AIRTABLE_BASE_ID}/${TABLE_ID}?pageSize=100" \
  -H "Authorization: Bearer ${AIRTABLE_API_KEY}"
```

Filtre : param `filterByFormula` (encoder l’URL).

Mise à jour (max 10 records par PATCH) :

```bash
curl -s -X PATCH "https://api.airtable.com/v0/${AIRTABLE_BASE_ID}/${TABLE_ID}" \
  -H "Authorization: Bearer ${AIRTABLE_API_KEY}" \
  -H "Content-Type: application/json" \
  -d '{"records": [{"id": "recXXX", "fields": {"Statut": "Contacté"}}]}'
```

## Workflow

1. Comprendre la demande (table **Contacts** ou **Contenus** : liste, recherche, MAJ statut, champs optionnels type `chatId Konect` sur un contact).
2. Construire la requête ; paginer avec `offset`.
3. Présenter les résultats de façon lisible.
4. Pour écritures : confirmer les **valeurs exactes** des singleSelect.
