---
description: Wizard KonectOS — Konect (connexion comptes + clé API), Airtable (token + création auto), persona, offre, prompts agents.
---
# Onboarding — Setup KonectOS

Configure KonectOS de zéro à « prêt à automatiser » en 5 phases guidées.

## Quand activer

- Première utilisation
- Fichiers `memory/` avec `[À CONFIGURER]`
- L'utilisateur demande « setup », « configuration », « onboarding »

## Prérequis

Lire `memory/brain.md`. Si une phase est déjà faite (ex : Konect OK mais Airtable manquant), reprendre à la phase concernée.

---

## Phase 1 — Konect (connexion des comptes + clé API)

### 1.0 Abonnement Konect

KonectOS automatise via [mykonect.ai](https://mykonect.ai) — **20 €/mois par compte social connecté** (LinkedIn, WhatsApp, Instagram = 3 comptes = 60 €/mois max).

Pas encore de compte → inviter à s'inscrire sur [mykonect.ai](https://mykonect.ai).

### 1.1 Connecter les comptes sociaux sur le dashboard Konect

**Avant de toucher à la clé API**, les comptes doivent être connectés depuis le dashboard Konect.

Expliquer à l'utilisateur :

> « Sur [mykonect.ai](https://mykonect.ai) → **Accounts** → clique sur "+ Add account" et connecte LinkedIn, WhatsApp et/ou Instagram selon les plateformes que tu veux automatiser. Chaque plateforme = 1 compte = 20 €/mois. »
>
> « Une fois tes comptes connectés, reviens ici. »

Attendre la confirmation avant de continuer.

### 1.2 Récupérer la clé API Konect

Demander directement en conversation :

> « Génère ta clé API Konect : dashboard [mykonect.ai](https://mykonect.ai) → **Settings → API Keys → Create key**. Copie-la ici (elle commence par `knct_`). »

Une fois la clé fournie :

1. Écrire dans `.env` :

```
KONECT_API_KEY=knct_XXXXX
KONECT_BASE_URL=https://mykonect.ai/api/v1
```

2. Tester immédiatement la connexion :

```bash
curl -s "https://mykonect.ai/api/v1/accounts" \
  -H "Authorization: Bearer LA_CLE_FOURNIE"
```

- `"ok": true` → clé valide ✓
- `401` / `403` → clé invalide. Demander de la régénérer sur [mykonect.ai](https://mykonect.ai) → Settings → API Keys.

### 1.3 Détecter et enregistrer les comptes connectés

La réponse de `GET /accounts` liste les comptes. Pour chaque entrée afficher :

```
| Plateforme | Statut       | UUID                                 |
|------------|--------------|--------------------------------------|
| LinkedIn   | connected    | xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx |
| WhatsApp   | connected    | xxxxxxxx-...                         |
| Instagram  | disconnected | — (à connecter sur mykonect.ai)      |
```

Pour chaque compte `connected`, écrire dans `.env` :

```
KONECT_ACCOUNT_ID_LINKEDIN=UUID
KONECT_ACCOUNT_ID_WHATSAPP=UUID
KONECT_ACCOUNT_ID_INSTAGRAM=UUID
```

Et noter les UUIDs dans `memory/operational/config.md` (section Account IDs).

Si un compte est `disconnected` : rappeler de le connecter depuis [mykonect.ai](https://mykonect.ai) → Accounts. Les commandes de cette plateforme seront inactives jusqu'à connexion.

### 1.4 Fenêtre d'envoi (recommandé)

Proposer de configurer la fenêtre horaire via `PATCH /accounts/{id}/settings` :
- `send_start_hour` / `send_end_hour` (ex : 8h–18h)
- `send_on_weekends: false`
- `timezone` (ex : `Europe/Paris`)
- `inbox_sync_enabled: true`

---

## Phase 2 — Airtable (token + création automatique)

### 2.1 Créer le token Airtable avec les bons scopes

Expliquer à l'utilisateur exactement quoi faire :

> « Va sur [airtable.com/create/tokens](https://airtable.com/create/tokens) → **Create new token** avec ces scopes précis (sans eux Claude ne peut pas créer la base) :
>
> ✅ `data.records:read` — lire les contacts  
> ✅ `data.records:write` — créer/modifier les contacts  
> ✅ `schema.bases:read` — lire la structure des tables  
> ✅ `schema.bases:write` — **créer les tables et les champs automatiquement**  
>
> Et dans "Access" : sélectionne **All current and future bases** (ou au moins la base KonectOS une fois créée).  
> Copie le token ici (il commence par `pat`). »

Une fois le token fourni :

1. Écrire dans `.env` :

```
AIRTABLE_API_KEY=patXXXXXXXXXXXXXX
```

2. Tester la connexion :

```bash
curl -s "https://api.airtable.com/v0/meta/bases" \
  -H "Authorization: Bearer LE_TOKEN_FOURNI"
```

- Réponse avec liste de bases (ou liste vide `[]`) → token valide ✓
- `401` / `403` → token invalide ou scopes manquants. Vérifier les scopes listés ci-dessus.

### 2.2 Créer la base KonectOS automatiquement

Claude crée la base via l'API Airtable :

```bash
curl -s -X POST "https://api.airtable.com/v0/meta/bases" \
  -H "Authorization: Bearer AIRTABLE_API_KEY" \
  -H "Content-Type: application/json" \
  -d '{
    "name": "KonectOS",
    "workspaceId": "WORKSPACE_ID",
    "tables": [
      {
        "name": "Contacts",
        "fields": [
          {"name": "Nom", "type": "singleLineText"},
          {"name": "Entreprise", "type": "singleLineText"},
          {"name": "Titre", "type": "singleLineText"},
          {"name": "Localisation", "type": "singleLineText"},
          {"name": "ID", "type": "singleLineText"},
          {"name": "LinkedIn URL", "type": "url"},
          {"name": "Instagram", "type": "singleLineText"},
          {"name": "WhatsApp", "type": "phoneNumber"},
          {"name": "Plateforme source", "type": "singleSelect", "options": {"choices": [{"name": "LinkedIn"}, {"name": "WhatsApp"}, {"name": "Instagram"}]}},
          {"name": "Source", "type": "singleSelect", "options": {"choices": [{"name": "LinkedIn Search"}, {"name": "LinkedIn Sales Navigator"}, {"name": "LinkedIn Relations"}, {"name": "LinkedIn Followers"}, {"name": "LinkedIn Post Engagement"}, {"name": "Instagram Search"}, {"name": "Instagram Followers"}, {"name": "WhatsApp Import"}, {"name": "Inbound"}, {"name": "Manuel"}]}},
          {"name": "Relation", "type": "singleSelect", "options": {"choices": [{"name": "1"}, {"name": "2"}, {"name": "3"}, {"name": "3+"}]}},
          {"name": "Statut", "type": "singleSelect", "options": {"choices": [{"name": "New"}, {"name": "Contacté"}, {"name": "Répondu"}, {"name": "RDV"}, {"name": "Gagné"}, {"name": "Perdu"}, {"name": "Ne pas contacter"}]}},
          {"name": "Score ICP", "type": "number", "options": {"precision": 0}},
          {"name": "Notes", "type": "multilineText"},
          {"name": "Dernier contact", "type": "date", "options": {"dateFormat": {"name": "iso"}}},
          {"name": "Dernier message", "type": "multilineText"},
          {"name": "Icebreaker", "type": "multilineText"},
          {"name": "chatId Konect", "type": "singleLineText"},
          {"name": "Plateforme chat", "type": "singleSelect", "options": {"choices": [{"name": "linkedin"}, {"name": "whatsapp"}, {"name": "instagram"}]}},
          {"name": "Unread", "type": "number", "options": {"precision": 0}}
        ]
      },
      {
        "name": "Contenus",
        "fields": [
          {"name": "Titre", "type": "singleLineText"},
          {"name": "Plateforme", "type": "singleSelect", "options": {"choices": [{"name": "LinkedIn"}, {"name": "Instagram"}, {"name": "WhatsApp"}]}},
          {"name": "Type", "type": "singleSelect", "options": {"choices": [{"name": "Post"}, {"name": "Carousel"}, {"name": "Message"}, {"name": "Story"}]}},
          {"name": "Statut", "type": "singleSelect", "options": {"choices": [{"name": "Brouillon"}, {"name": "Planifié"}, {"name": "Publié"}]}},
          {"name": "Texte", "type": "multilineText"},
          {"name": "Date publication", "type": "date", "options": {"dateFormat": {"name": "iso"}}},
          {"name": "scheduledAt", "type": "singleLineText"}
        ]
      }
    ]
  }'
```

**Note** : récupérer d'abord le `workspaceId` :

```bash
curl -s "https://api.airtable.com/v0/meta/workspaces" \
  -H "Authorization: Bearer AIRTABLE_API_KEY"
```

Prendre le premier workspace (`id`).

Après création, noter dans `.env` et `memory/operational/config.md` :

```
AIRTABLE_BASE_ID=appXXXXXXXXX   ← id retourné par POST /meta/bases
TABLE_CONTACTS=tblXXXXXXXXX
TABLE_CONTENUS=tblXXXXXXXXX
```

Les IDs des tables se récupèrent via :

```bash
curl -s "https://api.airtable.com/v0/meta/bases/BASE_ID/tables" \
  -H "Authorization: Bearer AIRTABLE_API_KEY"
```

---

## Phase 3 — Identité & offre

Questions guidées pour remplir :

- `memory/identity/persona.md` — profil utilisateur + ICP (secteur, taille entreprise, problème résolu)
- `memory/identity/brand.md` — ton, style, ce qu'on évite
- `memory/identity/offer.md` — offre, **CTA** (`goalLink`), règles de prix à ne jamais donner en DM

---

## Phase 4 — Prompts agents

Renseigner `memory/operational/config.md` :

- `agentName` (ex : "Sophie de Konect")
- `companyName`
- `language` (fr / en)

Compléter `memory/operational/agent-prompts.md` :

- Sections **WhatsApp / LinkedIn / Instagram** — adapter le system prompt, le premier message type et les relances au métier de l'utilisateur.

---

## Phase 5 — Templates & brain

- `memory/operational/templates.md` — valider 1–2 icebreakers adaptés au métier
- `memory/brain.md` — cocher les phases complétées, résumer le contexte

---

## Fin

Afficher un récapitulatif :

```
✅ Konect : X compte(s) connecté(s) (LinkedIn / WhatsApp / Instagram)
✅ Airtable : base "KonectOS" créée (Contacts + Contenus)
✅ Identité configurée
✅ Prompts agents prêts

Prochaine étape suggérée :
→ /linkedin-agent  pour gérer tes DMs LinkedIn
→ /prospect        pour trouver des leads
```
