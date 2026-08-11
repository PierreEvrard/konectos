---
description: Rédige des brouillons de réponse support à partir du contexte réel de la conversation et de la voix de marque — validation obligatoire avant envoi.
---
# Reply draft — brouillons de réponse

Écrit des réponses prêtes à envoyer pour les conversations identifiées par
`/inbox-triage`, dans le ton de l'utilisateur.

## Quand activer

- « réponds à », « prépare une réponse », après un `/inbox-triage`

## Prérequis

- `memory/identity/brand.md` (voix, ce qu'on dit et ne dit pas)
- `memory/knowledge/faq.md` si présent — les réponses déjà validées priment

## Lire le contexte AVANT de rédiger

```
get_conversation(chatId: "...", limit: 20)
```

Répondre sans avoir lu l'historique produit des réponses à côté du sujet :
la personne a souvent déjà expliqué son problème, ou on lui a déjà répondu.

Si le contact n'est pas connu :

```
get_profile(accountId: "...", identifier: "<url ou provider_id>")
```

## Rédiger

Pour chaque conversation, un brouillon qui :

- répond à **la** question posée, sans préambule ni formule creuse ;
- reprend le vocabulaire de la personne ;
- tient en 3-5 lignes — c'est de la messagerie, pas un e-mail ;
- ne promet rien qui ne soit pas dans `memory/` (jamais de délai, de prix ou
  d'engagement inventé) ;
- se termine par une question ou une action claire quand une suite est utile.

**Escalader plutôt que répondre** si : réclamation, question juridique ou
contractuelle, demande de remboursement, ton hostile. Dans ce cas, proposer un
message d'attente honnête (« je fais suivre à X, réponse d'ici Y ») et signaler
à l'utilisateur que ça demande son intervention.

## Valider puis envoyer

Afficher tous les brouillons, attendre la validation explicite, **puis
seulement** :

```
send_message(accountId: "...", chatId: "...", content: "...")
```

L'envoi est mis en file avec un délai humain (45-120 s) : c'est normal, le
message n'est pas parti à la seconde. `get_action_status(queueId)` confirme.

Avant un lot de réponses, vérifier la marge avec `get_usage` — un refus en
milieu de série laisse la moitié des gens sans réponse.
