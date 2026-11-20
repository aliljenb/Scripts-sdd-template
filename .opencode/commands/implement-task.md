---
description: SDD - Implement task(s) from a feature's approved tasks.md
agent: sdd-implement
model: anthropic/claude-sonnet-5-5
subagent: false
---

Feature name (`<feature-name>`): `$1`
Task (`<task>`, optional): `$2`

If the feature name is empty, ask the user for it instead of guessing.

The `implement-task` skill is included below. Follow its instructions exactly.

!`cat .opencode/skills/implement-task/SKILL.md`
