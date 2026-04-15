---
description: Calendrier éditorial multi-plateforme (LinkedIn + Instagram) — lignes Airtable Contenus + rappels scheduledAt Konect.
---
# Content plan — Calendrier éditorial

Produit une **vue 2–4 semaines** : thèmes, formats, dates, plateformes, statuts — synchronisée avec la table **Contenus** Airtable.

## Quand activer

- « calendrier éditorial », « content plan », « planifier mes posts »

## Prérequis

- ICP + offre (`persona.md`, `offer.md`, `brand.md`)
- Table **Contenus** créée (`/onboarding`)

## Workflow

1. Objectifs de période (notoriété, leads, autorité).
2. Grille hebdo : mix LinkedIn / Instagram, formats (post court, carrousel, DM day, etc.).
3. Pour chaque ligne : **Titre**, **Date publication**, **Plateforme**, **Type**, **Statut** = Brouillon ou Planifié, **Texte** (outline ou copie complète), **scheduledAt** si planifié côté Konect.
4. Insérer dans Airtable (`POST /v0/{base}/{table}`) par batch raisonnable.
5. Rappeler : publication réelle = `/post`, `/instagram-post` ou ajustement manuel selon médias.

## Lien Konect

Quand une ligne passe en **Planifié** avec date ISO : proposer la commande de publication correspondante pour créer l’action `post` dans la **queue** Konect.
