---
description: Qualifie les contacts qui ont répondu — critères métier, enrichissement, verdict go/no-go avant de proposer un RDV.
---
# Qualify — qualification avant RDV

Sépare, parmi les gens qui ont répondu, ceux à qui proposer un rendez-vous de
ceux qu'il ne faut pas relancer.

## Quand activer

- « qui vaut le coup », « qualifie », avant `/book`
- après un `/inbox-triage` qui a sorti des conversations « commercial »

## Prérequis

- `memory/identity/persona.md` — l'ICP et surtout les critères DISQUALIFIANTS
- Les critères de qualification définis à l'onboarding

## Récupérer ceux qui ont répondu

```
list_conversations(unreadOnly: true, limit: 30, includeLastMessages: 5)
```

Une réponse ne vaut pas un intérêt : « merci mais non », « je ne suis pas
concerné » et un message d'absence automatique sont des réponses. Lire le
contenu avant de qualifier.

## Enrichir — en lot, jamais un par un

```
enrich_profiles_batch(accountId: "...", identifiers: [...])   // 20 max
get_enrichment_results(queueIds: [...])                        // quelques minutes après
```

Boucler `get_profile` sur 20 personnes coûte 20 unités et déclenche 20 appels
plateforme espacés ; le lot fait le même travail proprement. Le cache évite de
repayer un profil déjà vu récemment.

## Décider

Pour chaque contact, un verdict explicite :

| Verdict | Ce que ça veut dire | Suite |
|---|---|---|
| Go | correspond à l'ICP, intérêt exprimé | `/book` |
| À creuser | signal faible, info manquante | une question ciblée |
| No-go | hors cible, ou critère disqualifiant | ne pas relancer, noter pourquoi |

Un no-go doit être **écrit** avec sa raison : sans ça la même personne revient
dans le pipeline au tour suivant.

## Restituer

Tableau : contact | société | poste | signal d'intérêt | verdict | raison.
Mettre à jour le CRM si connecté (statut + note). Sans CRM, écrire dans
`memory/operational/pipeline.md` — sinon la qualification est perdue.
