---
description: Construit et maintient la FAQ à partir des vraies questions reçues dans l'inbox Konect.
---
# FAQ — réponses réutilisables

Repère les questions qui reviennent dans les vraies conversations et en fait des
réponses validées, réutilisables par `/reply-draft`.

## Quand activer

- « fais-moi une FAQ », « quelles questions reviennent »
- après quelques semaines d'activité support

## Trouver ce qui revient

Balayer l'historique par thème plutôt qu'en lisant tout :

```
search_inbox(query: "prix")
search_inbox(query: "livraison")
search_inbox(query: "comment")
```

`search_inbox` cherche dans le cache Konect, pas sur la plateforme : c'est
gratuit en quota et instantané. Lancer une poignée de recherches sur les termes
métier de l'utilisateur, puis regrouper.

Pour repartir de l'activité brute :

```
list_recent_messages(sinceHours: 720, limit: 100)
```

## Regrouper

Une entrée FAQ est justifiée à partir de **3 occurrences** de la même question.
En dessous, c'est un cas particulier, pas une FAQ.

Pour chaque entrée :
- la question telle qu'elle est réellement posée (pas reformulée en jargon)
- le nombre de fois où elle est apparue
- la réponse validée, en 3-5 lignes
- ce qui doit déclencher une escalade plutôt que cette réponse

## Écrire

Persister dans `memory/knowledge/faq.md`. Ce fichier est lu par `/reply-draft`,
donc chaque ajout améliore les brouillons suivants.

Faire relire les réponses par l'utilisateur avant de les enregistrer : une FAQ
fausse se propage ensuite dans toutes les réponses automatiquement.
