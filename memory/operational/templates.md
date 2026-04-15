# Templates de messages validés

Espace pour les **icebreakers**, **premiers messages**, **relances** validés par l’utilisateur. Les agents et `/icebreaker` s’y réfèrent.

## Règles générales KonectOS

- Valider avec l’utilisateur avant envoi en masse (> 5 messages)
- Premier message LinkedIn : `POST /messages` avec `to` (URL profil ou identifiant)
- Réponse : `chatId` + `content` (jamais inverser)
- Notes invitation LinkedIn : max 300 caractères (`POST /relations/invite` → champ `message`)

---

## Icebreaker LinkedIn (exemple à remplacer)

```
Salut {prospect_name}, {context_note}

j’aimerais échanger 2 minutes sur [sujet], ça te dit ?
```

---

## Icebreaker Instagram (exemple à remplacer)

```
Hey {prospect_name}, j’ai vu [contexte réel]. Tu travailles encore sur [thème] ?
```

---

## Relance neutre

```
{prospect_name}, je me permets un petit bump au cas où mon message était passé à la trappe
```
