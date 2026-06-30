# sdd-template

> TODO: one-paragraph description.

## Development workflow

This project uses **Kiro-style Spec-Driven Development** with Claude CLI.
Before writing any code, create a spec for the feature:

```
specs/<feature-name>/
├── requirements.md   # What & why
├── design.md         # How
└── tasks.md          # In what order
```

Copy the templates from `specs/_template/` and see `CLAUDE.md` for the full workflow.

## Setup

```bash
python -m venv .venv && source .venv/bin/activate
pip install -r requirements.txt
```

## Run tests

```bash
pytest --cov=src
```

## Code quality

```bash
ruff check . && mypy src/
```
