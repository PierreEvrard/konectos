---
description: Générer des messages d'approche personnalisés (LinkedIn ou Instagram) — contexte résolu, few-shots templates.md, validation avant envoi Konect.
---
# Icebreaker — Messages d'approche

Produit des **premiers messages** personnalisés ; envoi via **`POST /messages`** avec `to` (nouvelle conversation).

## Quand activer

- « icebreaker », « message d'approche », « premier message »

## Prérequis (lecture obligatoire)

1. `memory/identity/persona.md` — ICP, ton
2. `memory/identity/offer.md` — offre, `goalLink`, règles prix
3. `memory/identity/brand.md` — style
4. `memory/operational/templates.md` — few-shots par canal
5. `memory/operational/agent-prompts.md` — section « Premier message » du canal choisi
6. `memory/operational/config.md` — bons `accountId`

---

## Étape 1 — Construire le CONTEXTE RÉSOLU

Avant de générer, assembler pour chaque cible :

```
[CONTEXTE_RÉSOLU — Icebreaker]
canal : [LinkedIn / Instagram / WhatsApp]
prospect_name : [prénom ou nom de l'entreprise]
context_note : [raison du contact — a commenté, a liké, profil pertinent, absent au live, etc.]
goalLink : [copie exacte depuis offer.md — si doit figurer dans le message]
règle_lien_premier_message : [oui / non — depuis offer.md]
ton : [copie depuis brand.md]
```

---

## Étape 2 — Générer les messages

Pour chaque cible, s'appuyer sur les **few-shots de `templates.md`** (canal correspondant) :

- **Imiter** le rythme et la longueur des exemples — ne pas copier-coller
- Ancrer sur `context_note` réel — aucune information inventée
- Respecter les règles du canal :
  - LinkedIn : pas de lien en premier message sauf `offer.md` autorise
  - WhatsApp : 2–3 blocs, une question, URL brute si incluse
  - Instagram : 1–2 phrases, ultra concis
- Utiliser `{prospect_name}` **une seule fois**

---

## Étape 3 — Tableau de prévisualisation

Avant envoi, présenter sous forme de tableau :

```
| # | Nom | Canal | Message proposé | Contexte |
|---|-----|-------|-----------------|---------|
| 1 | … | LI | "…" | a commenté post X |
| 2 | … | WA | "…" | absent live |
```

**Pause validation** si > 5 envois.

---

## Étape 4 — Envoi

**LinkedIn (nouveau fil)**

```bash
curl -s -X POST "${KONECT_BASE_URL}/messages" \
  -H "Authorization: Bearer ${KONECT_API_KEY}" \
  -H "Content-Type: application/json" \
  -d '{
    "accountId": "'"${KONECT_ACCOUNT_ID_LINKEDIN}"'",
    "to": "https://www.linkedin.com/in/USERNAME",
    "content": "MESSAGE_VALIDE"
  }'
```

**Instagram** : même schéma avec `KONECT_ACCOUNT_ID_INSTAGRAM` et l'identifiant `to` IG.

**WhatsApp** : même schéma avec `KONECT_ACCOUNT_ID_WHATSAPP`.

---

## Étape 5 — Suivi queue

Après chaque envoi, noter `queueId`. En cas de doute sur l'état :

```bash
curl -s "${KONECT_BASE_URL}/queue/${QUEUE_ID}" \
  -H "Authorization: Bearer ${KONECT_API_KEY}"
```

Si `failed` : lire `error` (fenêtre d'envoi, compte déconnecté, contenu refusé).

---

## Étape 6 — CRM (obligatoire — pas optionnel)

**Avant** l’envoi (Étape 4), vérifier que chaque cible existe bien dans
Airtable `Contacts`. Si absente → créer la ligne (`Statut = "New"`) avant
d’envoyer. Dédupliquer par URL / handle.

**Après** chaque envoi (chaque `queueId` renvoyé par Konect), mettre à
jour la ligne correspondante :
- `Statut` → « Contacté »
- `Dernier contact` → horodatage d’envoi (ISO)
- `Dernier message` → copie tronquée du message envoyé
- `Icebreaker` → texte complet si 1er contact
- `chatId Konect` → dès que disponible (après livraison)
- `Plateforme chat` → `linkedin` / `whatsapp` / `instagram`

Ne **jamais** envoyer sans avoir préparé la mise à jour CRM : c’est ce
qui permet à `/followup`, `/linkedin-agent` et `/report` de travailler
ensuite sur des données fiables.
