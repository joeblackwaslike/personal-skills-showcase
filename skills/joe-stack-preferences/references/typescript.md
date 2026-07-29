# TypeScript Preferences

## Scaffolding New Projects

When creating a new TypeScript project, scaffold it with the **`spinup-ts`** CLI rather than hand-rolling
config — it bakes in this exact stack (pnpm, ESM, strict TS, Biome + ESLint strict, Vitest, Husky,
GitHub Actions, Docker, release automation).

```bash
pnpm dlx spinup-ts my-project          # scaffold a new project (guided prompts)
pnpm dlx spinup-ts update .            # retrofit an existing repo with current tooling
```

Repo: `https://github.com/joeblackwaslike/spinup-ts` · TypeScript sibling of `spinup-py`.

## Non-Negotiables

- Node.js 22+ LTS.
- `pnpm` everywhere.
- ESM only; use `"type": "module"` and never `require()` or `module.exports`.
- TypeScript 5.x strict mode.
- `noUncheckedIndexedAccess: true` in all `tsconfig` files.
- Path aliases such as `@/*` to `./src/*`.
- Zod for all runtime validation at system boundaries: API input, env vars, external data.
- Biome for formatting, not Prettier.
- Never use `any`; use `unknown` plus narrowing, or proper generics.
- Avoid `as` type assertions. Prefer `satisfies`, generics, or proper narrowing.
- `as const` and exhaustive-switch `as never` are acceptable.
- Use `@ts-expect-error` over `@ts-ignore`.
- Prefer `type` over `interface`.
- Prefer `satisfies` when validating values without widening literals.
- Prefer `async`/`await` over callbacks or `.then()` chains.
- No barrel files unless publishing a library package.

## Standard Stack

- Monorepo: `pnpm` workspaces + Turborepo.
- Linting: Biome for TS/Node projects; ESLint only where framework-specific plugins are needed.
- Formatting: Biome.
- Testing: Vitest + Testing Library + Playwright.
- Pre-commit: Husky + lint-staged.
- Commit enforcement: commitizen + cz-git + commitlint.
- Release automation: release-please v4.
- Library build: Vite 7.
- Node build: `tsc`; use `tsx` for scripts.
- Logging: `pino`.
- Config: Zod-validated `.env`, preferably `t3-env`.
- Deployment: Vercel.
- CI: GitHub Actions.

## Next.js Stack

- Next.js 15+ App Router.
- React 19+.
- Turbopack.
- Biome + ESLint plugins for `react-hooks`, Next, and `jsx-a11y`.

## Preferred Libraries

- Web apps: Next.js.
- Standalone APIs and MCP HTTP servers: Hono.
- Data modeling: Zod + TypeScript native types.
- ORM/database: Drizzle ORM, `@supabase/supabase-js`, `@supabase/ssr`, Supabase PostgREST.
- Embeddings: Supabase pgvector + `@xenova/transformers` or Anthropic embeddings API.
- Background jobs: Inngest.
- CLI/TUI: `tsx`; Commander or Yargs if needed.
- AI/LLM: Vercel AI SDK `ai`, `@anthropic-ai/sdk`, `openai`, `@modelcontextprotocol/sdk`.
