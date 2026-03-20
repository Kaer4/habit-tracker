# Feature Plan: Habits & Checkins

## Goal
Users can manage habits and check them off for specific days, with streaks and basic stats.

## Backend Tasks
- Habit and HabitCheckin models.
- Endpoints:
  - GET /habits
  - POST /habits
  - PATCH /habits/{id}
  - DELETE /habits/{id}
  - POST /habits/{id}/checkins (toggle or explicit date)
  - GET /habits/{id}/checkins?from=&to=
  - GET /habits/{id}/streak
- Implement streak calculation given checkins.

## Frontend Tasks
- Dashboard page:
  - List habits with today’s completion checkbox and streak snippet.
  - “Add habit” modal/form.
- Habit detail page:
  - Calendar/heatmap of completions in grid format.
  - Current + longest streak and basic stats.

## UX Notes
- Prioritize fast daily check-in flow.
- Show visual feedback (e.g., progress bar, streak icon).
