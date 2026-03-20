# Plan: Habits & Checkins

## Objective
Build a habit tracker where each habit has a grid-based completion UI and a simple streak/stat view. MVP is **single-user** (no auth UI), but code should be structured so adding auth later only swaps the “active user” resolver.

## MVP Auth / Active User
- Mode: **No login/register**.
- Backend must still use an `active_user` dependency concept:
  - Example: `Depends(get_active_user)` returns the single MVP user.
  - Later auth migration: keep endpoint function signatures stable and swap only `get_active_user()` to JWT-based `get_current_user()`.

## Date & Grid Rules (Critical)
### Date format
- All dates are stored and exchanged as `YYYY-MM-DD` (no time component).

### Time basis / timezone assumption
- MVP assumption: interpret `YYYY-MM-DD` using the server’s configured timezone consistently.
- If you must choose a default: treat the UI date as the server date (simpler MVP). Add a comment where timezone would be configurable later.

### Grid modes -> computed date range
Let `anchor_date` be the date the user is viewing (default: today). For each `grid_mode`, compute:
- `week`
  - Range: 7 days starting on **Monday** (inclusive) through Sunday (inclusive).
- `month`
  - Range: from the Monday of the first week that intersects the month through the Sunday of the last week that intersects the month.
  - This produces a consistent rectangular grid.
- `year`
  - Range: all days in the calendar year, expanded to full weeks using the same Monday-start rule.
  - Implementation-friendly representation: a contributions-style grid where each cell is one day; rows are weeks, columns are weekdays (Mon..Sun).

### Check-in records must be view-invariant
- Switching `grid_mode` changes the visible range and layout only.
- The same stored checkins must appear in any mode whose range includes their dates.

## Data Model (MVP)
### Habit
- Fields: `id`, `user_id`, `title`, `description?`, `frequency`, `start_date`, `archived`, `created_at`
- MVP frequency handling:
  - Option A (recommended for MVP): treat frequency as metadata; allow checkins for any day >= `start_date`.
  - Option B: enforce frequency in the enabled/disabled grid cells only.
  - Choose one and be consistent.

### HabitCheckin
- Fields: `id`, `habit_id`, `date` (`YYYY-MM-DD`), `completed` (boolean)
- Uniqueness / dedupe constraint (recommended):
  - One row per `(habit_id, date)`.
  - This makes toggling/upserting idempotent and prevents duplicates.

## API Contracts (Backend)
Notes for Claude implementation:
- Use `active_user` on every endpoint that reads/writes habits/checkins.
- Validate `from <= to` and parse dates from `YYYY-MM-DD`.
- Treat missing checkins in a date range as `completed = false` for UI rendering (unless you decide to store explicit false rows).

### Habits
`GET /habits`
- Returns array of habits for the active user.
- Response fields (minimum): `id`, `title`, `archived`, `start_date`, `frequency`
- Optional: streak snippet (can be computed or omitted in MVP).

`POST /habits`
- Body: `{ title, frequency, start_date, description? }`
- Response: created habit `{ id, ... }`

`PATCH /habits/{id}`
- Body (all optional): `{ title?, frequency?, start_date?, description?, archived? }`
- Response: updated habit

`DELETE /habits/{id}`
- Choose one approach and document it:
  - Soft delete (recommended): set `archived=true`
  - Hard delete: remove row
- Response: `{ success: true }` or updated habit.

### Checkins
`POST /habits/{id}/checkins`
- Body supports either explicit set or toggle:
  - Explicit set: `{ date: "YYYY-MM-DD", completed: true|false }`
  - Toggle (optional convenience): `{ date: "YYYY-MM-DD" }`
- Response (minimum): `{ habit_id, date, completed }`
- Required semantics:
  - If explicit `completed` is provided:
    - Upsert to that value for `(habit_id, date)`.
  - If toggle mode:
    - If a row exists: flip it.
    - If missing: create with `completed=true`.
- Ensure the operation is idempotent for explicit-completed calls.

`GET /habits/{id}/checkins?from=YYYY-MM-DD&to=YYYY-MM-DD`
- Response fields (minimum):
  - Array of `{ date, completed }`
- Range semantics:
  - `from` and `to` are inclusive.
  - Return only checkins that exist; UI treats missing dates as not completed.

### Streaks & Stats (MVP)
`GET /habits/{id}/streak?from=YYYY-MM-DD&to=YYYY-MM-DD`
- Response:
  - `current_streak`: consecutive completed days ending at `to` (after applying `start_date` boundary)
  - `longest_streak`: max consecutive completed days within `[from,to]`
- Decide and implement explicitly:
  - `start_date` boundary rule: days before `start_date` are excluded from streak calculations.

## Backend Implementation Checklist (Agent Steps)
1. DB migration + constraints
   - Add tables/schemas for `Habit` and `HabitCheckin`
   - Add uniqueness constraint for `(habit_id, date)`
2. Service layer
   - Implement habit create/update/archive
   - Implement checkin upsert/toggle
   - Implement checkin range query
   - Implement streak calculation using date iteration + ordered checkins
3. Routers
   - Implement the API routes listed above
   - Ensure every route uses `active_user` dependency
4. Add tests (if you have a test directory)
   - Upsert/toggle correctness + no duplicates
   - Streak correctness across gaps

## Frontend Implementation Checklist (Grid-first UX)
### Habit detail page
1. Grid mode selector: `week` / `month` / `year`
2. Anchor navigation (prev/next)
   - Prev/Next shifts `anchor_date` by grid mode (week: +/-7 days, month: +/-1 month, year: +/-1 year)
3. Render grid cells
   - Compute `rangeStart/rangeEnd` from `anchor_date + grid_mode`
   - Fetch checkins for `[rangeStart, rangeEnd]`
   - Render one cell per day in the range
4. Cell interaction
   - On click, toggle completion for that specific day via API
   - Optimistically update the cell, then reconcile by refetch or using returned checkin

### Dashboard page (minimal MVP)
- List habits with:
  - Habit title
  - Start date / archived state
  - “Quick check” for `today` (single-cell checkbox)

## UX & Accessibility Notes
- One interaction per cell; immediate visual feedback.
- Keyboard accessibility:
  - Tab order for cells
  - Use an accessible button/role for each day cell

## Acceptance Criteria (Must Pass)
- Single-user mode works (no login/register screens).
- Grid modes (`week`/`month`/`year`) only change view range/layout; completion history remains consistent.
- Toggling the same day repeatedly does not create duplicates and always results in correct stored completion state.
- Streaks are computed correctly using completed days and excluding days before `start_date`.
- When there are no checkins in a visible range, the grid renders as all uncompleted without errors.
