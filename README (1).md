# ⚡ LevelUp — Don't just track your life. Level it up.

LevelUp is a full-stack fitness and personal-growth app that turns self-improvement into an RPG. Users earn XP for real-world actions, level up five character stats, complete daily quests, and receive personal coaching insights.

![Status](https://img.shields.io/badge/status-active-brightgreen)
![React](https://img.shields.io/badge/React-18-blue)
![TypeScript](https://img.shields.io/badge/TypeScript-5-blue)
![Supabase](https://img.shields.io/badge/Backend-Supabase-green)

## Features

- **RPG progression:** Workouts, sleep, water, and study sessions earn XP and boost Strength, Health, Discipline, Energy, and Consistency.
- **Daily quests:** An automatically generated daily quest board, with bonus XP for completing all quests.
- **Personal coach:** A rule-based engine analyzes recent logs and produces prioritized insights.
- **Workout system:** Weekly training split, guided exercises, and workout history.
- **Goals and habits:** Goal tracking with deadlines, habit tracking, and streaks.
- **Analytics:** Custom SVG charts, a consistency heatmap, and weight projections based on consistency.
- **Badges:** Achievement milestones such as first workout, a seven-day streak, and 100 workouts.

## Tech Stack

| Layer | Technology |
|---|---|
| Front end | React 18, TypeScript, Vite, Tailwind CSS |
| Icons | Lucide React |
| Charts | Custom SVG components |
| Backend | Supabase (Postgres, Auth, Row-Level Security) |

## Getting Started

### Prerequisites

- Node.js 18+
- A [Supabase](https://supabase.com) project

### 1. Clone and install

```bash
git clone https://github.com/YOUR_USERNAME/levelup.git
cd levelup
npm install
```

### 2. Configure environment variables

Create a `.env` file in the project root:

```env
VITE_SUPABASE_URL=your-supabase-project-url
VITE_SUPABASE_ANON_KEY=your-supabase-anon-key
```

Find these values in your Supabase dashboard under **Settings → API**. Do not commit private service-role keys or other secrets.

### 3. Apply the database schema

Apply the SQL migrations in `supabase/migrations/` to your Supabase project using the Supabase CLI or the dashboard SQL Editor. The supplied project audit describes 12 application tables with Row-Level Security policies.

### 4. Run the app

```bash
npm run dev
```

Open the local URL printed by Vite, create an account, and complete onboarding.

## Scripts

| Command | Description |
|---|---|
| `npm run dev` | Start the development server |
| `npm run build` | Create the production build in `dist/` |
| `npm run preview` | Preview the production build |
| `npm run lint` | Run ESLint |
| `npm run typecheck` | Run TypeScript checks |

## Project Structure

```text
src/
├── components/          # Auth, onboarding, navigation, charts, UI
├── pages/               # Dashboard, Workout, Schedule, Growth, Profile
├── lib/
│   ├── auth.tsx         # Supabase session provider
│   ├── gamification.ts  # XP engine, quests, badges, projections
│   ├── coach.ts         # Rule-based coaching insights
│   ├── demoData.ts      # Sample data
│   └── utils.ts         # Streaks, consistency, level calculations
└── types/               # Shared TypeScript types
supabase/
└── migrations/          # Database schema and RLS policies
```

## Database and Security

The supplied project audit lists these 12 tables:

`profiles`, `workouts`, `workout_logs`, `daily_logs`, `goals`, `habits`, `habit_logs`, `quests`, `schedule_items`, `body_metrics`, `badges`, `ai_insights`.

The audit states that Row-Level Security is enabled and owner-scoped policies are used. Review `audit.md` for the reported findings and recommended hardening work.

## Deployment

Build the app:

```bash
npm run build
```

Deploy the generated `dist/` directory to a supported static host such as Vercel or Netlify. Configure `VITE_SUPABASE_URL` and `VITE_SUPABASE_ANON_KEY` in the host's environment settings. For GitHub Pages, configure an SPA fallback and the correct Vite `base` path for the repository.

## License

Add a license file if you intend to publish this project under an open-source license. No license was supplied with the project details.
