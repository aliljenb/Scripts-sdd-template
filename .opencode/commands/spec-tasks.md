---
description: SDD - Generate tasks.md, a file-by-file implementation checklist derived from design.md
agent: sdd-tasks
model: anthropic/claude-sonnet-5
subagent: false
---

Feature name (`<feature-name>`): `$1`

If the feature name is empty, ask the user for it instead of guessing.

The `spec-tasks` skill is included below. Follow its instructions exactly.

!`cat .claude/skills/spec-tasks/SKILL.md`
