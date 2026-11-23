---
description: SDD - Generate requirements.md for a new feature or bugfix.md for a bug
agent: sdd-requirements
model: anthropic/claude-opus-5
subagent: false
---

Feature name (`<feature-name>`): `$1`

If the feature name is empty, ask the user for it instead of guessing.

The `spec-requirements` skill is included below. Follow its instructions exactly.

!`cat .opencode/skills/spec-requirements/SKILL.md`
