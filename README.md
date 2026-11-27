# sdd-template

A template for Spec-Driven Development (SDD), in the style of AWS Kiro, that
works with [OpenCode](https://opencode.ai), [Claude Code](https://claude.com/claude-code),
[GitHub Copilot](https://github.com/features/copilot) and [Kiro CLI](https://kiro.dev). It scaffolds a Python (backend) / React (frontend)
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
reason to change. See `.github/instructions/backend.instructions.md` and
`.github/instructions/frontend.instructions.md` for the enforced conventions.

## Project structure

```
<Project>/
├── .claude/
│   ├── skills/           # shared: one skill per phase (+ domain-model)
│   ├── rules ->          # symlink to .github/instructions (Claude Code)
│   └── settings.json     # Claude Code permissions
├── .github/
│   ├── instructions/     # shared: steering docs & phase-gate rules
│   └── agents/           # Copilot: per-phase agents
├── .kiro/
│   ├── agents/           # Kiro: per-phase agents
│   └── prompts/          # Kiro: slash commands
├── .opencode/
│   └── commands/         # OpenCode: slash commands
├── AGENTS.md             # tool-neutral entry point / index
├── CLAUDE.md             # imports AGENTS.md for Claude Code
├── opencode.json         # OpenCode: rule loading, permissions, per-phase agents
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

See `.github/instructions/structure.instructions.md` for the full layout and conventions, and
`.github/instructions/tech.instructions.md` for the stack (Python 3.12+, pip, pytest, ruff,
mypy).

## Getting started

1. Fill in `.github/instructions/product.instructions.md` with the product you're actually
   building (vision, users, goals); domain terms go in
   `docs/domain/glossary.md` — the agent reads this before
   drafting requirements or design so it doesn't invent product intent.
2. Update `.github/instructions/tech.instructions.md` if the stack differs from the defaults.
3. Run `/spec-new <feature-name>` to start your first feature.

## Shared files and per-tool layers

Everything that defines the workflow is shared, so there is one copy to
maintain:

| Shared file(s) | What it holds | How each tool finds it |
|---|---|---|
| `AGENTS.md` | Project index | OpenCode, Copilot and Kiro read it directly; Claude Code through `CLAUDE.md` (`@AGENTS.md`) |
| `.github/instructions/*.instructions.md` | Rules | OpenCode via `instructions` in `opencode.json`; Copilot natively (`applyTo`); Claude Code via the `.claude/rules` symlink (`paths`); Kiro via each agent's `file://` resources |
| `.claude/skills/*/SKILL.md` | Phase skills + templates | Claude Code and OpenCode natively; Copilot as `/` commands; Kiro via each agent's resources; OpenCode commands also inline them with `!cat` |

Each tool then has a thin layer that picks the model and limits what each
phase may do:

| | Per-phase model | Per-phase file-write limits | How you run a phase |
|---|---|---|---|
| **OpenCode** | `model:` in `.opencode/commands/*.md` | Yes — `agents` permissions in `opencode.json` | `/spec-design <feature>` |
| **Claude Code** | `model:` in each `SKILL.md` | No — instruction only | `/spec-design <feature>` |
| **Copilot** | `model:` in `.github/agents/*.agent.md` | No — tools per agent, files by instruction | Select agent `sdd-design`, then `/spec-design <feature>` |
| **Kiro CLI** | `model:` in `.kiro/agents/*.md` | Yes — `permissions` in each agent | `kiro-cli chat --agent sdd-design`, then `/spec-design <feature>` |

All tools use the same model split:

| Phase | Model tier | Edits allowed | Shell |
|---|---|---|---|
| `/spec-new` | Haiku | `specs/**` | none |
| `/spec-requirements` | Opus | `specs/*/requirements.md` | none |
| `/spec-design` | Opus | `specs/*/design.md`, `docs/domain/glossary.md`, `docs/domain/bounded-contexts.md` | none |
| `/spec-tasks` | Sonnet | `specs/*/tasks.md` | none |
| `/implement-task` | Sonnet | any file | `pytest`, `ruff`, `mypy`, `git` pre-approved |
| `/review` | Opus | nothing (read-only) | `pytest`, `ruff`, `mypy`, `git diff`, `git log` pre-approved |

`specs/**/prompt(s).txt` can't be read in OpenCode, Claude Code and Kiro.

## OpenCode

Each command in `.opencode/commands/` selects a dedicated agent and model,
then inlines the shared skill with shell injection
(``!`cat .claude/skills/<name>/SKILL.md` ``), so loading it never depends
on the model choosing to call the `skill` tool. The phase skills are denied
to every other agent (including `build` and `plan`), and the built-in
`browser` tool and any `claude-in-chrome` MCP tools are denied.

To change a phase's model, edit `model:` in its command file
(`provider/model`, optionally `#variant`; run `opencode models` for valid
IDs). Leave `agent:` alone — it carries the phase's permissions.

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

## GitHub Copilot

Pick the phase's agent (`sdd-requirements`, `sdd-design`, …) in the agent
dropdown, then run the skill as a slash command. The agent sets the model
and tools; planning agents have no terminal access, and `sdd-review` can't
edit. Copilot can't limit edits to specific files, so the "Only write to"
rules are followed as instructions.

- Model names must match what your Copilot plan offers in the model picker;
  update `model:` in `.github/agents/*.agent.md` if they differ.
- VS Code's *Copilot* harness looks for skills in `.github/skills/`. If the
  `/spec-*` commands don't appear there, add a symlink:
  `ln -s ../.claude/skills .github/skills`.

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
