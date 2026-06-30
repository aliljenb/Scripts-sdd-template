---
description: Scaffold a new feature spec folder from the templates
argument-hint: <feature-name>
allowed-tools: Bash(mkdir:*), Bash(cp:*), Bash(ls:*), Read, Write
---

Scaffold a new spec for the feature: **$1**

1. Validate the feature name `$1` is kebab-case. If not, suggest a corrected name and stop.
2. If `specs/$1/` already exists, report it and stop (do not overwrite).
3. Create `specs/$1/` and copy the three templates into it:
   - `specs/_templates/requirements.md` → `specs/$1/requirements.md`
   - `specs/_templates/design.md` → `specs/$1/design.md`
   - `specs/_templates/tasks.md` → `specs/$1/tasks.md`
4. In each copied file, replace `<feature>` with `$1`.
5. Confirm the folder is ready and tell the user the next step is
   `/spec-requirements $1`.

Do NOT fill in any content yet — that is the next phase.
