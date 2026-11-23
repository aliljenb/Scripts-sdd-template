---
description: SDD - Review a feature's implementation against its approved spec
agent: sdd-review
model: anthropic/claude-opus-5
subagent: true
---

Feature name (`<feature-name>`): `$1`

If the feature name is empty, ask the user for it instead of guessing.

The `review` skill is included below. Follow its instructions exactly.

!`cat .opencode/skills/review/SKILL.md`
