Feature name (`<feature-name>`): `${1}`

If the feature name is empty, ask the user for it instead of guessing.

Run the SDD `spec-design` phase by following `.claude/skills/spec-design/SKILL.md` exactly.

This phase must run as the `sdd-design` agent, which sets the right model and
file permissions. If your system prompt doesn't say you are the SDD agent for the
`spec-design` phase, stop and tell the user to start it with
`kiro-cli chat --agent sdd-design` (or switch agent in the session) and run
`/spec-design` again.
