---
applyTo: "src/**/*.py,tests/**/*.py"
paths:
  - "src/**/*.py"
  - "tests/**/*.py"
---

# Backend Rules

Code-level conventions for Python under `src/` and `tests/`. The design
principles behind them live in `.github/instructions/domain-driven-design.instructions.md`
(layering, when to use which pattern) and
`.github/instructions/solid-principles.instructions.md` (SRP); those take precedence if
anything here seems to conflict. See `.github/instructions/structure.instructions.md` for
the full directory layout and `.github/instructions/tech.instructions.md` for the tooling
stack.

## Where things go

- `src/<python_module>/domain/` imports only the standard library and
  other domain modules. If a domain file needs anything else, that
  dependency belongs in `application/` or `infrastructure/`.
- When the design calls for a building block, give it its own module
  rather than collapsing several into one `models.py`. Only introduce the
  building blocks the design justifies (see `domain-driven-design.md`).
- Repository interfaces (ports) are declared in
  `domain/<aggregate>/repository.py` as `typing.Protocol` or `abc.ABC`.
  Concrete adapters live in `infrastructure/repositories/` and depend on
  the port, never the other way around.
- Concrete adapters are wired into use cases at the composition root in
  `api/` (dependency injection), not imported by `application/`.

## Python conventions

- Value objects are immutable: use `@dataclass(frozen=True)` (or an
  equivalent) and validate invariants in `__post_init__`.
- Entities compare by identity: implement `__eq__`/`__hash__` on the ID,
  not on attribute values.
- Domain errors are explicit exception classes in the domain layer, not
  bare `ValueError`/`Exception`.
- All code is fully type-annotated and must pass `mypy` and `ruff` as
  configured in `tech.md`.

## Project conventions

- All backend source lives under `src/<python_module>/`; test files
  mirror that tree under `tests/`.
- Don't introduce a package not listed in `tech.md` without asking first.
  New runtime dependencies get added to the table in `tech.md`, with the
  reasoning noted in its Decision log.
