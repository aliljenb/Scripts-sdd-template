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

Full phase-gate rules: `.github/instructions/sdd-workflow.instructions.md`.

## Rules

Read these before working in this repo:

- `.github/instructions/product.instructions.md` — what this product is and why (fill in per project)
- `.github/instructions/tech.instructions.md` — approved stack; ask before adding anything not listed
- `.github/instructions/structure.instructions.md` — repo layout and DDD directory conventions
- `.github/instructions/sdd-workflow.instructions.md` — phase-gate rules
- `.github/instructions/backend.instructions.md` — DDD/SRP rules for `src/**/*.py`
- `.github/instructions/frontend.instructions.md` — SRP rules for `frontend/src/**/*`
- `.github/instructions/testing.instructions.md` — testing strategy, including Playwright for applicable UI tests
- `.github/instructions/domain-driven-design.instructions.md` — when to use which DDD pattern; layering
- `.github/instructions/solid-principles.instructions.md` — SRP rules

These files are loaded automatically by every supported tool (OpenCode,
Claude Code, GitHub Copilot, Kiro). Design principles (DDD, SRP) live in
`domain-driven-design` and `solid-principles`; `backend` and `frontend`
hold only code-level conventions, defer to them, and are scoped to their
paths where the tool supports it.

## Enforcement

The six phase skills live in `.claude/skills/` and are shared by all
tools. They are marked user-invoked only, so they run when the user types
the command, not because an agent decided to. Each phase runs under its
own per-phase agent, which sets the model and, where the tool supports it,
restricts which files may be edited and which shell commands may run. Do
not try to work around a denied permission. Where a tool cannot enforce a
restriction, the phase skill's "Only write to" rule still applies: if a
phase seems to need a change outside it, stop and tell the user instead.

## Principles

- **Domain-Driven Design**: business logic lives in `domain/`, free of
  framework/infrastructure concerns; `application/` orchestrates it;
  `infrastructure/` and `api/` are the outermost, framework-facing
  layers. See `structure.md` and `backend.md`.
- **Single Responsibility**: every module/class has exactly one reason to
  change. Design docs must state each new module's single responsibility;
  implementation and review both check code against that statement.
