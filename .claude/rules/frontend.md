---
paths:
  - "frontend/src/**"
---

# Frontend Rules

Code-level conventions for everything under `frontend/src/`. The SRP
principles behind them live in `.claude/rules/solid-principles.md`. See
`.claude/rules/structure.md` for the full directory layout.

## Component structure

- Split a component that both fetches/owns data and renders a
  non-trivial view into a container (data/state) and a presentational
  component (rendering).
- Keep API access, state management and presentation in separate
  modules/files rather than inline in one large component.

## Alignment with the backend

- Frontend types/DTOs for a feature mirror the DTOs defined in that
  feature's `specs/<feature-name>/design.md` (Application Layer § DTOs and
  API Layer § Endpoints) — don't invent a divergent shape client-side.
- Domain terms in component, prop and state names match
  `docs/domain/glossary.md` and the entity/value-object names in the
  relevant design.md, so a concept isn't named two ways across backend
  and frontend.

## Project conventions

- `frontend/package.json` is the single source of truth for dependencies
  — don't add a package without checking it's actually needed and
  updating that file.
