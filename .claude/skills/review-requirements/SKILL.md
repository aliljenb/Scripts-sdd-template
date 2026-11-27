---
name: review-requirements
description: SDD - Review a feature's requirements.
model: opus
argument-hint: [feature-name]
disable-model-invocation: true
allowed-tools: Read Glob Grep
---

Review the requirements spec of feature **$1**.
Read `specs/$1/requirements.md` in full.

This skill is read-only analysis — it reports findings, it does not edit code or spec files.

## Checks to run

1. **AMBIGUITIES** — vague words, undefined terms, unstated units.
2. **MISSING CASES** — error, boundary, security, timing, lifecycle.
3. **CONFLICTS** — requirements that contradict or compete.
4. **UNTESTABLE** — statements with no objective pass/fail.

## Reporting

Output as a table: ID | Type | Finding | Why it matters | Question for the author | Severity (High/Med/Low).
Cite the exact requirement text. If unsure, ask, do not guess.
Limit: top 15 findings, ranked by risk.

Do not fix issues yourself — this skill only reports; fixes go back through `/spec-requirements`.
