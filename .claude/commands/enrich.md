---
description: Enrichir un profil ou une entreprise LinkedIn via GET /profiles et GET /linkedin/companies.
---
# Enrich — Profils & entreprises

Enrichit les fiches leads avec Konect + met à jour Airtable si demandé.

## Quand activer

- « enrichir », « compléter le profil », « infos entreprise »

## Prérequis

- Account ID LinkedIn
- Identifiant profil : public ID LinkedIn ou identifiant provider retourné par la recherche

## Profil personne

```bash
curl -s "${KONECT_BASE_URL}/profiles/IDENTIFIANT?accountId=${KONECT_ACCOUNT_ID_LINKEDIN}" \
  -H "Authorization: Bearer ${KONECT_API_KEY}"
```

## Mon profil (référence ton)

```bash
curl -s "${KONECT_BASE_URL}/profiles/me?accountId=${KONECT_ACCOUNT_ID_LINKEDIN}" \
  -H "Authorization: Bearer ${KONECT_API_KEY}"
```

## Entreprise LinkedIn

```bash
curl -s "${KONECT_BASE_URL}/linkedin/companies/COMPANY_ID?accountId=${KONECT_ACCOUNT_ID_LINKEDIN}" \
  -H "Authorization: Bearer ${KONECT_API_KEY}"
```

## Workflow

1. Liste d’URL / IDs à traiter (batch modéré).
2. Appels profil (+ entreprise si pertinent).
3. Synthèse structurée pour l’utilisateur ; option PATCH Airtable **Contacts**.
4. Suite : `/score` ou `/icebreaker`.
