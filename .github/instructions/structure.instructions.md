---
applyTo: "**"
---

# Project Structure

> Update this file whenever new top-level files or directories are added.

```
<Project>/
├── .claude/
│   ├── skills/                        # shared phase skills (read by all four tools)
│   │   ├── domain-model/SKILL.md
│   │   ├── spec-new/SKILL.md
│   │   ├── spec-requirements/         # SKILL.md + template.md (requirements.md template)
│   │   ├── spec-design/               # SKILL.md + template.md (design.md template)
│   │   ├── spec-tasks/                # SKILL.md + template.md (tasks.md template)
│   │   ├── implement-task/SKILL.md
│   │   └── review/SKILL.md
│   ├── rules -> ../.github/instructions   # symlink so Claude Code loads the shared rules
│   └── settings.json                  # Claude Code permissions
├── .github/
│   ├── instructions/                  # shared rules (applyTo for Copilot, paths for Claude Code)
│   │   ├── sdd-workflow.instructions.md         # phase-gate rules
│   │   ├── product.instructions.md              # what/why
│   │   ├── tech.instructions.md                 # tech stack and constraints
│   │   ├── structure.instructions.md            # repo conventions, naming, layering
│   │   ├── domain-driven-design.instructions.md # DDD pattern & layering rules
│   │   ├── solid-principles.instructions.md     # SRP rules
│   │   ├── testing.instructions.md              # testing strategy (Playwright for UI)
│   │   ├── backend.instructions.md              # scoped: src/**/*.py, tests/**/*.py
│   │   └── frontend.instructions.md             # scoped: frontend/src/**
│   └── agents/                        # Copilot: one agent per phase (model + tools)
├── .kiro/
│   ├── agents/                        # Kiro: one agent per phase (model, rules, permissions)
│   └── prompts/                       # Kiro: /spec-new … /review slash commands
├── .opencode/
│   └── commands/                      # OpenCode: one per phase; picks agent + model, inlines the skill
├── AGENTS.md                          # tool-neutral index/entry point (OpenCode, Copilot, Kiro)
├── CLAUDE.md                          # imports AGENTS.md for Claude Code
├── opencode.json                      # OpenCode: instructions, permissions, per-phase agents
├── docs/
│   └── domain/
│       ├── glossary.md                # ubiquitous language
│       └── bounded-contexts.md        # bounded context map
├── frontend/
│   ├── public/                    # for images referenced by path/URL, not imported in components
│   ├── src/
│   │   ├── assets/
│   │   │   ├── icons/             # for icons imported directly into components
│   │   │   └── images/            # for images imported directly into components
│   │   └── components/
│   └── package.json               # single source of truth for frontend dependencies
├── specs/
│   ├── <feature-name-1>/
│   │   ├── requirements.md
│   │   ├── design.md
│   │   └── tasks.md
│   └── <feature-name-2>/
│       └── ...
├── src/<python_module>/
│   ├── domain/                    # pure business logic — zero framework/infra imports
│   │   ├── <aggregate_name>/
│   │   │   ├── entities.py        # entities (have identity)
│   │   │   ├── value_objects.py   # value objects (immutable, no identity)
│   │   │   ├── events.py          # domain events
│   │   │   ├── exceptions.py      # domain-specific exceptions
│   │   │   └── repository.py      # abstract repository interface (Protocol/ABC) — the "port"
│   │   └── shared/                # shared value objects/exceptions across aggregates
│   ├── application/                # use cases — orchestrate domain objects, no framework code
│   │   ├── <aggregate_name>/
│   │   │   ├── commands.py         # write use cases
│   │   │   ├── queries.py          # read use cases
│   │   │   └── dto.py              # data transfer objects crossing the boundary
│   ├── infrastructure/             # everything that talks to the outside world
│   │   ├── db/                     # ORM models, migrations, session management
│   │   └── repositories/           # concrete repo implementations — the "adapters"
│   └── api/                        # FastAPI routers, request/response schemas, DI wiring
│       └── cli/                    # command-line entrypoints (e.g. cron bots) — driving adapters
├── tests/
├── .gitignore
├── pyproject.toml
└── README.md
```

## Conventions

- All backend source code lives under `src/<python_module>/`
- All frontend source code lives under `frontend/src`
- Frontend containers (data/state) are separated from presentational
  components (rendering); API access and state management live in their
  own modules, not inline in components — see `frontend.md`
- Test files mirror the backend source tree
- One spec directory per feature — specs are never shared across features
- Steering documents live under `.github/instructions/`
