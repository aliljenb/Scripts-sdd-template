---
description: Draft or refine a feature's technical design (phase 2 of 3)
argument-hint: <feature-name>
allowed-tools: Read, Edit, Write, Grep, Glob, Bash(ls:*)
---

You are in **phase 2 (design)** of spec-driven development for feature **$1**.

Precondition: `specs/$1/requirements.md` must exist and have Status `approved`.
If it is not approved, stop and tell the user to complete `/spec-requirements $1` first.

First read for context:
- `specs/$1/requirements.md`
- `steering/tech.md`, `steering/structure.md`
- Any existing code under `src/pairstrading/` relevant to the feature.

Then:
1. Produce a technical design that satisfies every requirement: which layer the
   change belongs in (service / repository / backtest / util / etc.), module
   placement, function/class interfaces and signatures, DataFrame schemas and
   column-list constants, database impact (new tables/columns), error handling,
   and a testing strategy.
2. Include an explicit **correctness analysis** (no in-sample look-ahead,
   no silent currency mixing across pair legs, etc.).
3. Add a **traceability** section mapping each requirement (R1, R2, …) to the
   design element that satisfies it.
4. Write the result to `specs/$1/design.md`.

Rules:
- Ground every decision in the requirements; do not introduce new scope.
- Surface alternatives and justify the chosen approach.
- Set Status to `approved` ONLY after the user explicitly approves, then tell
  them the next step is `/spec-tasks $1`. Do not start writing tasks or code yet.
