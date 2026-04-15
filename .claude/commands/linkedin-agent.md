---
description: Agent DMs LinkedIn — lit les conversations Konect, construit un contexte résolu, propose des réponses classifiées, envoie après validation (chatId), suit la queue.
---
# LinkedIn Agent — Répondeur DMs

Traite les **messages LinkedIn** avec le **system prompt** configuré dans `memory/operational/agent-prompts.md`.

## Quand activer

- `/linkedin-agent`, « répondre sur LinkedIn », « DMs LinkedIn », « messages non lus »

## Prérequis (lecture obligatoire avant toute génération)

1. `memory/operational/config.md` — account ID + table IDs
2. `memory/operational/agent-prompts.md` — section **LinkedIn** (system prompt + premier message + relance)
3. `memory/identity/offer.md` — offre, CTA, règles prix
4. `memory/identity/persona.md` — ton, profil, ICP

---

## Étape 1 — Lister les conversations (sans les ouvrir)

```bash
curl -s "${KONECT_BASE_URL}/conversations?accountId=${KONECT_ACCOUNT_ID_LINKEDIN}&platform=linkedin&limit=50" \
  -H "Authorization: Bearer ${KONECT_API_KEY}"
```

Afficher un tableau **avant d'ouvrir** quoi que ce soit :

```
| # | Nom              | Aperçu                    | Non lus |
|---|------------------|---------------------------|---------|
| 1 | [participant_name] | [last_message_preview…]  | [unread_count] |
| 2 | …                | …                         | … |
```

Demander à l'utilisateur : **lesquelles traiter** (numéros ou « toutes les non lues »). Ne pas charger le détail avant cette validation.

---

## Étape 2 — Charger les messages sélectionnés

⚠️ `GET /conversations/{chatId}` **marque la conversation comme lue**. Ne l'appeler que sur les fils validés.

```bash
curl -s "${KONECT_BASE_URL}/conversations/${CHAT_ID}?limit=100" \
  -H "Authorization: Bearer ${KONECT_API_KEY}"
```

Si erreur 404 : retenter avec `?accountId=${KONECT_ACCOUNT_ID_LINKEDIN}` — certains déploiements l'exigent.

---

## Étape 3 — Construire le CONTEXTE RÉSOLU

Avant toute génération, assembler un bloc interne (non envoyé à l'utilisateur) :

```
[CONTEXTE_RÉSOLU — LinkedIn]
agentName : [valeur depuis config.md]
companyName : [valeur depuis persona.md / config.md]
offerDescription : [copie exacte depuis offer.md]
goalLink : [copie exacte depuis offer.md]
goalType : [copie exacte depuis offer.md]
règles_prix : [copie exacte depuis offer.md]
dernier_message_inbound : "[content du dernier message direction=inbound]"
date_dernier_inbound : [received_at]
historique_résumé : [2–3 phrases qui résument les échanges précédents]
```

Ce bloc est la **seule base** pour la génération. Aucune information ne doit être inventée hors de ce contexte.

---

## Étape 4 — Classifier et générer

Pour chaque conversation, indiquer en une ligne avant la proposition :

```
Classification : [vœux / question-offre / fort-intérêt / bas-intent / relance / spam / autre]
Langue détectée : [fr / en / es / …]
```

Puis proposer la réponse selon le **system prompt LinkedIn** de `agent-prompts.md`.

**Règles de génération (LinkedIn) :**

- 2–4 phrases maximum, pas de listes à puces dans le message
- Ton professionnel mais humain, naturel
- Référencer le `dernier_message_inbound` résumé — ne jamais ignorer ce que le prospect a dit
- Pas de markdown dans le texte final envoyé (pas de `**`, `_`, `-`)
- Pas de tirets longs ni points de suspension
- Ne jamais inventer d'informations hors du CONTEXTE_RÉSOLU
- Politique prix : strictement celle de `offer.md`

**Tableau de classification :**

| Catégorie | Action |
|-----------|--------|
| vœux / remerciement | Réponse courte chaleureuse, pas de qualification |
| question-offre | Répondre globalement, qualification progressive |
| fort-intérêt | Fast track : lien `goalLink` si politique définie |
| bas-intent | Réponse polie, ne pas pousser |
| relance | Vérifier si on a déjà envoyé le lien, adapter |
| spam / hors sujet | Proposer de ne pas répondre |

---

## Étape 5 — Validation & envoi

Proposer le texte + classification. Attendre OK explicite. Après validation :

```bash
curl -s -X POST "${KONECT_BASE_URL}/messages" \
  -H "Authorization: Bearer ${KONECT_API_KEY}" \
  -H "Content-Type: application/json" \
  -d '{
    "accountId": "'"${KONECT_ACCOUNT_ID_LINKEDIN}"'",
    "chatId": "'"${CHAT_ID}"'",
    "content": "REPONSE_VALIDEE"
  }'
```

Réponse attendue **202** → extraire `queueId` et `estimatedAt`.

---

## Étape 6 — Suivi de la file (Konect queue)

Après envoi, vérifier le statut si besoin :

```bash
curl -s "${KONECT_BASE_URL}/queue/${QUEUE_ID}" \
  -H "Authorization: Bearer ${KONECT_API_KEY}"
```

Cycle : `queued` → `processing` → `completed` / `failed`.

Si `failed` : lire le champ `error` — causes fréquentes : contenu refusé, fenêtre d'envoi fermée (→ `/settings`), compte déconnecté (→ reconnecter).

Pour annuler avant traitement (statut `queued` uniquement) :

```bash
curl -s -X DELETE "${KONECT_BASE_URL}/queue/${QUEUE_ID}" \
  -H "Authorization: Bearer ${KONECT_API_KEY}"
```

---

## Étape 7 — CRM

Mettre à jour **Contacts** Airtable : `Dernier contact`, `Statut`, `Notes` + champs optionnels `chatId Konect`, `Plateforme chat`, `Dernier aperçu message`.

---

## Limites

- Semi-auto : ne pas enchaîner > 5 envois sans validation explicite.
- Ne jamais générer une réponse sans le CONTEXTE_RÉSOLU complet.
