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

### 3. Airtable (gratuit) — selon ton usage

Base CRM **KonectOS** avec 2 tables : **Contacts** + **Contenus**, créée lors de
l'`/onboarding`.

**Requis pour la prospection et le setting** : lancer une séquence sans trace de
qui a été contacté, c'est recontacter les mêmes personnes et en oublier
d'autres. **Optionnel pour le support, le contenu et la veille** — l'onboarding
te le proposera sans bloquer si tu passes.

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

## MCP Konect

Le fichier [`.mcp.json`](.mcp.json) connecte le serveur MCP Konect
(`https://mykonect.ai/api/mcp`) avec ta `KONECT_API_KEY`. C'est la voie par
défaut pour toutes les actions : les tools renvoient le quota restant, des
erreurs actionnables, et embarquent les guides (`get_konect_guide`).

Le REST v1 reste documenté dans [`CLAUDE.md`](CLAUDE.md) comme repli.

---

## Commandes (32)

Organisées par ce que tu veux faire, pas par plateforme.

| Cas d'usage | Commandes |
|-----------|-------------|
| **Support client** | `/inbox-triage`, `/reply-draft`, `/faq` |
| **Prospection** | `/prospect`, `/enrich`, `/score`, `/invite`, `/icebreaker`, `/followup` |
| **Setting / RDV** | `/qualify`, `/book`, `/followup-rdv` |
| **Contenu** | `/post`, `/carousel`, `/instagram-post`, `/content-plan`, `/engage` |
| **Analyse & veille** | `/dashboard`, `/report`, `/weekly` |
| **Stratégie** | `/icp`, `/positioning`, `/sequence` |
| **Par plateforme** | `/linkedin-agent`, `/whatsapp-agent`, `/instagram-agent`, `/instagram-prospect` |
| **Système** | `/onboarding`, `/brain-status`, `/memory-save`, `/crm`, `/settings` |

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
