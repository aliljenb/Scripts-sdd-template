# sdd-template

A template for Spec-Driven Development (SDD), in the style of AWS Kiro, that
works with [Claude Code](https://claude.com/claude-code) and
[Kiro CLI](https://kiro.dev). It scaffolds a Python (backend) / React (frontend)
project where every feature moves through an explicit, phase-gated
workflow — requirements → design → tasks → implementation → review —
and the backend is built following Domain-Driven Design (DDD) with strict
adherence to the Single Responsibility Principle (SRP).

## Workflow

Each feature gets its own spec directory under `specs/<feature-name>/`
containing `requirements.md`, `design.md`, and `tasks.md`. A phase file
must be explicitly marked `Approved` before the next phase's command may
run — the agent will refuse to skip ahead.

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
│   ├── skills/           # shared: one skill per phase (+ domain-model)
│   ├── rules/            # shared: steering docs & phase-gate rules
│   └── settings.json     # Claude Code permissions
├── .kiro/
│   ├── agents/           # Kiro: per-phase agents
│   └── prompts/          # Kiro: slash commands
├── AGENTS.md             # tool-neutral entry point / index
├── CLAUDE.md             # imports AGENTS.md for Claude Code
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
   building (vision, users, goals); domain terms go in
   `docs/domain/glossary.md` — the agent reads this before
   drafting requirements or design so it doesn't invent product intent.
2. Update `.claude/rules/tech.md` if the stack differs from the defaults.
3. Run `/spec-new <feature-name>` to start your first feature.

## Shared files and per-tool layers

Everything that defines the workflow is shared, so there is one copy to
maintain:

| Shared file(s) | What it holds | How each tool finds it |
|---|---|---|
| `AGENTS.md` | Project index | Kiro reads it directly; Claude Code through `CLAUDE.md` (`@AGENTS.md`) |
| `.claude/rules/*.md` | Rules | Claude Code natively (`paths` frontmatter scopes `backend` and `frontend`); Kiro via each agent's `file://` resources |
| `.claude/skills/*/SKILL.md` | Phase skills + templates | Claude Code natively; Kiro via each agent's resources |

Each tool then has a thin layer that picks the model and limits what each
phase may do:

| | Per-phase model | Per-phase file-write limits | How you run a phase |
|---|---|---|---|
| **Claude Code** | `model:` in each `SKILL.md` | No — instruction only | `/spec-design <feature>` |
| **Kiro CLI** | `model:` in `.kiro/agents/*.md` | Yes — `permissions` in each agent | `kiro-cli chat --agent sdd-design`, then `/spec-design <feature>` |

Both tools use the same model split:

| Phase | Model tier | Edits allowed | Shell |
|---|---|---|---|
| `/spec-new` | Haiku | `specs/**` | none |
| `/spec-requirements` | Opus | `specs/*/requirements.md` | none |
| `/spec-design` | Opus | `specs/*/design.md`, `docs/domain/glossary.md`, `docs/domain/bounded-contexts.md` | none |
| `/spec-tasks` | Sonnet | `specs/*/tasks.md` | none |
| `/implement-task` | Sonnet | any file | `pytest`, `ruff`, `mypy`, `git` pre-approved |
| `/review` | Opus | nothing (read-only) | `pytest`, `ruff`, `mypy`, `git diff`, `git log` pre-approved |

`specs/**/prompt(s).txt` can't be read in Claude Code or Kiro.

## Claude Code

The phase skills are slash commands and set their model with Claude Code
aliases (`haiku`, `sonnet`, `opus`). Things to know:

- A skill's `model` applies only to the turn that invokes it. For phases
  with back-and-forth (`/spec-requirements`, `/spec-design`), use
  `/model opus` for the session so follow-up turns stay on Opus.
- `/review` uses `context: fork`, so it runs in a subagent without the
  implementer's context.
- There are no per-phase write limits; the phase skills' "Only write to"
  rules are followed as instructions.

## Kiro CLI

Start the phase's agent, then run its prompt:

```bash
kiro-cli chat --agent sdd-design
> /spec-design my-feature
```

Each agent in `.kiro/agents/` loads only the rules its phase needs plus its
phase skill, and its `permissions` block limits file writes and shell
commands. The prompts in `.kiro/prompts/` refuse to run under the wrong
agent. The agents use the CLI V3 `permissions` format.

- Model IDs must match Kiro's model service; check with `/model` and update
  `model:` in `.kiro/agents/*.md` if they differ.
