---
description: Consulter et mettre à jour le CRM Airtable (tables Contacts et Contenus uniquement).
---
# CRM — Airtable

Opérations **lecture / filtre / mise à jour** sur la base Airtable
**KonectOS**. Le CRM est la **source unique de vérité** pour chaque lead
(cf. Operating Rule 4 dans `CLAUDE.md`) : aucune action sortante sans
dédupe CRM préalable et sans mise à jour CRM après coup.

## Boucle CRM-sync (rappel)

1. **Scrape / recherche** (`/prospect`, `list_followers`, etc.) → dédupe
   contre `Contacts` → insérer les nouveaux en `Statut = "New"`, en
   remplissant **toujours** : `ID` (provider ID Konect du lead),
   `Localisation` (ville), `Relation` (degré LinkedIn 1 / 2 / 3 / 3+
   si dispo), `Source` (ex. `LinkedIn Search`, `LinkedIn Sales
   Navigator`, `LinkedIn Followers`, `Instagram Search`, …).
2. **Avant envoi** (`/icebreaker`, `/invite`, `/followup`) → s’assurer
   que la cible est dans `Contacts`. Si non : créer la ligne d’abord.
3. **Après envoi** → mettre à jour `Statut` (`Contacté` / `Répondu` / …),
   `Dernier contact` (ISO), `Dernier message` (copie tronquée),
   `Icebreaker` (si 1er contact), `chatId Konect`, `Plateforme chat`.
4. **Lecture inbox** (`/linkedin-agent`, `/whatsapp-agent`,
   `/instagram-agent`) → pour chaque message inbound, retrouver la
   ligne `Contacts` (par `chatId Konect` ou URL) et passer à
   `Statut = "Répondu"`, ajouter un extrait aux `Notes`.

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

1. Comprendre la demande (table **Contacts** ou **Contenus** : liste, recherche, MAJ statut, champs optionnels type `chatId Konect`, `ID`, `Localisation`, `Relation`, `Source` sur un contact).
2. Construire la requête ; paginer avec `offset`.
3. Présenter les résultats de façon lisible.
4. Pour écritures : confirmer les **valeurs exactes** des singleSelect.
