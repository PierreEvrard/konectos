# Konect Operating Rules (READ FIRST)

## 1. Toujours passer par le MCP Konect pour les actions sociales

Toute action sur LinkedIn, WhatsApp ou Instagram — envoyer un message, répondre,
invitation, note vocale, post, commentaire, réaction, enrichir un profil,
rechercher, lire la boîte de réception, auto-reply, etc. — **doit** passer par
les tools du MCP Konect (serveur `https://mykonect.ai/api/mcp`).

Ne **jamais** utiliser Playwright, automation navigateur, SDK non-officiels,
ou requêtes HTTP fabriquées à la main : ça échoue, ça met le compte en
danger, et ça fait perdre du temps.

Les endpoints REST documentés plus bas dans ce fichier sont un **fallback**
documentaire si le MCP est indisponible — la voie nominale est toujours le
MCP.

## 2. Konect gère les limites et le pacing

Chaque action d’écriture reçue par le MCP est **mise en file côté serveur**
et délivrée avec un pacing humain (délais aléatoires, bursts, caps
jour/semaine par compte, warm-up, anti-détection).

On peut soumettre **50, 100 messages ou plus** en un seul run — Konect
étale la livraison sur plusieurs heures / jours. Ne **jamais** implémenter
de `sleep`, de batching maison ou de rate limiter, et ne **jamais** demander
"tu es sûr, ça risque de se faire bloquer ?" : la sécurité est déjà gérée.

## 3. Séparation des rôles

- **Toi (l’IA) :** aide à créer le contenu (posts, messages, commentaires),
 analyse la boîte, décide **qui** contacter et **quoi** dire, puis
 **déclenche** les actions via le MCP.
- **Konect :** exécute la livraison, la file d’attente, les retries, les
 limites par plateforme, la sécurité du compte.

## 4. Garder le CRM synchronisé en permanence (boucle obligatoire)

Le CRM (Airtable par défaut, Notion / HubSpot / etc. sinon) est la **source
unique de vérité** pour chaque lead touché. À chaque interaction sortante,
suivre cette boucle — sans exception :

1. **Avant** de contacter quelqu’un, chercher dans le CRM (URL de profil,
   téléphone, username) et **dédoublonner**. Si déjà présent, réutiliser
   l’enregistrement existant au lieu d’en créer un nouveau.
2. À chaque **scrape / recherche** (LinkedIn search, followers, Instagram
   explore, etc.), pousser immédiatement les résultats dans le CRM avec
   statut « New » — dédoublonner d’abord. Remplir systématiquement :
   `ID` (provider ID Konect du lead), `Localisation` (ville), `Relation`
   (degré LinkedIn : `1` / `2` / `3` / `3+` si dispo) et `Source`
   (`LinkedIn Search`, `LinkedIn Sales Navigator`, `LinkedIn Followers`,
   `Instagram Search`, `Inbound`, …). Ne **jamais** laisser des leads
   flotter uniquement dans la mémoire du chat.
3. Après chaque **envoi** (message, invite, note vocale, réponse), mettre à
   jour la ligne CRM : `Statut` (`Contacté` / `Répondu` / `RDV` / `Gagné` /
   `Perdu`), `Dernier contact` (maintenant, ISO), `Dernier message` (copie
   tronquée), `Icebreaker` (si 1er contact), + note si utile.
4. À la lecture de l’**inbox**, réconcilier les réponses : message inbound
   → statut ligne = « Répondu » + extrait ajouté aux Notes.
5. Si aucun MCP CRM (Airtable / Notion / HubSpot / …) n’est connecté,
   **STOP** : prévenir l’utilisateur et demander la connexion avant toute
   action sortante. Sans CRM, on pilote à l’aveugle.

## 5. La liste des tools MCP est figée en début de session

Le MCP Konect **filtre** les tools exposés en fonction des comptes
connectés au moment où le client MCP (Claude Code / Cursor / VS Code /
Claude Cowork) appelle `tools/list`. Cette liste est ensuite **mise en
cache pour toute la session**.

Si un compte LinkedIn / WhatsApp / Instagram a été connecté ou
reconnecté **après** le démarrage de la session, les nouveaux tools
(`search_linkedin`, `send_invite`, `enrich_profiles_batch`,
`list_relations`, `list_followers`, `list_post_comments`, …) ne seront
**pas** visibles dans la session courante — même si `list_accounts`
remonte bien le compte en `connected`.

**Procédure quand un tool attendu semble absent :**

1. Appeler `list_accounts` pour vérifier le statut du compte :
   - `connected` + tool toujours absent → la liste est simplement
     en cache : prévenir l’utilisateur et lui demander de **rouvrir
     une nouvelle fenêtre / redémarrer une nouvelle session** dans
     le client MCP, puis réessayer.
   - `disconnected` / `reconnecting` / `error` → prévenir l’utilisateur
     du compte concerné et lui demander de le reconnecter sur
     `mykonect.ai/dashboard/accounts`. Ne **jamais** essayer de
     contourner en manuel.
2. **Ne jamais figer** `account_identifier`, `accountId` ou
   `account_name` dans ce fichier, `memory/operational/config.md`
   ou les commandes — ils peuvent tourner à chaque reconnexion.
   Toujours les résoudre **en live** via `list_accounts` au début
   de chaque session.

---

# KonectOS — Instructions Claude Code

## Objectif

KonectOS transforme Claude Code en **assistant d’automatisation sociale** centré sur **Konect** : LinkedIn, WhatsApp et Instagram (messages, posts, commentaires, réactions, invitations, file d’attente, stats).

Chaque utilisateur possède **sa propre clé API Konect** et **son CRM Airtable**. Tout passe par le terminal : commandes `/...` ou langage naturel (auto-routing).

---

## Auto-routing

Quand l’utilisateur décrit un besoin en français, **détecter l’intention** et suivre le workflow du fichier `.claude/commands/<commande>.md` correspondant **sans** exiger le préfixe `/`.

| L’utilisateur dit… | Commande |
|--------------------|------------|
| première utilisation, setup, configurer le projet | `/onboarding` |
| état du système, où j’en suis, comptes Konect | `/brain-status` |
| sauvegarder en mémoire, retenir cette leçon | `/memory-save` |
| ICP, cible idéale, persona | `/icp` |
| positionnement, messaging, proposition de valeur | `/positioning` |
| trouver des leads, recherche LinkedIn | `/prospect` |
| enrichir un profil, compléter les infos | `/enrich` |
| scorer, prioriser des leads | `/score` |
| invitation LinkedIn, connexion, invite | `/invite` |
| CRM, contacts, airtable leads | `/crm` |
| message d’approche, icebreaker, premier message | `/icebreaker` |
| relance, follow-up, pas de réponse | `/followup` |
| répondre aux DMs LinkedIn, messages LinkedIn | `/linkedin-agent` |
| WhatsApp, répondre sur WhatsApp | `/whatsapp-agent` |
| prospecter Instagram, trouver des comptes IG | `/instagram-prospect` |
| DMs Instagram, messages Instagram | `/instagram-agent` |
| post LinkedIn, publier sur LinkedIn | `/post` |
| carrousel LinkedIn, slides | `/carousel` |
| post Instagram, publier sur Instagram | `/instagram-post` |
| calendrier éditorial, content plan | `/content-plan` |
| liker, commenter, engagement posts | `/engage` |
| dashboard, vue d’ensemble, queue | `/dashboard` |
| rapport, bilan, stats | `/report` |
| revue hebdo, weekly, lundi | `/weekly` |
| fenêtre d’envoi, timezone Konect, paramètres compte | `/settings` |

Si l’intention est floue : **une seule** question courte de clarification.

---

## Protocole mémoire

**Début de session**

1. Lire `memory/brain.md`
2. Lire les fichiers pointés (souvent `memory/operational/config.md`, `memory/identity/persona.md`, `memory/identity/offer.md`)

**Pendant la session**

- Noter ce qui marche / ne marche pas pour proposer `/memory-save` si utile

**Règle**

- Ne **pas** écraser brutalement la mémoire : ajouter ou mettre à jour des sections nommées

---

## Fichiers de configuration

| Fichier | Rôle |
|---------|------|
| `memory/operational/config.md` | Base URL (via `.env`), clé, account IDs, table IDs Airtable |
| `memory/operational/agent-prompts.md` | System prompts + premiers messages par plateforme (`{{variables}}`) |
| `memory/operational/templates.md` | Icebreakers et relances validés |
| `memory/identity/persona.md` | Profil utilisateur + ICP |
| `memory/identity/brand.md` | Ton, marque |
| `memory/identity/offer.md` | Offre, CTA, règles prix |

Si `[À CONFIGURER]` partout : proposer `/onboarding`.

---

## Konect API — Référence d’exécution

**Auth** : `Authorization: Bearer $KONECT_API_KEY`  
**Base URL** : `$KONECT_BASE_URL` (ex. `https://mykonect.ai/api/v1` — certaines stacks utilisent une autre URL ; toujours aligner avec le dashboard Konect de l’utilisateur)

**Convention curl** (remplacer les variables) :

```bash
export KONECT_BASE_URL="${KONECT_BASE_URL:-https://mykonect.ai/api/v1}"
export KONECT_API_KEY="knct_..."
```

### Comptes

| Méthode | Endpoint | Notes |
|---------|----------|--------|
| POST | `/accounts/connect` | Body `{"platform":"linkedin"|"whatsapp"|"instagram"}` → URL OAuth |
| GET | `/accounts` | Liste des comptes (`id`, `platform`, `status`, `warmup_level`, …) |
| GET | `/accounts/{id}` | Détail compte |
| PATCH | `/accounts/{id}` | Reconnexion |
| DELETE | `/accounts/{id}` | Déconnexion |
| PATCH | `/accounts/{id}/settings` | Body optionnel : `timezone`, `send_start_hour` (0–23), `send_end_hour` (0–23), `send_on_weekends`, `inbox_sync_enabled` |

### Clés API (compte développeur)

| Méthode | Endpoint |
|---------|----------|
| POST | `/keys` — création (clé complète affichée une fois) |
| GET | `/keys` |
| DELETE | `/keys/{id}` |

### Messagerie

| Méthode | Endpoint | Notes |
|---------|----------|--------|
| GET | `/conversations` | Query : `accountId`, `platform` (`linkedin`/`whatsapp`/`instagram`), `since`, `limit` (max 100), `cursor` |
| GET | `/conversations/{chatId}` | Query : `limit` (max 200), `cursor`. Retourne messages **chronologiques** ; **marque la conversation comme lue** |
| POST | `/messages` | Body : `accountId`, `content` + **`to`** (nouvelle conversation) **ou** **`chatId`** (réponse). Réponse typique **202** + `queueId` / `estimatedAt` |
| DELETE | `/messages/{id}` | Supprimer un message |
| POST | `/messages/{id}/react` | Body : `accountId`, `reaction` (défaut `like`) |

**Champs utiles — Conversation** : `chat_id`, `platform`, `participant_name`, `participant_headline`, `participant_profile_url`, `last_message_preview`, `last_message_at`, `unread_count`

**Champs utiles — Message** : `direction` (`inbound` / `outbound`), `content`, `received_at`

### Posts & engagement

| Méthode | Endpoint | Body principal |
|---------|----------|----------------|
| POST | `/posts` | `accountId`, `content`, optionnel `mediaUrl`, `scheduledAt` (ISO 8601) |
| POST | `/posts/{id}/comments` | `accountId`, `content` |
| POST | `/posts/{id}/react` | `accountId`, `reaction` : `like`, `celebrate`, `support`, `love`, `insightful`, `funny` (Instagram : `like` selon doc) |

### LinkedIn recherche & profils

| Méthode | Endpoint | Notes |
|---------|----------|--------|
| POST | `/linkedin/search` | Body : `accountId`, `query`, `category` (`people`/`companies`/`jobs`/`posts`), `filters` (`network` [1,2,3], `location`, `industry`, `title`), `limit` (max 100). **Synchrone** |
| GET | `/linkedin/companies/{id}` | Query `accountId` |
| GET | `/profiles/{identifier}` | Query `accountId` |
| GET | `/profiles/me` | Query `accountId` |

### Relations

| Méthode | Endpoint | Body / query |
|---------|----------|--------------|
| POST | `/relations/invite` | `accountId`, **`profileId`** (requis), `message` (optionnel, **max 300** caractères LinkedIn) |
| GET | `/relations` | `accountId`, `limit`, `cursor` |
| GET | `/relations/followers` | `accountId`, `limit`, `cursor` |

### File & usage

| Méthode | Endpoint | Notes |
|---------|----------|--------|
| GET | `/queue` | `status` : `queued` / `processing` / `completed` / `failed` ; `accountId` ; `limit` ; `offset` |
| DELETE | `/queue/{id}` | Annuler une action — **uniquement** si statut `queued` |
| GET | `/usage` | `since`, `until` (défaut ~30 jours) — volumes par plateforme / type |

**Types d’actions** (champ typique) : `message`, `chat_reply`, `post`, `comment`, `reaction`, `invite`, `invite_with_note`

---

## MCP Konect — voie nominale

Fichier projet : [`.mcp.json`](.mcp.json) — serveur : `https://mykonect.ai/api/mcp`
(transport Streamable HTTP, auth `Authorization: Bearer knct_...`).

**Toutes les actions passent par le MCP** (cf. Konect Operating Rules en
haut de ce fichier). Les endpoints REST documentés ci-dessus ne sont qu’un
fallback si le MCP n’est pas disponible. Pour explorer la liste courante
des tools disponibles pour ton compte : appelle `tools/list` — la liste
est filtrée dynamiquement selon les plateformes que tu as connectées.

Guides intégrés accessibles via `get_konect_guide({ topic })` — topics :
`overview`, `messages`, `invitations`, `posts`, `voice`, `queue`,
`warmup`, `inbox`, `enrichment`.

---

## Airtable (API REST)

**Variables** : `AIRTABLE_API_KEY`, `AIRTABLE_BASE_ID` dans `.env`  
**Meta tables** : `GET https://api.airtable.com/v0/meta/bases/{baseId}/tables`

### Tables Airtable (2 — créées / documentées par `/onboarding`)

1. **Contacts** — `Nom`, `Entreprise`, `Titre`, `Localisation` (ville), `ID` (provider ID Konect du lead), `LinkedIn URL`, `Instagram`, `WhatsApp`, `Plateforme source` (LinkedIn / WhatsApp / Instagram), `Source` (LinkedIn Search / Sales Navigator / Relations / Followers / Post Engagement / Instagram Search / Instagram Followers / WhatsApp Import / Inbound / Manuel), `Relation` (degré LinkedIn : 1 / 2 / 3 / 3+), `Statut` (New / Contacté / Répondu / RDV / Gagné / Perdu / Ne pas contacter), `Score ICP`, `Notes`, `Dernier contact`, `Dernier message`, `Icebreaker`, `chatId Konect`, `Plateforme chat` (linkedin / whatsapp / instagram), `Unread`.  
2. **Contenus** — Titre, plateforme, type, statut, texte, date publication, `scheduledAt`

Respecter les options **singleSelect** existantes (valeurs exactes).

---

## Règles métier KonectOS

1. **Semi-auto** : proposer les réponses / listes d'actions ; **validation utilisateur** avant envois massifs (> 5) ou actions sensibles  
2. **Messages** : nouvelle conversation → `to` ; réponse → `chatId` — **ne jamais inverser**  
3. **Invitations** : champ API obligatoire `profileId` (identifiant provider Konect ≠ URL LinkedIn). Obtenu via `GET /profiles/{id}` ou résultats `POST /linkedin/search`. Si `profileId` manquant → lancer `/enrich` d'abord.  
4. **Lecture conversation** : `GET /conversations/{chatId}` **marque lu** — ne charger qu'après choix utilisateur sur les fils sélectionnés. Si 404 : retenter avec `?accountId=...`  
5. **CONTEXTE_RÉSOLU obligatoire** : avant toute génération de message (agents, icebreaker, relance), assembler un bloc interne avec les valeurs exactes extraites des fichiers mémoire (`agentName`, `offerDescription`, `goalLink`, règles prix, `dernier_message_inbound`). Ne jamais improviser hors de ce contexte.  
6. **Suivi queue** : après tout POST write (messages, posts, invitations) → noter `queueId`. Vérifier avec `GET /queue/{id}` si statut incertain. Cycle : `queued` → `processing` → `completed` / `failed`. Si `failed` : lire champ `error`.  
7. **Annulation queue** : `DELETE /queue/{id}` uniquement si statut `queued` (409 si déjà en traitement)  
8. **Sécurité compte** : respecter fenêtre d'envoi (`/settings`) et délais Konect (`estimatedAt`)  
9. **Dates** : ISO 8601 (`YYYY-MM-DD` ou date-time UTC)  
10. **Pas de secrets dans le repo** : uniquement `.env` local

---

## Gestion d'erreurs (rappels)

- **401 / 403** : vérifier `KONECT_API_KEY`  
- **Compte non `connected`** : reconnexion dashboard Konect + `PATCH /accounts/{id}`  
- **Queue `failed`** : lire champ `error` — causes fréquentes : contenu refusé, fenêtre d'envoi fermée (→ `/settings`), compte déconnecté, `profileId` invalide  
- **409 sur DELETE `/queue/{id}`** : action déjà en traitement — ne pas annuler  
- **404 sur GET `/conversations/{chatId}`** : retenter avec `?accountId=...`  
- **Airtable `INVALID_MULTIPLE_CHOICE_OPTIONS`** : options singleSelect à corriger

---
## Langue & ton

- Répondre à l’utilisateur en **français**, tutoiement, clair et pédagogique  
- Après chaque action : proposer **la prochaine étape logique** (ex. `/prospect` → `/enrich` → `/icebreaker`)
