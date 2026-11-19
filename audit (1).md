# LevelUp — Project Audit

**Audit date:** 2026-11-19  
**Application:** LevelUp — Gamified Fitness & Personal Growth RPG  
**Stack described:** React 18, TypeScript, Vite, Tailwind CSS, Supabase (Postgres + Auth)

> **Scope note:** This document is based on the project description and audit findings supplied with this request. The complete source repository, database migrations, Supabase policies, and deployment configuration were not provided for independent verification. Findings below should therefore be treated as reported project claims, not as a fresh penetration test or code audit.

## 1. Project Overview

LevelUp is described as a full-stack, multi-user application that gamifies fitness and personal growth. Users earn XP for real-world activities such as workouts, sleep, hydration, and study; level up five RPG stats; complete daily quests; and receive rule-based coaching insights.

### Main screens

| Screen | Purpose |
|---|---|
| Auth | Email/password sign-up and sign-in |
| Onboarding | First-run profile setup, goals, sleep target, and available time |
| Dashboard | XP bar, level, stats, daily quests, coaching insights, and motivation |
| Workout | Weekly training split, guided execution, and workout logging |
| Schedule | Daily schedule items |
| Growth | Goals, habits, body metrics, charts, and consistency heatmap |
| Profile | Account settings and progress overview |

## 2. Architecture

- **Frontend:** React single-page application using Vite, TypeScript, and Tailwind CSS; `@/` is described as an alias for `src/`.
- **Authentication:** Supabase Auth with email/password. Session handling is described as using `onAuthStateChange` and a deadlock guard in `src/lib/auth.tsx`.
- **Database:** Supabase Postgres with 12 application tables and Row-Level Security (RLS).
- **Gamification logic:** `src/lib/gamification.ts` contains XP rewards, stat boosts, level progression, quest generation, badge thresholds, and projections.
- **Coaching logic:** `src/lib/coach.ts` contains rule-based coaching insights.
- **Utilities:** `src/lib/utils.ts` contains streak, consistency, and level calculations.
- **Charts:** Custom SVG chart components.
- **Demo data:** `src/lib/demoData.ts` supplies sample data for new accounts.

### Database tables reported

`profiles`, `workouts`, `workout_logs`, `daily_logs`, `goals`, `habits`, `habit_logs`, `quests`, `schedule_items`, `body_metrics`, `badges`, and `ai_insights`.

## 3. Security Review — Reported Findings

The supplied audit reports the following controls. They have not been independently tested as part of preparing this file.

### 3.1 Row-Level Security — Reported PASS

The supplied audit states:
- RLS is enabled on all 12 tables.
- Each table has owner-scoped SELECT, INSERT, UPDATE, and DELETE policies for authenticated users.
- Policies use `auth.uid() = user_id`, or `auth.uid() = id` for profiles.
- `profiles.id` references `auth.users(id)` with `ON DELETE CASCADE`.
- User ID columns default to `auth.uid()`.
- `daily_logs` has a unique constraint on `(user_id, date)`.

**Verification recommended:** Inspect every migration and test access using two separate authenticated accounts to confirm users cannot read or modify each other's rows.

### 3.2 Authentication — Reported PASS

The supplied audit states:
- Supabase Auth manages credentials and sessions.
- Screens are gated by session and onboarding completion.
- Only the public Supabase anon key is exposed in the browser.

**Verification recommended:** Check route guards, session expiry behavior, password reset flows, and ensure no service-role key or other secret is included in client bundles.

### 3.3 Findings and recommendations

| # | Severity as reported | Finding | Recommendation |
|---|---|---|---|
| 1 | Low | Users may be able to update XP, level, and stat columns through the profile update policy. | Move XP/stat changes into a trusted Postgres function or Edge Function; restrict direct client updates. |
| 2 | Low | Badge rows are reportedly inserted by the client. | Award badges server-side or validate awards in a trusted function. |
| 3 | Low | Numeric daily-log values may accept unbounded client input. | Add database CHECK constraints and sensible input validation. |
| 4 | Info | Coaching insights are computed client-side. | If external AI services are added, call them through an Edge Function and keep API keys server-side. |
| 5 | Info | No custom auth rate limiting is described. | Review Supabase Auth rate-limit settings and tune them for the expected public usage. |

The supplied audit reports no critical or high-severity issues. This statement has not been independently verified.

## 4. Code Quality

### Strengths reported
- Separation between pages, components, libraries, and types.
- Domain logic described as pure TypeScript and suitable for unit testing.
- Shared TypeScript types.
- Custom SVG charts without a chart-library dependency.
- Consistent database policy and schema conventions.

### Improvement opportunities
- Add unit tests for gamification and coaching functions.
- Consider splitting large Workout and Dashboard page components as the application grows.
- Add continuous integration for linting, type checking, and production builds.

## 5. Deployment Notes

The supplied project description specifies:
- `npm run build` generates the `dist/` directory.
- Hosting options include Vercel, Netlify, or GitHub Pages with SPA fallback support.
- Required environment variables:
  - `VITE_SUPABASE_URL`
  - `VITE_SUPABASE_ANON_KEY`
- Database migrations are stored in `supabase/migrations/`.

Never commit `.env` files containing credentials. Use the hosting provider's environment-variable settings for deployment.

## 6. Pre-Release Checklist

- [ ] Review all Supabase migrations and confirm RLS is enabled on every user-data table.
- [ ] Test cross-account data isolation with at least two test users.
- [ ] Restrict client-side updates to XP, level, stats, and badge awards.
- [ ] Add CHECK constraints for numeric daily-log fields.
- [ ] Add unit tests for XP, level, quest, badge, streak, and coaching logic.
- [ ] Add CI checks for lint, typecheck, and build.
- [ ] Verify environment variables are configured securely.
- [ ] Test sign-up, sign-in, sign-out, onboarding, and session restoration.
- [ ] Verify mobile layouts and accessibility.
- [ ] Confirm production hosting supports client-side route fallback.

## 7. Overall Assessment

Based on the supplied project description, LevelUp has a clear full-stack architecture and a well-defined user journey: authentication, onboarding, daily activity logging, quests, and progress tracking. The highest-priority hardening tasks are to make XP and badge awards server-authoritative, validate user-submitted numeric data at the database layer, and add automated tests and CI.

This document is a source-based project audit summary, not a certification of security or production readiness.
