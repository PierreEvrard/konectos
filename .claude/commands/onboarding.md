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

### 1.1 Récupérer la clé API Konect

Poser la question directement en conversation :

> « Quelle est ta clé API Konect ? Elle commence par `knct_` et se trouve dans ton dashboard sur [mykonect.ai](https://mykonect.ai) → Settings → API Keys. »

Une fois la clé fournie, l'écrire dans `.env` :

```
KONECT_API_KEY=knct_XXXXX
KONECT_BASE_URL=https://mykonect.ai/api/v1
```

Puis tester immédiatement :

```bash
curl -s "https://mykonect.ai/api/v1/accounts" \
  -H "Authorization: Bearer LA_CLE_FOURNIE"
```

- Réponse `"ok": true` → clé valide, continuer.
- Erreur `401` / `403` → clé invalide ou expirée. Demander de la régénérer sur [mykonect.ai](https://mykonect.ai) → Settings → API Keys.

### 1.2 Détecter et enregistrer les comptes connectés

La réponse liste les comptes. Pour chaque entrée : `id`, `platform`, `status`.

Afficher un tableau récap :

```
| Plateforme | Statut       | UUID                                 |
|------------|--------------|--------------------------------------|
| LinkedIn   | connected    | xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx |
| WhatsApp   | connected    | xxxxxxxx-...                         |
| Instagram  | disconnected | —                                    |
```

Pour chaque compte `connected`, écrire dans `.env` :

```
KONECT_ACCOUNT_ID_LINKEDIN=UUID
KONECT_ACCOUNT_ID_WHATSAPP=UUID
KONECT_ACCOUNT_ID_INSTAGRAM=UUID
```

Et noter les UUIDs dans `memory/operational/config.md` (section Account IDs).

Si un compte est `disconnected` ou absent : informer que la connexion se fait depuis [mykonect.ai](https://mykonect.ai) → Accounts → Connect. Les commandes liées à cette plateforme seront désactivées jusqu'à connexion.

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
