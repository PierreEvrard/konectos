---
description: Invitations LinkedIn (ou follow Instagram) en batch via POST /relations/invite — profileId obligatoire (via /enrich ou recherche), validation obligatoire.
---
# Invite — Invitations & follows

Envoie des **demandes de connexion LinkedIn** ou **follow Instagram** via la file Konect.

## Quand activer

- « invitation LinkedIn », « connect request », « follow Instagram »

## Prérequis

- Compte `connected` pour la plateforme cible
- **`profileId`** (identifiant provider Konect) pour chaque cible

> **Important :** `profileId` ≠ URL LinkedIn. C'est l'identifiant retourné par `GET /profiles/{identifier}` ou par `POST /linkedin/search`. Si tu ne l'as pas encore, lancer **`/enrich`** sur chaque cible avant d'utiliser cette commande.

---

## Obtenir le profileId (si manquant)

Via la recherche :

```bash
curl -s -X POST "${KONECT_BASE_URL}/linkedin/search" \
  -H "Authorization: Bearer ${KONECT_API_KEY}" \
  -H "Content-Type: application/json" \
  -d '{
    "accountId": "'"${KONECT_ACCOUNT_ID_LINKEDIN}"'",
    "query": "NOM PRENOM",
    "category": "people",
    "limit": 5
  }'
```

Via le profil direct :

```bash
curl -s "${KONECT_BASE_URL}/profiles/PUBLIC_ID?accountId=${KONECT_ACCOUNT_ID_LINKEDIN}" \
  -H "Authorization: Bearer ${KONECT_API_KEY}"
```

Extraire l'identifiant provider (`id` ou équivalent dans la réponse) → c'est le `profileId` à utiliser.

---

## Appel API

```bash
curl -s -X POST "${KONECT_BASE_URL}/relations/invite" \
  -H "Authorization: Bearer ${KONECT_API_KEY}" \
  -H "Content-Type: application/json" \
  -d '{
    "accountId": "'"${KONECT_ACCOUNT_ID_LINKEDIN}"'",
    "profileId": "IDENTIFIANT_PROVIDER"
  }'
```

- **Pas de note d'invitation.** Konect ne l'expose pas : LinkedIn plafonne les
  invitations avec note à 1-2/jour sur un compte basique, contre ~21 sans note,
  et pour un taux d'acceptation équivalent. Un `message` envoyé ici est ignoré
  côté serveur. La bonne séquence : invitation nue, puis `/icebreaker` dès
  l'acceptation — le message passe alors sur le quota messages, bien plus large.
- Instagram : follow via le même endpoint (sans `message`).

---

## Workflow

1. Vérifier la marge du jour : `get_usage` → `limits[].actions.invite`.
2. **Préparer la liste** (profileId) — si `profileId` manquant sur certaines cibles : utiliser `/enrich` d'abord.
3. **Validation utilisateur** avant tout envoi > 5 invitations.
4. Envoyer par vagues ; noter `queueId` pour chaque action.
5. Suivi queue :

```bash
curl -s "${KONECT_BASE_URL}/queue/${QUEUE_ID}" \
  -H "Authorization: Bearer ${KONECT_API_KEY}"
```

Si `failed` : lire `error` — limite atteinte, fenêtre fermée, profil introuvable.

6. Annuler si besoin (`queued` uniquement) :

```bash
curl -s -X DELETE "${KONECT_BASE_URL}/queue/${QUEUE_ID}" \
  -H "Authorization: Bearer ${KONECT_API_KEY}"
```

7. MAJ **Contacts** Airtable : `Statut` → « Invité », `Dernier contact`.

## Suivi des acceptations (méthode native)

Le tool MCP `list_sent_invites` liste les invitations ENCORE EN ATTENTE de ce
compte (1 unité `scrape_comments` par page). C'est la méthode de référence,
pas la vérification du degré de relation profil par profil (1 unité `profile`
chacun, dix fois plus cher).

Détection d'une acceptation : comparer deux appels espacés. Une invitation qui
a quitté la liste a été acceptée, refusée ou retirée — la liste ne distingue
pas ces trois cas, donc confirmer par le degré de relation UNIQUEMENT pour les
profils sortis de la liste, avant de déclencher `/icebreaker`.
