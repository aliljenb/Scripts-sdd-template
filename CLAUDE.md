@AGENTS.md

## Claude Code notes

- Rules load from `.claude/rules/`. `backend` and `frontend` rules are
  path-scoped through their `paths` frontmatter; the rest load every
  session.
- Each phase skill in `.claude/skills/` sets its own model. Claude Code
  applies a skill's model only to the turn that invokes it. For phases with
  back-and-forth (`/spec-requirements`, `/spec-design`), follow-up turns use
  the session model, so switch with `/model opus` for those sessions.
- `/review` runs in a forked subagent (`context: fork`) so it starts without
  the implementer's context.
- Claude Code has no per-phase file-write permissions. Respect each phase
  skill's "Only write to" restriction exactly; if a phase needs to touch a
  file outside it, stop and tell the user.
