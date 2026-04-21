# KonectOS

Assistant **Claude Code** centré sur **[Konect](https://mykonect.ai)** : automatisation **LinkedIn**, **WhatsApp** et **Instagram** (messages, posts, commentaires, réactions, invitations, file d'attente, stats) + **CRM Airtable** + **prompts agents** configurables.

---

## Prérequis

### 1. Claude Code ou Cursor (obligatoire)

KonectOS fonctionne avec **[Claude Code](https://docs.anthropic.com/claude-code)** (terminal Anthropic) ou **[Cursor](https://cursor.sh)** en mode agent. Ce n'est pas une application web — c'est un assistant IA piloté depuis ton éditeur.

### 2. Abonnement Konect (obligatoire)

KonectOS utilise l'API **[Konect](https://mykonect.ai)** pour piloter LinkedIn, WhatsApp et Instagram.

> **Tarif : 20 €/mois par compte social connecté**  
> (LinkedIn = 1 compte, WhatsApp = 1 compte, Instagram = 1 compte — chaque plateforme est indépendante)

1. Créer un compte sur [mykonect.ai](https://mykonect.ai)
2. Connecter les plateformes souhaitées (LinkedIn, WhatsApp, Instagram)
3. Générer une clé API (`knct_...`) depuis le dashboard
4. Renseigner la clé dans le fichier `.env`

### 3. Airtable (gratuit)

Base CRM **KonectOS** avec 2 tables : **Contacts** + **Contenus**. Créée lors de l'`/onboarding`.

---

## Installation

```bash
git clone https://github.com/PierreEvrard/konectos.git
cd konectos
cp .env.example .env
# Renseigner KONECT_API_KEY, account IDs, AIRTABLE_*
```

1. Ouvrir le dossier dans Claude Code ou Cursor  
2. Lancer **`/onboarding`** (guide interactif complet)  
3. Vérifier avec **`/brain-status`**

---

## Variables d'environnement

| Variable | Rôle |
|----------|------|
| `KONECT_API_KEY` | Clé API Konect (`knct_...`) — depuis [mykonect.ai](https://mykonect.ai) |
| `KONECT_BASE_URL` | API Konect (défaut : `https://mykonect.ai/api/v1`) |
| `KONECT_ACCOUNT_ID_LINKEDIN` | UUID du compte LinkedIn connecté |
| `KONECT_ACCOUNT_ID_WHATSAPP` | UUID du compte WhatsApp connecté |
| `KONECT_ACCOUNT_ID_INSTAGRAM` | UUID du compte Instagram connecté |
| `AIRTABLE_API_KEY` | Token Airtable |
| `AIRTABLE_BASE_ID` | ID de la base **KonectOS** |

Les **account IDs** sont récupérés automatiquement lors de l'`/onboarding` via `GET /accounts`.

---

## MCP Konect — voie nominale

Le fichier [`.mcp.json`](.mcp.json) configure le MCP natif Konect
(`https://mykonect.ai/api/mcp`). **Toutes les actions sociales passent par le
MCP** : messages, invitations, posts, commentaires, enrichissement de profils,
inbox, etc. Konect gère la file d’attente, les délais humains, les caps par
plateforme et la sécurité du compte — voir les "Konect Operating Rules" en
haut de [`CLAUDE.md`](CLAUDE.md).

La clé API (`knct_...`) est lue depuis la variable `KONECT_API_KEY` du fichier
`.env` (Claude Code / Cursor / VS Code / Antigravity la substituent
automatiquement au démarrage).

---

## Commandes (26)

| Catégorie | Commandes |
|-----------|-------------|
| **Système** | `/onboarding`, `/brain-status`, `/memory-save` |
| **Stratégie** | `/icp`, `/positioning`, `/sequence` |
| **LinkedIn** | `/prospect`, `/enrich`, `/score`, `/invite` |
| **CRM & messages** | `/crm`, `/icebreaker`, `/followup`, `/linkedin-agent`, `/whatsapp-agent` |
| **Instagram** | `/instagram-prospect`, `/instagram-agent` |
| **Contenus** | `/post`, `/carousel`, `/instagram-post`, `/content-plan`, `/engage` |
| **Analytics** | `/dashboard`, `/report`, `/weekly`, `/settings` |

Tape `/commande` **ou** décris ton besoin en langage naturel : l'auto-routing de [`CLAUDE.md`](CLAUDE.md) détecte l'intention automatiquement.

---

## Mémoire projet

| Fichier | Rôle |
|--------|------|
| `memory/brain.md` | Index de session (lu en premier) |
| `memory/identity/` | Persona, brand, offre |
| `memory/operational/config.md` | Clés, IDs, paramètres |
| `memory/operational/agent-prompts.md` | Prompts IA par canal (WA/LI/IG) |
| `memory/operational/templates.md` | Messages validés (few-shots) |
| `memory/knowledge/lessons.md` | Apprentissages |
| `memory/synthesis/weekly-synthesis.md` | Revues hebdo |

---

## Licence & usage

Template open-source pour solopreneurs utilisant Konect. Chaque utilisateur configure sa propre clé API et sa propre base Airtable. Ne jamais commiter le fichier `.env`.
