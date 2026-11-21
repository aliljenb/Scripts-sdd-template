---
name: spec-new
description: SDD - Set up a new feature spec folder from the templates
---

> **Arguments:** `<feature-name>` is the argument passed to the `/spec-new` command.

Scaffold a new spec for the feature: **<feature-name>**

Use only the file tools (read, glob/list, write). The shell is not
available in this phase — do not try to run shell commands.

1. Validate that <feature-name> is a single kebab-case name (lowercase letters,
   digits, and hyphens only, no spaces). If not, suggest a corrected name and stop.
2. If `specs/<feature-name>/` already exists (check with glob/list), report it
   and stop (do not overwrite).
3. Read each template and write it into `specs/<feature-name>/`, replacing
   every `[feature-name]` with <feature-name> in the content you write
   (writing the first file creates the folder):
   - `.opencode/skills/spec-requirements/template.md` → `specs/<feature-name>/requirements.md`
   - `.opencode/skills/spec-design/template.md` → `specs/<feature-name>/design.md`
   - `.opencode/skills/spec-tasks/template.md` → `specs/<feature-name>/tasks.md`
4. Write an empty `specs/<feature-name>/prompts.txt` file.
5. Confirm the folder is ready and tell the user the next step is
   `/spec-requirements <feature-name>`.

Do NOT fill in any content yet — that is the next phase.
