---
description: État KonectOS — comptes Konect, queue, usage API, mémoire, rappels d’actions.
---
# Brain status — État du système

Vue rapide : Konect, file d’attente, usage, CRM (si configuré), fichiers mémoire.

## Quand activer

- `/brain-status`, « où j’en suis », « état du projet »

## Prérequis

- `.env` avec au moins `KONECT_API_KEY` et `KONECT_BASE_URL`

## Workflow

### 1. Comptes Konect

```bash
curl -s "${KONECT_BASE_URL}/accounts" \
  -H "Authorization: Bearer ${KONECT_API_KEY}"
```

Tableau : plateforme | status | id | warmup (si dispo).

### 2. File d’attente (queued)

```bash
curl -s "${KONECT_BASE_URL}/queue?status=queued&limit=20" \
  -H "Authorization: Bearer ${KONECT_API_KEY}"
```

### 3. Usage ~30 jours

```bash
curl -s "${KONECT_BASE_URL}/usage" \
  -H "Authorization: Bearer ${KONECT_API_KEY}"
```

### 4. Mémoire

Vérifier présence de `[À CONFIGURER]` dans :

- `memory/operational/config.md`
- `memory/identity/persona.md`, `offer.md`

### 5. Synthèse utilisateur

Proposer **1 à 3** prochaines actions concrètes (ex. finir onboarding, `/settings`, `/linkedin-agent`).
