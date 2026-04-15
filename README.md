# KonectOS

Assistant **Claude Code** centré sur **Konect** : automatisation **LinkedIn**, **WhatsApp** et **Instagram** (messages, posts, commentaires, réactions, invitations, file d’attente, stats) + **CRM Airtable** + **prompts agents** configurables.

## Prérequis

- Compte [Konect](https://konect.dev) avec clé API personnelle
- Base **Airtable** (CRM)
- [Claude Code](https://docs.anthropic.com/claude-code) ou Cursor avec agent

## Installation rapide

```bash
cd KonectOS
cp .env.example .env
# Éditer .env : KONECT_API_KEY, KONECT_BASE_URL si besoin, account IDs, AIRTABLE_*
```

1. Ouvre le dossier dans ton IDE / Claude Code  
2. Lance **`/onboarding`** (ou décris ton besoin de setup en langage naturel)  
3. Vérifie l’état avec **`/brain-status`**

## Variables d’environnement

| Variable | Rôle |
|----------|------|
| `KONECT_API_KEY` | Bearer Konect |
| `KONECT_BASE_URL` | API (défaut `https://mykonect.ai/api/v1`) |
| `KONECT_ACCOUNT_ID_LINKEDIN` | UUID compte LinkedIn |
| `KONECT_ACCOUNT_ID_WHATSAPP` | UUID WhatsApp |
| `KONECT_ACCOUNT_ID_INSTAGRAM` | UUID Instagram |
| `AIRTABLE_API_KEY` | Token Airtable |
| `AIRTABLE_BASE_ID` | Base CRM |

Les **account IDs** se récupèrent avec `GET /accounts` (voir `CLAUDE.md`).

## MCP (optionnel)

Le fichier [`.mcp.json`](.mcp.json) référence le MCP documenté Konect / KodeStudio. Active-le dans ton client si tu veux de l’aide contextuelle sur la doc.

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

Tu peux taper `/commande` **ou** décrire ton intention : l’auto-routing est défini dans [`CLAUDE.md`](CLAUDE.md).

## Mémoire projet

- `memory/brain.md` — index de session  
- `memory/identity/*` — persona, brand, offre  
- `memory/operational/*` — config Konect/Airtable, **agent-prompts**, templates, séquences  
- `memory/knowledge/lessons.md` — apprentissages  
- `memory/synthesis/weekly-synthesis.md` — revue hebdo  

## Licence & usage

Projet template pour clients Konect / solopreneurs : configure ta propre clé et ta base ; ne commite jamais `.env`.
