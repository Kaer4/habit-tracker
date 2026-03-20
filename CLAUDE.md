# Project: Habit Tracker

## Goal
A personal habit tracker web app for a single user (authentication deferred). Core features:
- Daily checklist of habits
- Grid-based completion view for each habit (default `week`, user can switch to `month` or `year`)
- Streaks per habit
- Calendar/heatmap view of completions (later)
- Simple stats (current streak, longest streak, completion rate)

## Tech Stack
- Frontend: Next.js (App Router) + TypeScript + Tailwind CSS
- Backend: FastAPI + Python
- Database: PostgreSQL (with SQLModel/SQLAlchemy)
- Auth (future): Email + password with JWT (httpOnly cookie or Authorization header) for multi-user support

## Core Domain Model (high level)
- User: id, created_at (single-user MVP; add email/password fields later for auth)
- Habit: id, user_id, title, description, frequency, start_date, archived, created_at
- HabitCheckin: id, habit_id, date, completed
- (Optional) Reminder, StreakCache for performance

## High-Level Architecture
- Next.js: app/ for routing, components/ for UI, lib/ for API client and auth helpers.
- FastAPI: routers/ for habits and checkins; models/ and schemas/ for DB and Pydantic; services/ for business logic.
- Frontend calls FastAPI JSON endpoints for all data. For now, use an "active user" dependency that returns the single MVP user; when adding auth later, swap that dependency to JWT-backed `get_current_user()`.

## UX Overview
- Main dashboard: list of habits, selectable grid mode (`week`/`month`/`year`) to mark each habit done per day, quick add.
- Habit detail page: streak info (and calendar/heatmap view later).
- Basic settings page.

## Non-Goals (for now)
- Social/sharing features
- Complex gamification
- Cross-platform mobile app
