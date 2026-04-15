# Templates de messages validés

Espace pour les **icebreakers**, **premiers messages**, **relances** validés par l'utilisateur. Les agents et `/icebreaker` s'y réfèrent.

**Règle** : ces templates sont des **patterns de style** — ne pas copier-coller tel quel. Imiter le rythme, la longueur, la structure. Personnaliser `{prospect_name}` et `{context_note}` à chaque message.

---

## Règles générales KonectOS

- Valider avec l'utilisateur avant envoi en masse (> 5 messages)
- Premier message LinkedIn / Instagram : `POST /messages` avec `to` (identifiant profil ou URL)
- Réponse dans un fil existant : `chatId` + `content` (ne jamais inverser)
- Notes invitation LinkedIn : max 300 caractères (`POST /relations/invite` → champ `message`)
- Ne jamais envoyer de lien dans le tout premier message LinkedIn sauf politique contraire dans `offer.md`

---

## LinkedIn — Icebreakers (few-shots)

### Contexte : a commenté un post

```
Salut {prospect_name}, j'ai vu ton commentaire sur [sujet du post]

tu es sur quel type de projet en ce moment ?
```

### Contexte : a réagi à un post (like / partage)

```
Hey {prospect_name}, j'ai vu que tu avais réagi à [nom du post]

tu travailles encore sur [thème] ?
```

### Contexte : profil pertinent sans interaction connue

```
Salut {prospect_name}, ton profil est apparu dans mes recherches sur [secteur / rôle]

tu es en train de chercher quelque chose de précis dans ce domaine ?
```

---

## WhatsApp — Premiers messages (few-shots)

### Contexte générique : lien à partager

```
Salut {prospect_name}, {context_note}

je te mets le lien ici :
[goalLink]

tu en es où dans ton projet ?
```

### Contexte : absent à un live / webinar

```
Salut {prospect_name}, j'ai vu que tu t'étais inscrit au live mais t'as pas pu être là

je te mets le replay ici :
[goalLink]

tu cherches à lancer quoi en ce moment ?
```

### Contexte : a montré de l'intérêt

```
Hello {prospect_name}, {context_note}

je te partage le lien : [goalLink]

t'as déjà une idée ou t'es encore en réflexion ?
```

---

## Instagram — Icebreakers (few-shots)

```
Hey {prospect_name}, j'ai vu {context_note} — tu travailles encore sur ça ?
```

```
Salut {prospect_name}, {context_note} — t'es sur quel type de projet en ce moment ?
```

---

## Relances (tous canaux)

### LinkedIn / Instagram — neutre

```
{prospect_name}, je reviens vers toi au cas où mon message s'était perdu

tu as eu le temps d'y jeter un œil ?
```

### WhatsApp — J+3

```
Hello {prospect_name}, t'as eu le temps de jeter un œil au lien que je t'avais envoyé

tu veux qu'on avance ensemble ou ce n'est plus le bon moment
```

### WhatsApp — neutre

```
{prospect_name}, je reviens vers toi au cas où mon message était passé à la trappe

tu es encore intéressé par [thème] ?
```

---

## Notes d'invitation LinkedIn (few-shots, max 300 caractères)

```
Salut {prospect_name}, j'ai vu [contexte bref] — j'aimerais qu'on soit connectés pour échanger.
```

```
{prospect_name}, ton profil m'a semblé pertinent sur [thème]. Heureux de connecter.
```

---

> Ajouter ici les templates validés après `/memory-save` — remplacer les exemples par les versions qui fonctionnent vraiment.
