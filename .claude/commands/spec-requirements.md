---
description: Draft or refine a feature's requirements (phase 1 of 3)
argument-hint: <feature-name>
allowed-tools: Read, Edit, Write, Bash(ls:*)
---

You are in **phase 1 (requirements)** of spec-driven development for feature **$1**.

First read for context:
- `steering/product.md`, `steering/tech.md`, `steering/structure.md`
- `specs/$1/requirements.md` (current draft; if missing, tell the user to run `/spec-new $1`)

Then:
1. Work WITH the user to capture requirements as user stories with **EARS**
   acceptance criteria (WHEN/IF/WHILE/WHERE … THE SYSTEM SHALL …).
2. Capture non-functional requirements — especially **reproducibility**,
   data-consistency (no in-sample look-ahead in walk-forward periods), and
   any performance/data-volume constraints.
3. Write the result to `specs/$1/requirements.md`.

Rules:
- Describe *what* and *why* only. **No implementation details, no code, no design.**
- Ask clarifying questions when a requirement is ambiguous rather than guessing.
- When you believe requirements are complete, set the doc's Status to `approved`
  ONLY after the user explicitly approves, then tell them the next step is
  `/spec-design $1`. Do not move to design on your own.
