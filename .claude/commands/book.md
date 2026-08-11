---
description: Propose un rendez-vous aux contacts qualifiés — un message par personne, validé avant envoi.
---
# Book — proposer un RDV

Transforme un contact qualifié en rendez-vous posé, sans tomber dans le message
type qu'on repère à dix mètres.

## Quand activer

- « propose un call », « envoie mon lien », après `/qualify`

## Prérequis

- Verdict **Go** de `/qualify` — ne pas proposer de RDV à un contact non qualifié
- Le lien de réservation et le type de RDV (durée, objet) définis à l'onboarding

## Reprendre le fil AVANT d'écrire

```
get_conversation(chatId: "...", limit: 20)
```

La proposition doit s'appuyer sur ce que la personne a dit. « Suite à ce que
vous disiez sur X » convertit ; « seriez-vous disponible pour un échange »
ne convertit pas.

## Rédiger

Un message par personne, jamais un gabarit copié :

- rappeler en une ligne le point concret qui justifie l'échange ;
- annoncer la durée et l'objet (« 20 min pour voir si ça s'applique chez vous ») ;
- **une seule** façon de répondre : le lien, ou deux créneaux — pas les deux ;
- pas de relance dans le premier message.

Message court. Sur LinkedIn et WhatsApp, un pavé ne se lit pas.

## Valider puis envoyer

Afficher tous les messages, attendre validation, puis :

```
send_message(accountId: "...", chatId: "...", content: "...")
```

Vérifier la marge avant un lot :

```
get_usage()   // limits[].actions.message : perHour / perDay / perWeek
```

## Après l'envoi

Noter dans le CRM (ou `memory/operational/pipeline.md`) : date de proposition,
créneau proposé, et la date de relance si pas de réponse — c'est ce que
`/followup-rdv` relira.
