Feature name (`<feature-name>`): `${1}`

If the feature name is empty, ask the user for it instead of guessing.

Run the SDD `spec-requirements` phase by following `.claude/skills/spec-requirements/SKILL.md` exactly.

This phase must run as the `sdd-requirements` agent, which sets the right model and
file permissions. If your system prompt doesn't say you are the SDD agent for the
`spec-requirements` phase, stop and tell the user to start it with
`kiro-cli chat --agent sdd-requirements` (or switch agent in the session) and run
`/spec-requirements` again.
