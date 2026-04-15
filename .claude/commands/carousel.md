---
description: Produit le script d’un carrousel LinkedIn (slides) puis guide la publication (texte intro + POST /posts ou découpe manuelle selon workflow utilisateur).
---
# Carousel — LinkedIn

Konect **`POST /posts`** accepte du **texte** (+ `mediaUrl` optionnel). Un carrousel multi-images peut nécessiter **plusieurs médias** selon les capacités produit : ce workflow sépare **rédaction** et **publication**.

## Quand activer

- « carrousel », « slides LinkedIn », « document carousel »

## Prérequis

- `memory/identity/brand.md`, `memory/identity/persona.md`

## Livrable principal

1. **Slide 1 — accroche** (texte court)
2. **Slides 2..N** — une idée par slide, bullets concis
3. **Dernière slide** — CTA aligné avec `offer.md`
4. **Légende / post d’accompagnement** — paragraphe d’introduction pour le champ `content` de `POST /posts`

## Publication

- Si une **seule image** carrousel exportée : `mediaUrl` + `content` intro.
- Sinon : publier l’intro via `POST /posts` et déléguer l’upload multi-images à l’app native / équipe selon process utilisateur.

## Workflow

1. Collecter l’objectif pédagogique et 3–5 points clés.
2. Générer le script slide par slide + légende.
3. Valider avec l’utilisateur.
4. Option : enregistrer dans Airtable **Contenu** (Type = Carousel, Statut = Brouillon / Planifié).
