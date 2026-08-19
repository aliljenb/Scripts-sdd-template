# sdd-template

A template for Spec-Driven Development (SDD) with [Claude Code](https://claude.com/claude-code),
in the style of AWS Kiro. It scaffolds a Python (backend) / React (frontend)
project where every feature moves through an explicit, phase-gated
workflow — requirements → design → tasks → implementation → review —
and the backend is built following Domain-Driven Design (DDD) with strict
adherence to the Single Responsibility Principle (SRP).

## Workflow

Each feature gets its own spec directory under `specs/<feature-name>/`
containing `requirements.md`, `design.md`, and `tasks.md`. A phase file
must be explicitly marked `Approved` before the next phase's command may
run — Claude Code will refuse to skip ahead.

| Step | Command | Produces |
|------|---------|----------|
| 1 | `/spec-new <feature-name>` | Scaffolds `specs/<feature-name>/` from the templates |
| 2 | `/spec-requirements <feature-name>` | `requirements.md` — user stories + acceptance criteria |
| 3 | `/spec-design <feature-name>` | `design.md` — DDD domain model + layered technical design |
| 4 | `/spec-tasks <feature-name>` | `tasks.md` — file-by-file implementation checklist |
| 5 | `/implement-task <feature-name> [task]` | Code, implemented against the approved plan |
| 6 | `/review <feature-name>` | An audit of the implementation against the spec |

No code is written in `src/` or `frontend/src/` until the Tasks phase is
approved — Requirements and Design are planning-only.

## Domain-Driven Design & SRP

Backend code is organized into DDD layers, each with one job:

- **`domain/`** — pure business logic (entities, value objects, domain
  events/exceptions, repository interfaces). Zero framework or
  infrastructure imports.
- **`application/`** — use cases (commands/queries) that orchestrate
  domain objects. No direct database/HTTP calls — only through
  repository ports.
- **`infrastructure/`** — adapters: ORM models, migrations, concrete
  repository implementations.
- **`api/`** — FastAPI routers, request/response schemas, DI wiring.

Every design and its implementation are checked against the Single
Responsibility Principle — each module/class should have exactly one
reason to change. See `.claude/rules/backend.md` and
`.claude/rules/frontend.md` for the enforced conventions.

## Project structure

```
<Project>/
├── .claude/
│   ├── skills/          # /spec-new, /spec-requirements, /spec-design,
│   │                     #   /spec-tasks, /implement-task, /review
│   ├── rules/            # steering docs & phase-gate rules
│   └── CLAUDE.md         # entry point / index
├── frontend/
│   └── src/
├── specs/
│   └── <feature-name>/
│       ├── requirements.md
│       ├── design.md
│       └── tasks.md
├── src/<python_module>/
│   ├── domain/
│   ├── application/
│   ├── infrastructure/
│   └── api/
└── tests/
```

See `.claude/rules/structure.md` for the full layout and conventions, and
`.claude/rules/tech.md` for the stack (Python 3.12+, pip, pytest, ruff,
mypy).

## Getting started

1. Fill in `.claude/rules/product.md` with the product you're actually
   building (vision, users, goals, glossary) — Claude reads this before
   drafting requirements or design so it doesn't invent product intent.
2. Update `.claude/rules/tech.md` if the stack differs from the defaults.
3. Run `/spec-new <feature-name>` to start your first feature.
