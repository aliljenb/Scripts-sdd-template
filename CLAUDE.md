# Project: sdd-template

This project follows **Kiro-style Spec-Driven Development (SDD)** using Claude CLI.

## Workflow

Every feature goes through a three-phase spec cycle before any implementation code
is written. Present each document to the user for approval before moving to the next.

| Phase | File | Answers |
|-------|------|---------|
| 1 | `requirements.md` | What & why (user stories + acceptance criteria) |
| 2 | `design.md`        | How (architecture, components, data models) |
| 3 | `tasks.md`         | In what order (ordered, checkboxed plan) |

**Never write implementation code until all three documents are approved.**

## Starting a new feature

1. Create `specs/<feature-name>/` (copy the three files from `specs/_template/`)
2. Draft `requirements.md` and present it — wait for approval
3. Draft `design.md` and present it — wait for approval
4. Draft `tasks.md` and present it — wait for approval
5. Implement tasks in order; tick each checkbox before starting the next
6. Update `steering/structure.md` whenever new files or directories are added

## Steering documents

Read all three before starting work in any new session:

- `steering/product.md`   — product goals and hard constraints
- `steering/tech-stack.md` — approved technologies (do not add packages without asking)
- `steering/structure.md`  — canonical project layout

## Rules

- Do not introduce packages not listed in `steering/tech-stack.md` without explicit approval.
- Do not skip the spec cycle for "small" changes — all changes start with a spec.
- Every implementation task must include at least one test.
- If implementation diverges from `design.md`, update the design doc first.
