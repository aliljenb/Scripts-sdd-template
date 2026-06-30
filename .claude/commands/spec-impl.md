---
description: Implement the next unchecked task from a feature's task list
argument-hint: <feature-name>
allowed-tools: Read, Edit, Write, Grep, Glob, Bash(pytest:*), Bash(ls:*)
---

You are **implementing** feature **$1** from its approved spec.

Precondition: `specs/$1/tasks.md` must exist and have Status `approved`.
If not, stop and tell the user to complete `/spec-tasks $1` first.

First read:
- `specs/$1/tasks.md` — find the FIRST unchecked `[ ]` task.
- `specs/$1/design.md` and `specs/$1/requirements.md` for context.
- `steering/tech.md` and `steering/structure.md` for conventions and placement.

Then implement **exactly one task**:
1. State which task you are doing.
2. Write or update tests first where the task calls for it.
3. Implement the code under `src/pairstrading/` per the design.
4. Run `pytest`. Fix issues until green.
5. Mark the task `[x]` in `specs/$1/tasks.md`.
6. Summarize what changed and **stop** for review. Do NOT start the next task.

Rules:
- One task per invocation. Never batch tasks.
- Stay within the approved design and scope. If the design is wrong or
  insufficient, stop and recommend returning to `/spec-design $1`.
- Do not hit the real database or external APIs in tests — use the mock
  pattern from `steering/tech.md`.
