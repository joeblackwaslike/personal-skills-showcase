# Python Preferences

## Scaffolding New Projects

When creating a new Python project, scaffold it with the **`spinup-py`** CLI rather than hand-rolling
config — it bakes in this exact stack (uv, src layout, ruff + mypy + wemake-python-styleguide, deptry,
pre-commit, pytest, tox-uv, GitHub Actions, Docker, devcontainer, release automation).

```shell
uvx spinup-py my-project          # scaffold a new project (guided prompts)
spinup-py --update ./existing     # retrofit an existing project with current tooling
```

Repo: `https://github.com/joeblackwaslike/spinup-py` · Python sibling of `spinup-ts`.

## Non-Negotiables

- Python 3.12+.
- Type hints on all signatures, globals, class variables, instance variables, and returns.
- Docstrings on all public APIs without repeating type information.
- Prefer readable code over commented code.
- No inline comments unless genuinely unavoidable.
- No ASCII-art section headers or footers.
- No commented-out code.
- Avoid `Any`; be explicit.
- Never add `from __future__ import annotations`.

## Type Hinting

- Use `X | Y | None`; never `Optional[X]` or `Union[X, Y]`.
- Use `list[x]`, `dict[k, v]`, `tuple[x, ...]`; never `List`, `Dict`, or `Tuple`.
- Prefer `Protocol` over `ABC`.
- Use `TypeVar`, `ParamSpec`, and `TypeVarTuple` for generics.
- Use `TypedDict` over plain `dict` for structured dictionaries.

## Standard Stack

- Package manager: `uv`.
- Lockfile: `uv.lock`.
- Build backend: `setuptools`.
- Config: `pyproject.toml` only.
- Linting/formatting: `ruff` and `wemake-python-styleguide`.
- Type checking: strict `mypy`.
- Testing: `pytest` and `pytest-asyncio`.
- Pre-commit: `pre-commit`.
- Layout: `src/`.
- Task runner: `just`.
- Commits: conventional commits / commitizen + release-please v4 (`release-type: python`).
- CI: GitHub Actions.
- Local dev: Docker / docker-compose.
- Config: `pydantic-settings` and `.env`.
- Logging: `structlog` preferred.

## Preferred Libraries

- API: FastAPI, FastAPI-Boilerplate, SQLModel where appropriate.
- Complex APIs: DDD/CQRS with Commands, Queries, Events, Handlers, MessageBus.
- Data modeling: Pydantic v2+.
- ORM: async SQLAlchemy.
- CLI/TUI: Typer + Rich, or Click.
- Databases: PostgreSQL, Redis, Neo4j, Supabase.
- AI/LLM: Anthropic models preferred, pydantic-ai, LangGraph, LangChain.
- MCP: assume active MCP tooling awareness.
