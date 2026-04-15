---
description: Concevoir des séquences multi-touch cross-plateforme (LinkedIn → WhatsApp → Instagram) et les documenter.
---
# Séquences multi-touch

Conçoit des enchaînements **LI → WA → IG** (ou sous-ensembles) et les enregistre dans **`memory/operational/sequences.md`** + recommandations d’enregistrement Airtable (table **Séquences**).

## Quand activer

- « séquence », « relances sur plusieurs canaux », « après LinkedIn WhatsApp »

## Prérequis

- `memory/identity/persona.md`, `memory/identity/offer.md`
- `memory/operational/config.md` (account IDs)

## Workflow

1. Clarifier le **déclencheur** (ex. nouveau lead LinkedIn, formulaire rempli, numéro obtenu).
2. Lister les **étapes** avec délai (J0, J+2, …), **plateforme**, **type d’action Konect** :
   - invitation / message `to` / réponse `chatId` / post / commentaire / réaction
3. Règles **semi-auto** : points de validation utilisateur avant envois massifs.
4. Écrire la séquence dans `memory/operational/sequences.md` (format du fichier).
5. Si Airtable prêt : schéma des champs à remplir pour chaque contact en séquence.

## Rappels API

- Nouveau fil : `POST /messages` avec `to`
- Réponse : `chatId` + `content`
- Posts planifiés : `POST /posts` + `scheduledAt`
