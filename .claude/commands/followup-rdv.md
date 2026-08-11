---
description: Relance les propositions de RDV restées sans réponse — une fois, avec un angle neuf, puis on arrête.
---
# Followup RDV — relance de rendez-vous

Relance les gens à qui un RDV a été proposé et qui n'ont pas répondu, sans
devenir insistant.

## Quand activer

- « relance les RDV », « qui n'a pas répondu », quelques jours après `/book`

## Trouver qui relancer

Croiser deux sources :

```
list_conversations(limit: 50, includeLastMessages: 3)
```

et le pipeline (CRM ou `memory/operational/pipeline.md`) : on cherche les
contacts dont le **dernier message est sortant** et date de plus de 3 jours.

Vérifier message par message : quelqu'un qui a répondu « pas maintenant » n'est
pas un silence, c'est un refus poli. Le relancer casse la relation.

## La règle : une seule relance

Une relance, jamais deux. Après ça, le contact sort du pipeline actif — on
pourra le recontacter dans plusieurs mois sur un autre prétexte, pas avant.

Délai : 3 à 5 jours ouvrés après la proposition. Moins, c'est du harcèlement ;
plus, le contexte est perdu.

## Rédiger

Une relance qui apporte **quelque chose de neuf** :

- un angle différent (« je repensais à ce que vous disiez sur X »)
- une info utile (un article, un cas similaire)
- ou une porte de sortie explicite (« si ce n'est pas le moment, dites-le
  moi et je n'insiste pas »)

Ce qu'il ne faut pas écrire : « je me permets de revenir vers vous »,
« up », « avez-vous vu mon message ». Ces relances ne relancent rien.

## Valider puis envoyer

```
send_message(accountId: "...", chatId: "...", content: "...")
```

Marquer ensuite le contact comme relancé dans le pipeline, avec la date : c'est
ce qui garantit qu'il ne sera pas relancé une deuxième fois.
