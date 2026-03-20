# Skill: Fullstack Feature Implementation

## Purpose
Given a feature plan file under plans/, implement both backend and frontend changes in a safe, incremental way.

## Steps
1. Read the relevant plan file (e.g., plans/habits.md).
2. Summarize:
   - Backend endpoints, models, and services required.
   - Frontend pages, components, and state changes required.
3. Propose a step-by-step implementation order:
   - DB models and migrations.
   - Backend endpoints.
   - Frontend types/api client.
   - Frontend UI and integration.
4. For each step:
   - Show a short diff or file-level change description.
   - Apply minimal, focused edits to the codebase.
   - Verify imports and types.

## Constraints
- Never rename or move files without explicitly stating that you are doing so.
- Prefer small, incremental changes over large refactors.
- Keep TypeScript types and Pydantic/SQLModel models in sync.
