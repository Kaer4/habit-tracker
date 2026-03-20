# Skill: FastAPI Endpoint Implementation

## Purpose
Implement new FastAPI endpoints and their models/schemas based on a feature plan.

## Steps
1. Identify which router file to change or create (e.g., app/routers/habits.py).
2. Ensure matching Pydantic schemas exist (create under app/schemas if needed).
3. Implement endpoints with:
   - Proper dependency injection for DB session and active user context.
   - Proper response models and error handling.
4. Update tests or add basic tests if a tests/ directory exists.

## Constraints
- Don’t change existing endpoints’ signatures unless explicitly requested.
- Keep business logic in services, not routers, when possible.
- For auth extensibility later:
  - Keep endpoints depending on an `active_user` dependency (e.g., `active_user: User = Depends(get_active_user)`), not directly on JWT/cookies.
  - When you add authentication later, swap only the implementation of `get_active_user()` to read from JWT and resolve the real user; keep the endpoint parameters/types stable.
