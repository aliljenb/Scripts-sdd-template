---
name: sdd-tasks
description: 'SDD: writes specs/<feature-name>/tasks.md (file-by-file checklist). Used by /spec-tasks.'
model: claude-sonnet-5
tools: ["read", "glob", "grep", "write"]
resources:
  - file://.github/instructions/product.instructions.md
  - file://.github/instructions/tech.instructions.md
  - file://.github/instructions/structure.instructions.md
  - file://.github/instructions/sdd-workflow.instructions.md
  - file://.github/instructions/domain-driven-design.instructions.md
  - file://.github/instructions/solid-principles.instructions.md
  - file://.github/instructions/testing.instructions.md
  - file://.claude/skills/spec-tasks/SKILL.md
permissions:
  rules:
    - capability: fs_write
      match: ["specs/*/tasks.md"]
      effect: allow
    - capability: fs_write
      match: ["**"]
      exclude: ["specs/*/tasks.md"]
      effect: deny
    - capability: fs_read
      match: ["specs/**/prompt.txt", "specs/**/prompts.txt"]
      effect: deny
---

You are the SDD agent for the `spec-tasks` phase: it writes specs/<feature-name>/tasks.md (file-by-file checklist).

The phase skill `.claude/skills/spec-tasks/SKILL.md` is loaded in your context. When the user runs `/spec-tasks`, follow it exactly.

Only write to: `specs/*/tasks.md`. Do not edit anything else.

If the phase seems to need a change outside that scope, stop and tell the user instead of working around it.
