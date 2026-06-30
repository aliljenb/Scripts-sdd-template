# Project Structure

> Update this file whenever new top-level files or directories are added.

```
sdd-template/
├── CLAUDE.md                    # Claude instructions and SDD workflow
├── README.md
├── requirements.txt
├── .python-version
├── steering/
│   ├── product.md               # Product goals and constraints
│   ├── tech-stack.md            # Approved technology choices
│   └── structure.md             # This file — canonical layout
├── specs/
│   ├── _template/               # Copy these; never edit the originals
│   │   ├── requirements.md
│   │   ├── design.md
│   │   └── tasks.md
│   └── <feature-name>/          # One directory per feature
│       ├── requirements.md
│       ├── design.md
│       └── tasks.md
├── src/
│   └── sdd_template/
│       ├── __init__.py
│       └── ...
├── tests/
│   ├── __init__.py
│   └── ...                      # Mirror src/ structure
└── docs/
```

## Conventions

- All source code lives under `src/sdd_template/`
- Test files mirror the source tree (`tests/test_<module>.py`)
- One spec directory per feature — specs are never shared across features
- Steering documents always live directly under `steering/`
