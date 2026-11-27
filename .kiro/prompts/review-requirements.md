Feature name (`<feature-name>`): `${1}`

If the feature name is empty, ask the user for it instead of guessing.

Run the SDD `review-requirements` phase by following `.claude/skills/review-requirements/SKILL.md` exactly.

This phase must run as the `sdd-review-requirements` agent, which sets the right model and
file permissions. If your system prompt doesn't say you are the SDD agent for the
`review-requirements` phase, stop and tell the user to start it with
`kiro-cli chat --agent sdd-review-requirements` (or switch agent in the session) and run
`/review-requirements` again.
