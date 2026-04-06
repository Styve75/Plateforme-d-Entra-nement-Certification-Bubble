# Architecture Technique — Certif Bubble

## Stack Retenu

| Couche | Technologie | Justification |
|---|---|---|
| Framework | Next.js 15 (App Router) | Full-stack JS/TS, Server Actions, SSR/SSG natif |
| Base de données | Supabase (PostgreSQL) | Relationnel, RLS, free tier généreux |
| Authentification | Supabase Auth | Intégré, email/password + OAuth |
| Temps réel | Supabase Realtime | Leaderboard live sans infrastructure supplémentaire |
| Hosting | Vercel | Intégration native Next.js, free tier |
| Langage | TypeScript | Type safety sur tout le projet |
| IA (Phase 3) | Anthropic Claude API | Appels directs depuis Server Actions |

## Hébergement & Coûts

- **Vercel** (free tier) : hosting Next.js, déploiements automatiques sur push
- **Supabase** (free tier) : 500 MB DB, 50 000 users auth, 2 GB bandwidth, realtime inclus
- **Coût Phase 1-2 : $0**
- Phase 3 : ajout facturation Anthropic API (pay-per-use)

## Architecture Applicative

```
certif-bubble/
├── app/                        # Next.js App Router
│   ├── (auth)/                 # Routes publiques (login, signup)
│   ├── (app)/                  # Routes protégées (dashboard, exam, leaderboard)
│   │   ├── dashboard/          # Vue thèmes + barres de progression
│   │   ├── training/[theme]/   # Entraînement par thème
│   │   ├── exam/               # Examen blanc (90 questions, chrono)
│   │   └── leaderboard/        # Classement global
│   └── api/                    # API routes (webhooks, etc.)
├── components/                 # Composants UI réutilisables
├── lib/
│   ├── supabase/               # Client Supabase (server + client)
│   ├── actions/                # Server Actions (logique métier)
│   └── utils/                  # Helpers (calcul score, etc.)
└── types/                      # Types TypeScript (DB schema)
```

## Schéma Base de Données (Supabase/PostgreSQL)

Conforme au PRD :

| Table | Champs clés |
|---|---|
| `users` | id, email, certification_type, global_accuracy, best_time |
| `questions` | id, text, image_url, category, certification, correct_answer, explanation_link |
| `theme_progress` | id, user_id (FK), category, completed_questions, total_questions |
| `exam_attempts` | id, user_id (FK), date, score, time_taken, is_final_exam |
| `leaderboard` | id, user_id (FK), rank, score_max, time_min |

**Sécurité :** Row Level Security (RLS) activé sur toutes les tables — chaque user ne voit que ses propres données (sauf leaderboard qui est public).

## Logique Métier Clé

### Verrouillage de l'Examen Final
```typescript
// lib/actions/exam.ts
async function canAccessFinalExam(userId: string): Promise<boolean> {
  const progress = await supabase
    .from('theme_progress')
    .select('completed_questions, total_questions')
    .eq('user_id', userId)

  return progress.data.every(
    (theme) => theme.completed_questions >= theme.total_questions
  )
}
```

### Calcul du Score
```typescript
const score = (correctAnswers / totalQuestions) * 100
const isCertifiedReady = score >= 97.3
```

## Flux d'Authentification

1. Signup → Supabase Auth crée l'user + sélection `certification_type` (Associate / Developer)
2. Supabase initialise les `theme_progress` pour chaque catégorie via un trigger PostgreSQL
3. Session gérée via cookies httpOnly (Next.js middleware)

## Realtime — Leaderboard (Phase V1)

```typescript
// Subscription côté client
supabase
  .channel('leaderboard')
  .on('postgres_changes', { event: '*', schema: 'public', table: 'leaderboard' }, 
    (payload) => updateUI(payload))
  .subscribe()
```

## Intégration IA (Phase V2)

```typescript
// lib/actions/ai-mentor.ts
import Anthropic from '@anthropic-ai/sdk'

const client = new Anthropic() // ANTHROPIC_API_KEY via env

async function generatePersonalizedPath(weakCategories: string[]) {
  const response = await client.messages.create({
    model: 'claude-opus-4-6',
    max_tokens: 1024,
    messages: [{ role: 'user', content: `...` }]
  })
  return response.content
}
```

## Variables d'Environnement

```env
NEXT_PUBLIC_SUPABASE_URL=
NEXT_PUBLIC_SUPABASE_ANON_KEY=
SUPABASE_SERVICE_ROLE_KEY=
ANTHROPIC_API_KEY=          # Phase 3 uniquement
```

## Décisions Techniques Notables

- **Server Actions** plutôt qu'une API REST séparée → moins de code, type-safe end-to-end
- **RLS Supabase** pour la sécurité des données plutôt qu'une logique de filtrage manuelle
- **Trigger PostgreSQL** pour l'initialisation automatique du `theme_progress` à l'inscription
- **`certification_type`** stocké sur le profil user pour filtrer les questions (Associate vs Developer)
