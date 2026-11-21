# sdd-template

A template for Spec-Driven Development (SDD) with [OpenCode](https://opencode.ai),
in the style of AWS Kiro. It scaffolds a Python (backend) / React (frontend)
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
reason to change. See `.opencode/rules/backend.md` and
`.opencode/rules/frontend.md` for the enforced conventions.

## Project structure

```
<Project>/
├── .opencode/
│   ├── commands/         # /spec-new, /spec-requirements, /spec-design,
│   │                     #   /spec-tasks, /implement-task, /review
│   ├── skills/           # one skill per command (+ domain-model)
│   └── rules/            # steering docs & phase-gate rules
├── AGENTS.md             # entry point / index
├── opencode.json         # rule loading, permissions, per-phase agents
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

See `.opencode/rules/structure.md` for the full layout and conventions, and
`.opencode/rules/tech.md` for the stack (Python 3.12+, pip, pytest, ruff,
mypy).

## Getting started

1. Fill in `.opencode/rules/product.md` with the product you're actually
   building (vision, users, goals); domain terms go in
   `docs/domain/glossary.md` — the agent reads this before
   drafting requirements or design so it doesn't invent product intent.
2. Update `.opencode/rules/tech.md` if the stack differs from the defaults.
3. Run `/spec-new <feature-name>` to start your first feature.

## How the workflow is enforced in OpenCode

Each command in `.opencode/commands/` selects a dedicated agent and model,
then inlines the skill of the same name from `.opencode/skills/` with
OpenCode's shell injection (``!`cat .opencode/skills/<name>/SKILL.md` ``).
The skill text is pasted in before the model runs, so loading it never
depends on the model choosing to call the `skill` tool, and the skill files
remain the single source of truth:

| Command | Agent | Model | What the agent may change |
|---------|-------|-------|---------------------------|
| `/spec-new` | `sdd-spec-new` | `anthropic/claude-haiku-5-5` | `specs/**`; no shell |
| `/spec-requirements` | `sdd-requirements` | `anthropic/claude-opus-5-5` | `specs/*/requirements.md` only; no shell |
| `/spec-design` | `sdd-design` | `anthropic/claude-opus-5-5` | `specs/*/design.md`, `docs/domain/glossary.md`, `docs/domain/bounded-contexts.md`; no shell |
| `/spec-tasks` | `sdd-tasks` | `anthropic/claude-sonnet-5-5` | `specs/*/tasks.md` only; no shell |
| `/implement-task` | `sdd-implement` | `anthropic/claude-sonnet-5-5` | any file; `pytest`, `ruff`, `mypy`, `git` pre-approved, other shell asks |
| `/review` | `sdd-review` | `anthropic/claude-opus-5-5` | nothing (read-only); `pytest`, `ruff`, `mypy`, `git diff`, `git log` pre-approved |

- The phase skills are denied to every other agent (including `build` and
  `plan`), so they only run when you type the command.
- All rule files in `.opencode/rules/` load on every session via the
  `instructions` entry in `opencode.json`.
- `specs/**/prompt(s).txt` cannot be read, and the built-in `browser` tool
  and any `claude-in-chrome` MCP tools are denied.

## Changing the model for a command

Each command sets its model in the `model:` field of its own file's
frontmatter in `.opencode/commands/`. Skills have no model setting of their
own. A skill runs on whatever model the command that inlines it uses, so to
change the model for a skill, edit its command:

| Command / skill | File to edit |
|-----------------|--------------|
| `/spec-new` | `.opencode/commands/spec-new.md` |
| `/spec-requirements` | `.opencode/commands/spec-requirements.md` |
| `/spec-design` | `.opencode/commands/spec-design.md` |
| `/spec-tasks` | `.opencode/commands/spec-tasks.md` |
| `/implement-task` | `.opencode/commands/implement-task.md` |
| `/review` | `.opencode/commands/review.md` |

For example, to run the design phase on Sonnet instead of Opus, change
`.opencode/commands/spec-design.md`:

```diff
 ---
 description: SDD - Generate design.md (DDD domain model + layered design) for a feature
 agent: sdd-design
-model: anthropic/claude-opus-5-5
+model: anthropic/claude-sonnet-5-5
 subagent: false
 ---
```

Notes:

- **Format:** `provider/model`, for example `anthropic/claude-sonnet-5-5`
  or `openai/gpt-5`. If the model offers variants (such as reasoning-effort
  levels), you can pick one by adding `#variant`, for example
  `anthropic/claude-opus-5-5#high`.
- **Finding valid IDs:** run `opencode models` to list the models your
  configured providers offer. You can add an optional provider filter, for
  example `opencode models anthropic`. The provider must be set up first,
  for example with `opencode auth login`.
- **Only change `model:`.** Leave the `agent:` line as it is. The agent
  (defined under `agents` in `opencode.json`) controls what each phase may
  edit and run, so changing the model keeps those restrictions in place.
- **Using one model everywhere:** delete the `model:` line from a command
  file. That command then uses the model currently selected in your
  OpenCode session (or the top-level `model` in `opencode.json`).
- Changes take effect the next time you run the command. If a command
  still uses the old model, restart OpenCode.
- After changing a model, update the **Model** column in the table above
  so the README still matches the commands.
