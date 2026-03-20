# Project: Habit Tracker

## Goal
A personal habit tracker web app for logged-in users. I want to have the displays in a large grid format that is minimalist and easy to use. Core features:
- Daily checklist of habits
- Streaks per habit
- Calendar/heatmap view of completions that can be viewed in increments of a day, week, month, or year
- Simple stats (current streak, longest streak, completion rate)

## Tech Stack
- Frontend: Next.js (App Router) + TypeScript + Tailwind CSS
- Backend: FastAPI + Python
- Database: PostgreSQL (with SQLModel/SQLAlchemy)
- Auth: Email + password with JWT (httpOnly cookie or Authorization header)

## Core Domain Model (high level)
- User: id, email, password_hash, created_at
- Habit: id, user_id, title, description, frequency, start_date, archived, created_at
- HabitCheckin: id, habit_id, date, completed
- (Optional) Reminder, StreakCache for performance

## High-Level Architecture
- Next.js: app/ for routing, components/ for UI, lib/ for API client and auth helpers.
- FastAPI: routers/ for auth, habits, checkins; models/ and schemas/ for DB and Pydantic; services/ for business logic.
- Frontend calls FastAPI JSON endpoints for all data, using JWT for auth.

## UX Overview
- Auth: login/register pages.
- Main dashboard: list of habits, today’s completion checkboxes, quick add.
- Habit detail page: streak info + calendar/heatmap.
- Basic settings page.

## Non-Goals (for now)
- Social/sharing features
- Complex gamification
- Cross-platform mobile app
