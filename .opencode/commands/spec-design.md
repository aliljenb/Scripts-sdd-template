---
description: SDD - Generate design.md (DDD domain model + layered design) for a feature
agent: sdd-design
model: anthropic/claude-opus-5
subagent: false
---

Feature name (`<feature-name>`): `$1`

If the feature name is empty, ask the user for it instead of guessing.

The `spec-design` skill is included below. Follow its instructions exactly.

!`cat .claude/skills/spec-design/SKILL.md`
