---
description: Trie l'inbox Konect (non-lus, urgences, questions récurrentes) et propose un plan de traitement — aucune réponse envoyée sans validation.
---
# Inbox triage — support client

Passe en revue les messages entrants sur tous les comptes connectés, les classe
par nature et par urgence, et propose quoi traiter en premier.

## Quand activer

- « trie mon inbox », « qu'est-ce qui est arrivé », « j'ai quoi à répondre »
- début de journée, reprise après absence

## Lire l'inbox — une seule fois, pas en boucle

```
list_recent_messages(sinceHours: 24, limit: 50)
```

Un seul appel ramène l'activité de toutes les conversations avec les métadonnées
des participants. **Ne pas** boucler `get_conversation` conversation par
conversation : c'est plus lent et ça n'apporte rien de plus.

Pour ne voir que ce qui attend une réponse :

```
list_conversations(unreadOnly: true, limit: 30, includeLastMessages: 5)
```

Lire ne marque rien comme lu — les badges de non-lus de l'utilisateur restent
intacts, tu peux parcourir librement.

## Classer

Range chaque message entrant dans une de ces catégories :

| Catégorie | Ce que c'est | Priorité |
|---|---|---|
| Urgent | client bloqué, incident, mécontentement | traiter tout de suite |
| Question | demande d'info à laquelle la FAQ répond | réponse rapide |
| Commercial | intérêt, demande de prix, de démo | passer au setting |
| Administratif | facture, accès, RGPD | à router vers un humain |
| Bruit | spam, automatique, hors sujet | ignorer |

Signale à part toute conversation marquée `account_disconnected` : elle est
lisible mais **on ne peut pas y répondre** tant que le compte n'est pas
reconnecté.

## Restituer

Un tableau : contact | plateforme | catégorie | résumé en une ligne | action
proposée. Puis les 3 sujets qui reviennent le plus (candidats FAQ).

## Répondre

Ne rédige rien ici — enchaîne sur `/reply-draft` pour les brouillons, ou
`/faq` si la même question revient souvent.
