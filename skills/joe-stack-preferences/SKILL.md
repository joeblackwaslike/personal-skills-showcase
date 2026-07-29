---
name: joe-stack-preferences
description: Use when choosing or evaluating Joe Black's preferred software stacks, architecture defaults, dependencies, or coding standards for Python, TypeScript, Next.js, frontend, AI/agent systems, MCP servers, Claude Code plugins, GitHub bots, CLIs, APIs, Python-vs-TypeScript decisions, TypeScript linting/code-quality setup (Biome, ESLint strict mode, rule coverage), or release tooling (commitizen, release-please, Conventional Commits).
---

# Joe Stack Preferences

Use this skill to keep Joe's stack and architecture preferences out of always-on context while still applying them when work involves code, dependencies, project setup, or architecture.

## Core Rule

Default to TypeScript for agents, APIs, tools, bots, MCP servers, Claude Code plugins, GitHub apps, and web apps. Use Python when the task genuinely requires local model inference, torch/HuggingFace, pydantic-ai, or a Python-only library.

Do not substitute preferred libraries casually. If a dependency, framework, or API detail may have changed, check current official docs and known gotchas before recommending or implementing it.

## Reference Map

Load only the reference files needed for the task:

- `references/stack-decisions.md`: architecture defaults, Python-vs-TypeScript choice, tRPC/REST rules.
- `references/typescript.md`: TypeScript non-negotiables, standard stack, preferred libraries.
- `references/frontend.md`: Next.js/React frontend stack and UI library preferences.
- `references/ai-agents.md`: TypeScript AI SDK, Anthropic/OpenAI, MCP, prompt caching, streaming, parallel agents.
- `references/python.md`: Python typing, tooling, and preferred backend libraries.
- `references/agentic-repos.md`: Joe's agent infra & full-stack AI app repos and local paths.
- `references/biome-eslint.md`: Biome vs ESLint design — tool responsibilities, full `biome.json` (with `useNamingConvention`) and `eslint.config.mjs` configs, CI/pre-commit setup, IDE setup, monorepo config. Strict mode: `@typescript-eslint/strictTypeChecked` + unicorn + sonarjs + `import-x` (circular/parent-import rules) + `no-secrets`; structural complexity rules (`max-lines-per-function`, `max-params`, `max-depth`, etc.); error handling rules; magic numbers; unclosable-gaps documentation (boolean trap, broad catch, nested functions, SQL injection — no TS equivalent exists).
- `references/linting-coverage-matrix.md`: Cross-language linting rule coverage matrix (ruff/WPS vs Biome/ESLint) by category — formatting, naming, complexity, type safety, async, imports, security. Shows which gaps are now closed (`✅`), partially covered (`⚠️`), or have no TS equivalent (~~no equivalent~~). Use to verify coverage before adding a new rule or to answer "does TS have an equivalent for WPS rule X?"
- `references/typescript-stack-cheatsheet.md`: Comprehensive TypeScript stack quick reference — stack decision tables, frontend/backend library choices, tooling standards, non-negotiables, AI/agent patterns with code examples, and Supabase/Drizzle database strategy.
- `references/release-management.md`: Commit enforcement and release automation — commitizen + cz-git + commitlint (TypeScript) and commitizen PyPI package (Python), release-please v4 GitHub Actions workflow, `release-please-config.json` / manifest templates for Node and Python, monorepo config, and full Husky hook wiring.

## Usage

1. Identify the task domain: Python, TypeScript, frontend, AI agent, MCP/plugin, API, CLI, GitHub bot, linting/code-quality, or architecture decision.
2. Read `stack-decisions.md` for architecture or dependency decisions.
3. Read the specific language/domain reference needed for implementation details.
4. Apply repo-local conventions if they conflict with these preferences; mention the conflict briefly.
5. Keep main-document behavior rules in force: clarify ambiguous scope, avoid touching unrelated files, and summarize changes.

### Linting domain routing

For any task involving linting, formatting, ESLint/Biome config, or adding/evaluating a lint rule:

- **Setting up or configuring a project** → read `biome-eslint.md` for the full config (biome.json, eslint.config.mjs, dependencies, CI, IDE).
- **"Does TypeScript have an equivalent for WPS rule X?"** or **checking gap coverage** → read `linting-coverage-matrix.md`.
- **Adding a new rule** → check `linting-coverage-matrix.md` first to verify it isn't already covered or explicitly unclosable, then use `biome-eslint.md` for the config context.
- **Strict mode / wemake-equivalent setup** → `biome-eslint.md` has the complete strict config (strictTypeChecked + unicorn + sonarjs + import-x + no-secrets).
