---
description: Agent DMs Instagram — contexte résolu, prompt Instagram (ultra court), classification, envoi après validation.
---
# Instagram Agent — Répondeur DMs

Même architecture que LinkedIn/WhatsApp Agent avec `platform=instagram` et le prompt **Instagram** de `memory/operational/agent-prompts.md`.

## Quand activer

- « DMs Instagram », « répondre sur Instagram », `/instagram-agent`

## Prérequis

1. `memory/operational/config.md` — `KONECT_ACCOUNT_ID_INSTAGRAM`
2. `memory/operational/agent-prompts.md` — section **Instagram**
3. `memory/identity/offer.md` — CTA, règles prix
4. `memory/identity/brand.md` — ton IG

---

## Étape 1 — Lister (sans ouvrir)

```bash
curl -s "${KONECT_BASE_URL}/conversations?accountId=${KONECT_ACCOUNT_ID_INSTAGRAM}&platform=instagram&limit=50" \
  -H "Authorization: Bearer ${KONECT_API_KEY}"
```

Afficher tableau, demander à l'utilisateur quels fils traiter avant de charger.

---

## Étape 2 — Charger les fils sélectionnés

⚠️ Marque comme lu. Appeler uniquement sur les fils validés.

```bash
curl -s "${KONECT_BASE_URL}/conversations/${CHAT_ID}?limit=100" \
  -H "Authorization: Bearer ${KONECT_API_KEY}"
```

Si 404 : retenter avec `?accountId=${KONECT_ACCOUNT_ID_INSTAGRAM}`.

---

## Étape 3 — Construire le CONTEXTE RÉSOLU

```
[CONTEXTE_RÉSOLU — Instagram]
agentName : [config.md]
companyName : [persona.md / config.md]
offerDescription : [copie exacte offer.md]
goalLink : [copie exacte offer.md]
règles_prix : [copie exacte offer.md]
dernier_message_inbound : "[content du dernier inbound]"
date_dernier_inbound : [received_at]
historique_résumé : [1–2 phrases]
```

---

## Étape 4 — Classifier et générer

```
Classification : [curiosité / intérêt / low-intent / question / hors-sujet]
Langue : [fr / en / …]
```

**Règles génération (Instagram) :**

- Ultra concis : 1–2 phrases, style DM naturel
- Une seule question
- Pas de markdown, pas de listes
- Basé sur le `dernier_message_inbound` — répondre à ce qui a été dit
- Ne pas inventer de contexte sur le profil

---

## Étape 5 — Validation & envoi (réponse)

```bash
curl -s -X POST "${KONECT_BASE_URL}/messages" \
  -H "Authorization: Bearer ${KONECT_API_KEY}" \
  -H "Content-Type: application/json" \
  -d '{
    "accountId": "'"${KONECT_ACCOUNT_ID_INSTAGRAM}"'",
    "chatId": "'"${CHAT_ID}"'",
    "content": "REPONSE_VALIDEE"
  }'
```

## Envoi — Premier message (nouveau fil)

```bash
curl -s -X POST "${KONECT_BASE_URL}/messages" \
  -H "Authorization: Bearer ${KONECT_API_KEY}" \
  -H "Content-Type: application/json" \
  -d '{
    "accountId": "'"${KONECT_ACCOUNT_ID_INSTAGRAM}"'",
    "to": "IDENTIFIANT_IG",
    "content": "MESSAGE_VALIDE"
  }'
```

---

## Étape 6 — Suivi queue

```bash
curl -s "${KONECT_BASE_URL}/queue/${QUEUE_ID}" \
  -H "Authorization: Bearer ${KONECT_API_KEY}"
```

Si `failed` : lire `error` → compte déconnecté, fenêtre d'envoi, contenu refusé.

---

## Étape 7 — CRM

MAJ **Contacts** Airtable uniquement (`Dernier contact`, `Statut`, `Notes`, champs optionnels).

---

## Limites

- Semi-auto : validation avant batch > 5.
- Toujours construire le CONTEXTE_RÉSOLU avant de générer.
