---
description: Scorer et prioriser les leads selon l’ICP (tiers) — lecture CRM + règles persona.md.
---
# Score — Priorisation ICP

Attribue un **score / tier** aux contacts selon `memory/identity/persona.md` et met à jour Airtable.

## Quand activer

- « scorer », « prioriser », « qui contacter en premier »

## Prérequis

- Table **Contacts** + champs score/statut configurés
- ICP à jour (`/icp`)

## Workflow

1. Récupérer les contacts (Airtable `GET .../Contacts` avec pagination `offset`).
2. Appliquer des règles explicites (titres, secteur, signaux dans Notes, etc.) → Tier 1 / 2 / 3.
3. Prévisualiser le tableau de scoring pour validation.
4. Après validation : `PATCH` Airtable par lots de ≤ 10 records.
5. Suite : `/icebreaker` ou `/invite` selon stratégie.

## Note

Le scoring est **heuristique** côté Claude ; documenter les critères utilisés dans les **Notes** ou dans `memory/knowledge/lessons.md` via `/memory-save`.
