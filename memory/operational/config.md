# Configuration opérationnelle KonectOS

## Konect

- **Base URL API** : utiliser `KONECT_BASE_URL` du `.env` (défaut doc : `https://mykonect.ai/api/v1`)
- **Clé API** : `KONECT_API_KEY` dans `.env` (ne jamais committer la clé réelle)

### Account IDs (UUID)

| Plateforme | Account ID | Statut |
|------------|------------|--------|
| LinkedIn   | [À CONFIGURER] | |
| WhatsApp   | [À CONFIGURER] | |
| Instagram  | [À CONFIGURER] | |

### Variables agents (répliquent les placeholders `{{...}}`)

- **agentName** : [À CONFIGURER]
- **companyName** : [À CONFIGURER]
- **language** : français

## Airtable

- **Base ID** : [À CONFIGURER]
- **Table Contacts** : [À CONFIGURER après création meta]
- **Table Contenus** : [À CONFIGURER]

## Paramètres d’envoi (optionnel — aussi gérables via `/settings`)

- **Timezone** : [À CONFIGURER]
- **Fenêtre** : [À CONFIGURER] (ex. 9h–19h)
- **Week-end** : [À CONFIGURER]
