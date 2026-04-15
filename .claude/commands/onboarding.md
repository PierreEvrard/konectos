---
description: Wizard KonectOS — clé Konect, comptes, Airtable, persona, offre, prompts agents et tables CRM.
---
# Onboarding — Setup KonectOS

Configure KonectOS de zéro à « prêt à automatiser » (Konect + Airtable + mémoire + prompts).

## Quand activer

- Première utilisation
- Fichiers `memory/` avec `[À CONFIGURER]`
- L’utilisateur demande « setup », « configuration », « onboarding »

## Prérequis

Lire `memory/brain.md`. Reprendre une phase déjà faite si partiellement complétée.

---

## Phase 1 — Konect (connexion & IDs)

### 1.0 Vérifier l'abonnement Konect

KonectOS utilise [mykonect.ai](https://mykonect.ai) — **20 €/mois par compte social connecté** (LinkedIn, WhatsApp, Instagram = 3 comptes distincts = 60 €/mois max).

Si l'utilisateur n'a pas encore de compte : l'inviter à s'inscrire sur [mykonect.ai](https://mykonect.ai), connecter les plateformes souhaitées, puis générer une clé API depuis le dashboard avant de continuer.

### 1.1 Vérifier la clé et la base URL

Demander à l’utilisateur de remplir `.env` :

- `KONECT_API_KEY`
- `KONECT_BASE_URL` (défaut `https://mykonect.ai/api/v1` si son instance utilise ce host)

### 1.2 Lister les comptes

```bash
curl -s "${KONECT_BASE_URL}/accounts" \
  -H "Authorization: Bearer ${KONECT_API_KEY}"
```

Pour chaque entrée : `id`, `platform`, `status`, `warmup_level` (si présent).

- **LinkedIn** → noter UUID dans `KONECT_ACCOUNT_ID_LINKEDIN` + `memory/operational/config.md`
- **WhatsApp** → idem
- **Instagram** → idem

Si `status != connected` : guider vers le dashboard Konect + `POST /accounts/connect` ou `PATCH /accounts/{id}` selon doc.

### 1.3 Fenêtre d’envoi (recommandé)

Proposer de configurer tout de suite via `/settings` ou `PATCH /accounts/{id}/settings` (timezone, `send_start_hour`, `send_end_hour`, `send_on_weekends`, `inbox_sync_enabled`).

---

## Phase 2 — Airtable

1. Créer une base nommée **KonectOS** (nom par défaut — peut être personnalisé mais garder ce nom pour cohérence).
2. Créer un token avec scopes records read/write + schema bases read.
3. Renseigner `AIRTABLE_API_KEY`, `AIRTABLE_BASE_ID` dans `.env`.

### Tables à créer (2 tables — champs minimum)

KonectOS n’utilise **que** deux tables Airtable : **Contacts** et **Contenus** (pas de tables Conversations ni Séquences : le suivi de fil et des séquences se fait sur **Contacts** + fichier `memory/operational/sequences.md`).

**Contacts** (primary `Nom`)  
`Nom`, `Entreprise`, `Titre`, `LinkedIn URL` (url), `Instagram` (text), `WhatsApp` (phone/text), `Plateforme source` (singleSelect), `Statut` (singleSelect), `Score ICP` (singleSelect ou number), `Notes` (long), `Dernier contact` (date)

Champs **optionnels** recommandés pour lier Konect sans table Conversations :  
`chatId Konect` (text) — identifiant de conversation Konect pour la dernière interaction ; `Plateforme chat` (singleSelect : LinkedIn / WhatsApp / Instagram) ; `Dernier aperçu message` (text) ; `Unread` (number) si tu synchronises manuellement ou via script.

**Contenus**  
`Titre`, `Plateforme`, `Type`, `Statut`, `Texte` (long), `Date publication` (date), `scheduledAt` (text ou date)

Après création : `GET https://api.airtable.com/v0/meta/bases/{baseId}/tables` et noter les **table IDs** (Contacts + Contenus) dans `memory/operational/config.md`.

---

## Phase 3 — Identité & offre

Mettre à jour (questions guidées) :

- `memory/identity/persona.md` — profil + ICP
- `memory/identity/brand.md` — ton
- `memory/identity/offer.md` — offre, **CTA** (`goalLink`), règles prix DM

---

## Phase 4 — Prompts agents

Renseigner `memory/operational/config.md` :

- `agentName`, `companyName`, `language`

Compléter `memory/operational/agent-prompts.md` :

- Sections WhatsApp / LinkedIn / Instagram (system + premier message + relance) en adaptant les défauts au métier de l’utilisateur.

---

## Phase 5 — Templates & brain

- `memory/operational/templates.md` — 1–2 icebreakers validés
- `memory/brain.md` — cocher setup, résumer contexte 7 jours

---

## Fin

Afficher un récapitulatif : comptes OK, base Airtable, prochaine commande suggérée (`/brain-status` puis `/prospect` ou `/linkedin-agent`).
