# Project Index

This is a Spec-Driven Development (SDD) template project. Features are
built through a phase-gated workflow, and backend code follows
Domain-Driven Design with strict Single Responsibility.

## Workflow

Each feature moves through phases in order, one spec directory per
feature under `specs/<feature-name>/`. Every phase file must be
explicitly approved (its `## Status` checklist marked `Approved`) before
the next phase's command may run.

1. `/spec-new <feature-name>` — scaffold `specs/<feature-name>/`
2. `/spec-requirements <feature-name>` — write user stories + acceptance
   criteria
3. `/spec-design <feature-name>` — write the DDD domain model and layered
   design
4. `/spec-tasks <feature-name>` — break the design into a file-by-file
   task checklist
5. `/implement-task <feature-name> [task]` — implement tasks against the
   approved plan
6. `/review <feature-name>` — check the implementation against the spec

Full phase-gate rules: `.opencode/rules/sdd-workflow.md`.

## Rules

Read these before working in this repo:

- `.opencode/rules/product.md` — what this product is and why (fill in per project)
- `.opencode/rules/tech.md` — approved stack; ask before adding anything not listed
- `.opencode/rules/structure.md` — repo layout and DDD directory conventions
- `.opencode/rules/sdd-workflow.md` — phase-gate rules
- `.opencode/rules/backend.md` — DDD/SRP rules for `src/**/*.py`
- `.opencode/rules/frontend.md` — SRP rules for `frontend/src/**/*`
- `.opencode/rules/testing.md` — testing strategy, including Playwright for applicable UI tests
- `.opencode/rules/domain-driven-design.md` — when to use which DDD pattern; layering
- `.opencode/rules/solid-principles.md` — SRP rules

All files in `.opencode/rules/` are loaded automatically through the
`instructions` entry in `opencode.json`. `backend.md` and `frontend.md`
apply only to the paths named in their Scope line.

## Enforcement

Each workflow command (`.opencode/commands/*.md`) runs under its own
agent and model, and inlines the skill of the same name from
`.opencode/skills/` into the prompt (via `!`cat …``), so the skill's
instructions are always present before the agent starts. The phase skills (`spec-new`,
`spec-requirements`, `spec-design`, `spec-tasks`, `implement-task`,
`review`) are denied to every other agent, so they only run when the
user types the command. Each phase agent's permissions restrict which
files it may edit and which shell commands it may run; do not try to
work around a denied permission — stop and tell the user instead.

## Principles

- **Domain-Driven Design**: business logic lives in `domain/`, free of
  framework/infrastructure concerns; `application/` orchestrates it;
  `infrastructure/` and `api/` are the outermost, framework-facing
  layers. See `structure.md` and `backend.md`.
- **Single Responsibility**: every module/class has exactly one reason to
  change. Design docs must state each new module's single responsibility;
  implementation and review both check code against that statement.
