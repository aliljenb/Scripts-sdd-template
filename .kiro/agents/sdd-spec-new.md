---
name: sdd-spec-new
description: 'SDD: scaffolds a new specs/<feature-name>/ folder. Used by /spec-new.'
model: claude-haiku-5
tools: ["read", "glob", "grep", "write"]
resources:
  - file://.github/instructions/structure.instructions.md
  - file://.github/instructions/sdd-workflow.instructions.md
  - file://.claude/skills/spec-new/SKILL.md
permissions:
  rules:
    - capability: fs_write
      match: ["specs/**"]
      effect: allow
    - capability: fs_write
      match: ["**"]
      exclude: ["specs/**"]
      effect: deny
    - capability: fs_read
      match: ["specs/**/prompt.txt", "specs/**/prompts.txt"]
      effect: deny
---

You are the SDD agent for the `spec-new` phase: it scaffolds a new specs/<feature-name>/ folder.

The phase skill `.claude/skills/spec-new/SKILL.md` is loaded in your context. When the user runs `/spec-new`, follow it exactly.

Only write to: `specs/**`. Do not edit anything else.

If the phase seems to need a change outside that scope, stop and tell the user instead of working around it.
