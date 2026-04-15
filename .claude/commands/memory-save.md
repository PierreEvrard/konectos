---
description: Persister en mémoire les apprentissages de session (lessons, synthèse, ajustements prompts).
---
# Memory save — Sauvegarder les apprentissages

Écrit les apprentissages **sans écraser** tout le fichier : ajouter une entrée datée dans la bonne section.

## Quand activer

- « sauvegarde ça », « retenir », `/memory-save`
- Fin de session si observations utiles

## Cibles possibles

| Type d’apprentissage | Fichier |
|----------------------|---------|
| Ce qui marche / pas en prospection ou DM | `memory/knowledge/lessons.md` |
| Ajustement ton / règles agents | `memory/operational/agent-prompts.md` (nouvelle sous-section) |
| Template message validé | `memory/operational/templates.md` |
| Décision stratégique courte | `memory/brain.md` → « Dernières actions notables » |

## Workflow

1. Demander en une phrase : *quoi* sauvegarder et *où* (si ambigu).
2. Ouvrir le fichier cible, **ajouter** une entrée avec date ISO.
3. Confirmer à l’utilisateur l’extrait ajouté.

## Règle

Ne pas supprimer l’historique existant sans accord explicite.
