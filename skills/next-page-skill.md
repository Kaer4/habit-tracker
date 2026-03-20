# Skill: Next.js Page Implementation

## Purpose
Implement or update a Next.js App Router page to satisfy a UI/UX description.

## Input
- Target route path (e.g., app/app/page.tsx).
- UX description (layout, sections, data shown).
- Data requirements (what API calls, what props/state).

## Steps
1. Inspect existing page file if it exists.
2. Design component structure: main component, child components.
3. Implement or update the page:
   - Use TypeScript and React function components.
   - Use Tailwind for layout and styling.
4. If data fetching is needed:
   - Decide if this should be a server or client component.
   - Use existing `lib/apiClient.ts` or create a fetch helper.

## Constraints
- Avoid unnecessary libraries beyond React, Next.js, Tailwind.
- Keep layout simple and responsive.
- Respect existing design tokens/components when present.