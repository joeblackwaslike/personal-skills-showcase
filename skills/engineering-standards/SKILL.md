---
name: engineering-standards
description: Joe Black's engineering execution standards — code-generation principles, strict Python type-safety and wemake-python-styleguide philosophy, the Tier 0-3 verification gate, standard project tooling, PR/merge autonomy, and git-worktree discipline. Invoke when writing or reviewing code to hold the work to these bars.
---

# Engineering Standards

How I hold code — mine and my agents' — to a senior bar. Opinionated on purpose: the constraints are where the design work happens. This is a public, sanitized slice of the standards I run across every project.

## Default stack posture

- **TypeScript-first** for agents, APIs, tools, bots, MCP servers, plugins, GitHub apps, and web apps.
- **Python** when the task genuinely needs it — local model inference, torch/HuggingFace, pydantic-ai, or Python-only libraries.
- Deeper framework/library choices live in the companion **`joe-stack-preferences`** skill.

## Code generation

- **Confirm scope before writing** — full implementation vs. core logic vs. skeleton.
- **Don't rewrite code that wasn't asked about;** don't touch files that weren't mentioned.
- Match the surrounding codebase's async/sync style. New projects are async-first.
- **No ASCII section-banner comments** in any language (e.g. `# ---- Public methods ----`). They signal a module/class doing too much — extract the section into its own module/class instead. Delete banners you encounter.
- Docstrings on all public APIs; never duplicate type-hint information in prose.
- **Readable code over commented code** — if code needs a comment to be understood, refactor it. No inline comments unless there's genuinely no better way. No commented-out code.

## Python type-safety (strict)

| Preference | Rule |
|---|---|
| Union / Optional | `X \| Y \| None` (PEP 604) — never `Optional[X]` or `Union[X, Y]` |
| Generics | `list[x]`, `dict[k, v]`, `tuple[x, ...]` — never `List`/`Dict`/`Tuple` from `typing` |
| Structural typing | `Protocol` over `ABC` |
| Generics declaration | `TypeVar`, `ParamSpec`, `TypeVarTuple` |
| Structured dicts | `TypedDict` over plain `dict` |
| Return annotations | Always — including private/internal methods |
| `Any` | Avoid; always be explicit |

## wemake-python-styleguide (WPS) philosophy

The strictest mainstream Python linter, and I lean into it:

- **Almost every WPS rule exists to make the code better.** A tripped rule is a signal to improve the *design*, not noise to silence. "It's a lot of work" is not a reason to disable it.
- **Default: satisfy the rule by refactoring** — packages, OOP, single-responsibility. Never disable a rule project-wide.
- **Inline `# noqa: WPSxxx` (with a one-line reason) is the only acceptable suppression**, reserved for the narrow legitimate cases below.

| Rule | Verdict | Action |
|---|---|---|
| WPS202 too-many-members | Keep | Split the module into a **package** (one submodule per concern); re-export the public API in `__init__.py` |
| WPS214 too-many-methods | Keep | Decompose the class into composed **single-concern objects** |
| WPS211 too-many-args | Keep | Aggregate related args into an object; `# noqa` only for Typer/Click command params |
| WPS412 logic-in-`__init__` | Keep | Move logic to a real submodule; re-export |
| WPS347 vague-import | Keep | `from pkg import module` then `module.func()` — self-documenting call sites |
| WPS110 vague-name (`data`, `result`, `item`…) | Keep | Rename; `# noqa` only for external-contract-bound names |

> If you find yourself adding banner comments to group methods/functions by concern, that's the decomposition telling you where the module/class boundaries are.

## Standard project tooling

| Concern | Tool |
|---|---|
| Python version | 3.12+ |
| Package manager / venv | `uv` (exclusively) |
| Config file | `pyproject.toml` only |
| Lint + format | `ruff` |
| Type check | `mypy --strict` |
| Testing | `pytest` + `pytest-asyncio` |
| Project layout | `src/` layout |
| Lockfile | `uv.lock` (pinned) |
| Task runner | `just` |
| Commits | conventional commits (commitizen) |
| CI | GitHub Actions |
| Local dev | Docker / docker-compose |
| Config mgmt | `pydantic-settings` + `.env` |
| Logging | `structlog` |
| Secrets | a secrets manager + CLI — never in source or plaintext |

For TypeScript: Biome + ESLint (strict, `strictTypeChecked`), Vitest, Husky, tsup/tsc, released via Conventional Commits + release-please.

## The Verification Gate (Tier 0-3)

**Before declaring any code work complete**, it clears these tiers — in order:

- **Tier 0 — Static.** `ruff check` + `ruff format --check`, `mypy --strict` (Python); `biome` / `tsc --noEmit` (TS). Must pass clean.
- **Tier 1 — Tests.** Full suite green. **No skips masking failures.**
- **Tier 2 — Adversarial.** Re-read the diff as a hostile reviewer: edge cases, error paths, resource cleanup, injection surfaces.
- **Tier 3 — Behavioral.** For non-trivial changes, confirm *runtime* behavior — golden output, a manual smoke, or a property test. Not just "it compiles."

**A change that only satisfies Tier 0 is not done. Never report success on un-run code.**

## PR & merge autonomy

- Drive PRs to completion — don't stop to hand off routine steps. Once a PR is approved and CI is green, **merge it** (squash unless repo convention differs) and move to the next.
- Own the whole review loop: triage feedback, fix real issues, reply to threads, re-poll, merge.
- Stop only for genuinely hard-to-reverse risk (data loss, destructive ops, outward-facing publishing) or a real product/design decision.
- Force-push **only your own** feature branches, and prefer `--force-with-lease`. Never force-push shared/protected branches.
- Sequence multi-PR work lowest-number-first; don't start the next until the current merges.

## Git worktrees for parallel / agent work

Default for feature work; **mandatory** when running parallel agents/subagents on one repo:

- Start a feature in a dedicated **git worktree**; work there, commit, prune when done.
- **Never run concurrent agents against the same working tree.** Each gets its own worktree so edits/stashes/resets can't clobber each other. (A stray `git stash` from one shared-tree agent once silently wiped a large multi-file refactor — worktrees prevent that class of bug entirely.)
- Every concurrently-launched subagent gets the instruction: *work in your own worktree, commit there, do not touch the parent checkout.*

## Shell & scripts

- Prefer **composable tools** (`jq`, `socat`, standard CLIs) over bespoke code.
- Use `jq` for JSON in shell — never inline Python parsers.
- **Write files directly** rather than through `osascript`/interpolated heredocs (both mangle `$` variables).
- Single-line edits to existing files: read first, then a targeted replace — never rewrite the whole file.

## Related public skills

The methodology above is backed by hands-on skills in my public [`agent-skills`](https://github.com/joeblackwaslike/agent-skills) library:

- Agentic method → [best-practices-for-agentic-development](https://github.com/joeblackwaslike/agent-skills/tree/main/skills/best-practices-for-agentic-development)
- Architecture → [domain-driven-design](https://github.com/joeblackwaslike/agent-skills/tree/main/skills/domain-driven-design) · [solid-principles](https://github.com/joeblackwaslike/agent-skills/tree/main/skills/solid-principles)
- Git / PR discipline → [git-github-workflows](https://github.com/joeblackwaslike/agent-skills/tree/main/skills/git-github-workflows) · [working-with-git](https://github.com/joeblackwaslike/agent-skills/tree/main/skills/working-with-git) · [working-with-github](https://github.com/joeblackwaslike/agent-skills/tree/main/skills/working-with-github)
- CI &amp; releases → [working-with-github-actions](https://github.com/joeblackwaslike/agent-skills/tree/main/skills/working-with-github-actions) · [working-with-release-please](https://github.com/joeblackwaslike/agent-skills/tree/main/skills/working-with-release-please)
- Shell → [working-with-bash](https://github.com/joeblackwaslike/agent-skills/tree/main/skills/working-with-bash) · [working-with-zsh](https://github.com/joeblackwaslike/agent-skills/tree/main/skills/working-with-zsh)
