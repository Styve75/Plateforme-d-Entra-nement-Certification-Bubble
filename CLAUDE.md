# CLAUDE.md — Certif Bubble

## Aperçu de l'objectif du projet

Plateforme d'entraînement pour les certifications Bubble (Associate & Developer). L'objectif est d'atteindre un niveau de maîtrise de 97,3 % via un parcours d'apprentissage forcé par thème et une validation rigoureuse via un examen final de 90 questions.

## Aperçu de l'architecture globale

- **Framework :** Next.js 15 (App Router) avec TypeScript
- **Base de données :** Supabase (PostgreSQL) avec Row Level Security
- **Auth :** Supabase Auth (email/password + OAuth)
- **Hosting :** Vercel (free tier)
- **IA (Phase 3) :** Anthropic Claude API via Server Actions

Structure des routes :
- `(auth)/` — pages publiques (login, signup)
- `(app)/dashboard/` — thèmes + barres de progression
- `(app)/training/[theme]/` — entraînement par thème
- `(app)/exam/` — examen blanc (90 questions, chrono)
- `(app)/leaderboard/` — classement global

## Style visuel

- Interface claire et minimaliste
- Pas de mode sombre pour le MVP

## Contraintes et Politiques

- NE JAMAIS exposer les clés API au client — toutes les clés sensibles restent côté serveur (Server Actions, variables d'environnement sans préfixe `NEXT_PUBLIC_`)

## Dépendances

- Préférer les composants existants plutôt que d'ajouter de nouvelles bibliothèques UI

## Tests d'interface

À la fin de chaque développement qui implique l'interface graphique :
- Tester avec le skill `example-skills:webapp-testing` (Playwright), l'interface doit être responsive, fonctionnelle et répondre au besoin développé

## Documentation

- Spécifications fonctionnelles : [PRD.md](./PRD.md)
- Architecture technique : [ARCHITECTURE.md](./ARCHITECTURE.md)

## Context7

Utiliser automatiquement les outils MCP Context7 (`mcp__context7__resolve-library-id` puis `mcp__context7__query-docs`) pour toute génération de code, étapes de configuration/installation, ou documentation de bibliothèque/API — sans attendre une demande explicite.

## Langue

Toutes les spécifications doivent être rédigées en français, y compris les specs OpenSpec (sections Purpose et Scenarios). Seuls les titres de Requirements doivent rester en anglais avec les mots-clés SHALL/MUST pour la validation OpenSpec.
