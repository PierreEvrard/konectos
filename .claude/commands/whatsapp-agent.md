---
description: Agent conversations WhatsApp — construit le contexte résolu, applique le prompt WhatsApp (qualification), propose réponses courtes classifiées, envoie après validation.
---
# WhatsApp Agent — Répondeur

Traite les **DMs WhatsApp** avec le **bloc WhatsApp** de `memory/operational/agent-prompts.md` : qualification Situation → Objectif → Contexte, messages courts et naturels, gestion du lien CTA.

## Quand activer

- « WhatsApp », « répondre sur WhatsApp », `/whatsapp-agent`

## Prérequis (lecture obligatoire)

1. `memory/operational/config.md` — `KONECT_ACCOUNT_ID_WHATSAPP`
2. `memory/operational/agent-prompts.md` — section **WhatsApp** (system prompt + premier message + relance)
3. `memory/identity/offer.md` — CTA, règles prix, `goalLink`
4. `memory/identity/persona.md` — ton

---

## Étape 1 — Lister les conversations (sans les ouvrir)

```bash
curl -s "${KONECT_BASE_URL}/conversations?accountId=${KONECT_ACCOUNT_ID_WHATSAPP}&platform=whatsapp&limit=50" \
  -H "Authorization: Bearer ${KONECT_API_KEY}"
```

Afficher le tableau (nom, aperçu, unread). **Demander à l'utilisateur quels fils traiter** avant de charger les messages.

---

## Étape 2 — Charger les messages sélectionnés

⚠️ Marque la conversation comme lue. N'appeler que sur les fils validés.

```bash
curl -s "${KONECT_BASE_URL}/conversations/${CHAT_ID}?limit=100" \
  -H "Authorization: Bearer ${KONECT_API_KEY}"
```

Si 404 : retenter avec `?accountId=${KONECT_ACCOUNT_ID_WHATSAPP}`.

---

## Étape 3 — Construire le CONTEXTE RÉSOLU

```
[CONTEXTE_RÉSOLU — WhatsApp]
agentName : [valeur config.md]
companyName : [valeur persona.md / config.md]
offerDescription : [copie exacte offer.md]
goalLink : [copie exacte offer.md]
goalType : [copie exacte offer.md]
règles_prix : [copie exacte offer.md]
dernier_message_inbound : "[content du dernier message direction=inbound]"
date_dernier_inbound : [received_at]
étape_qualification : [Situation / Objectif / Contexte / Lien envoyé / Clôture — déduire de l'historique]
historique_résumé : [2–3 phrases sur l'échange]
```

Seul ce bloc est utilisé pour générer la réponse. Aucune information extérieure.

---

## Étape 4 — Classifier et générer

Indiquer en une ligne avant la proposition :

```
Classification : [curiosité / qualification-en-cours / fort-intérêt / low-intent / hors-sujet / after-link]
Étape qualification : [Situation / Objectif / Contexte / Lien envoyé / Clôture]
Langue : [fr / en / …]
```

Puis rédiger la réponse selon le **system prompt WhatsApp** de `agent-prompts.md`.

**Règles de génération (WhatsApp) :**

- Cible : ~25 mots, max 2 phrases courtes
- Une seule question à la fois
- Pas de listes à puces, pas de markdown
- Pas de tirets longs ni points de suspension
- Référencer le `dernier_message_inbound` — répondre à ce qui a été dit
- Pas de validations mécaniques répétées (éviter « Super ! », « D'accord ! »)
- URL CTA (`goalLink`) en brut, jamais en markdown
- Politique prix : strictement `offer.md`

**Décisions automatiques :**

| Situation | Action |
|-----------|--------|
| Fort intérêt + projet actif | Fast track : envoyer `goalLink` |
| Curieux sans projet | Répondre poliment, ne pas pousser |
| Boucle sans intention après 3 échanges | Rediriger `goalLink` une fois, puis clôture |
| Question de prix | Renvoi `goalLink` per `offer.md` |

---

## Étape 5 — Validation & envoi

Attendre OK explicite. Après validation :

```bash
curl -s -X POST "${KONECT_BASE_URL}/messages" \
  -H "Authorization: Bearer ${KONECT_API_KEY}" \
  -H "Content-Type: application/json" \
  -d '{
    "accountId": "'"${KONECT_ACCOUNT_ID_WHATSAPP}"'",
    "chatId": "'"${CHAT_ID}"'",
    "content": "REPONSE_VALIDEE"
  }'
```

Extraire `queueId` + `estimatedAt`.

---

## Étape 6 — Suivi queue

```bash
curl -s "${KONECT_BASE_URL}/queue/${QUEUE_ID}" \
  -H "Authorization: Bearer ${KONECT_API_KEY}"
```

Si `failed` : lire `error` → fenêtre d'envoi (→ `/settings`), contenu refusé, compte déconnecté.

---

## Étape 7 — CRM

MAJ **Contacts** Airtable : `Dernier contact`, `Statut`, `Notes`, champs optionnels `chatId Konect`, `Plateforme chat`.

---

## Limites

- Semi-auto : validation avant tout batch > 5.
- Ne jamais générer sans CONTEXTE_RÉSOLU complet.
