---
description: Derive an implementation task list from the design (phase 3 of 3)
argument-hint: <feature-name>
allowed-tools: Read, Edit, Write, Bash(ls:*)
---

You are in **phase 3 (tasks)** of spec-driven development for feature **$1**.

Precondition: `specs/$1/design.md` must exist and have Status `approved`.
If it is not approved, stop and tell the user to complete `/spec-design $1` first.

First read:
- `specs/$1/design.md` and `specs/$1/requirements.md`
- `steering/tech.md` (testing strategy, conventions)

Then:
1. Break the design into a checklist of **small, ordered, test-backed** coding
   tasks. Each task should be completable and verifiable on its own.
2. Favour a test-first rhythm: write/extend tests, then implement to green.
3. Every task cites the requirement(s) it satisfies (`_Requirements: R1, R2_`).
4. Cover edge cases, the correctness check, and a final full-suite `pytest` task.
5. Write the result to `specs/$1/tasks.md`.

Rules:
- Tasks are concrete coding steps only — no deployment/manual chores.
- No task should be so large it can't be reviewed in one sitting.
- Set Status to `approved` ONLY after the user explicitly approves, then tell
  them implementation begins with `/spec-impl $1`. Do not write feature code yet.
