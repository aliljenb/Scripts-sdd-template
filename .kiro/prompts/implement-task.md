Feature name (`<feature-name>`): `${1}`
Task (`<task>`, optional): `${2}`

If the feature name is empty, ask the user for it instead of guessing.

Run the SDD `implement-task` phase by following `.claude/skills/implement-task/SKILL.md` exactly.

This phase must run as the `sdd-implement` agent, which sets the right model and
file permissions. If your system prompt doesn't say you are the SDD agent for the
`implement-task` phase, stop and tell the user to start it with
`kiro-cli chat --agent sdd-implement` (or switch agent in the session) and run
`/implement-task` again.
