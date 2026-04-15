---
description: Rapport d’activité — agrège GET /usage, stats queue (failed/completed), et CRM pour une période définie.
---
# Report — Bilan d’activité

Rapport **lecture seule** ou avec recommandations, sur une période (défaut : 30 derniers jours).

## Quand activer

- « rapport », « bilan », « stats », « performance Konect »

## Données Konect

1. `GET /usage?since=&until=`
2. `GET /queue?status=failed&limit=50` — analyser messages d’erreur
3. `GET /queue?status=completed&limit=50` — optionnel pour volumétrie récente

## Données CRM (si configuré)

- Nouveaux contacts, statuts « Répondu / RDV / Client » sur la période (Airtable ; filtrer par `Dernier contact` ou champ date équivalent).

## Livrable

Sections recommandées :

1. **Résumé exécutif** (5 bullets)
2. **Volumes par plateforme / type d’action** (depuis `usage`)
3. **Erreurs file** (patterns `failed`)
4. **Pipeline CRM**
5. **Recommandations** 3 actions prioritaires (ex. `/settings`, `/followup`, `/memory-save`)

## Workflow

1. Demander la période si non précisée.
2. Collecter les données (curl).
3. Produire le rapport en français, ton clair.
4. Proposer `/weekly` si fin de semaine.
