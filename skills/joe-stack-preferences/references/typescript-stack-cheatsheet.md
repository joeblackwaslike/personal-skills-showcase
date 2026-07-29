# TypeScript Stack Cheatsheet

## Stack Decision Logic

| Scenario | Default |
|---|---|
| New full-stack web app | Next.js 15 + tRPC + Vercel + Supabase + shadcn/ui |
| New MCP server | Node.js + `@modelcontextprotocol/sdk` + TypeScript |
| New Claude Code plugin | Node.js + TypeScript + `.claude-plugin/plugin.json` |
| New AI-powered tool or bot | TypeScript + `ai` (Vercel AI SDK) + `@anthropic-ai/sdk` + Vercel or Node |
| New GitHub App/bot | TypeScript + Octokit + Vercel Functions |
| Event-driven AI workflow | TypeScript + Inngest |
| Durable / long-running workflow | TypeScript + Temporal SDK |
| Standalone REST API / microservice | Hono + TypeScript (plain REST) |
| TypeScript ↔ TypeScript service | Hono + tRPC adapter |
| Scheduled batch / ETL pipeline | Inngest (event-driven) or Temporal TypeScript SDK |
| Pure data science / ML | Python (only when Python ecosystem required) |
| LangGraph / pydantic-ai pipeline | Python (unless graph has no Python-only deps → prefer TS) |
| CLI tool (non-agent) | TypeScript + tsx; Python + Typer if ML-adjacent |

> **Python is the exception.** Use it only when the task genuinely requires torch, HuggingFace,
> local model inference, or pydantic-ai. For agents, APIs, bots, MCP servers — default to TypeScript.

### tRPC vs REST

| | Use |
|---|---|
| Every caller is TypeScript | tRPC (type safety is free, no codegen) |
| Any non-TS caller (Python, mobile, public) | Hono + plain REST |
| TypeScript ↔ Python boundary | REST/JSON only |

---

## Frontend Stack (React / Next.js)

| Category | Library |
|---|---|
| Framework | Next.js 15+ (App Router) |
| React | 19+ |
| UI components | shadcn/ui (Radix UI + CVA + tailwind-merge) |
| Styling | Tailwind CSS v4 |
| Icons | Lucide React |
| Toasts | Sonner |
| Command palette | cmdk |
| Forms | React Hook Form + `@hookform/resolvers` + Zod |
| Server state | TanStack Query v5 (+ `@trpc/react-query` when using tRPC) |
| Client / UI state | Zustand |
| Tables | TanStack Table v8 |
| API layer | tRPC + `@trpc/react-query` (TS↔TS); openapi-fetch (REST fallback) |
| Charts | Recharts |
| Dates | date-fns + react-day-picker |
| Themes | next-themes |
| Animations | motion (Framer Motion v11+) + `@formkit/auto-animate` (simple lists) |
| Markdown | react-markdown + remark-gfm + rehype-pretty-code |
| Utility classes | clsx + tailwind-merge (via shadcn `cn()`) |
| Routing | Next.js App Router (colocated layouts, loading.tsx, error.tsx) |

---

## Node / TypeScript Backend Stack

| Category | Library |
|---|---|
| Web app framework | Next.js 15+ (Route Handlers + Server Actions) |
| API / microservice framework | Hono |
| Type-safe RPC | tRPC (on Next.js or Hono adapter) |
| Data modeling | Zod + TypeScript native types |
| ORM | Drizzle ORM (backend queries + migrations) |
| Database client | `@supabase/supabase-js` + `@supabase/ssr` (auth, realtime, storage) |
| Embeddings / vector | Supabase pgvector + `@xenova/transformers` or Anthropic/OpenAI embeddings API |
| Background jobs | Inngest |
| Durable workflows | Temporal TypeScript SDK |
| Config | Zod-validated `.env` via `t3-env` |
| Logging | pino |
| CLI / scripts | tsx (zero-build execution) + Commander or Yargs |
| GitHub integration | Octokit |
| AI / LLM | see AI section below |

---

## TypeScript Standard Stack (Tooling)

| Category | Choice |
|---|---|
| Node.js | 22+ LTS |
| Package manager | pnpm (everywhere) |
| Monorepo | pnpm workspaces + Turborepo |
| Module system | ESM only — `"type": "module"` in package.json |
| TypeScript | 5.x — `strict: true`, `noUncheckedIndexedAccess: true` |
| Target / resolution | ES2022+; NodeNext (Node), Bundler (Vite/frontend) |
| Linting | Biome (default) + ESLint w/ `react-hooks` + `jsx-a11y` + `@next/eslint-plugin-next` (Next.js only) |
| Formatting | Biome |
| Testing | Vitest + `@testing-library/react` + Playwright (E2E) |
| Pre-commit | Husky + lint-staged |
| Commit enforcement | commitizen + cz-git + commitlint |
| Release automation | release-please v4 (Google) |
| Build — Next.js | Turbopack (built-in) |
| Build — library | Vite 7 |
| Build — Node | tsc (no bundler); tsx for scripts |
| Deployment | Vercel |
| CI | GitHub Actions |

---

## TypeScript Non-Negotiables

### tsconfig
- `strict: true` — no exceptions
- `noUncheckedIndexedAccess: true` — array/object access returns `T | undefined`
- `exactOptionalPropertyTypes: true` — optional props ≠ `| undefined` unless explicit

### Module system
- ESM only — never `require()` or `module.exports`
- `"type": "module"` in every package.json

### Types
- Never `any` — use `unknown` + narrowing, or proper generics
- Avoid `as` assertions — use `satisfies`, generics, or narrowing instead
- `as const` and `as never` (exhaustive checks) are acceptable exceptions
- Use `@ts-expect-error` over `@ts-ignore` (fails if error no longer exists)
- Prefer `type` over `interface` — use `interface` only when declaration merging is needed
- Zod for all runtime validation at system boundaries (API input, env vars, external data)

### Code style
- Path aliases: `@/*` → `./src/*` in all projects
- Async-first: `async/await` over callbacks or `.then()` chains
- No barrel files (`index.ts` re-exports) unless it's a published library package
- Biome for formatting and linting; ESLint only for framework-specific plugin rules

---

## AI & Agent Development (TypeScript)

### Primary libraries

| Library | Use |
|---|---|
| `ai` (Vercel AI SDK) | Primary abstraction — unified streaming, tool use, multi-provider |
| `@anthropic-ai/sdk` | Anthropic-specific features; always cache system prompts (`cache_control: ephemeral`) |
| `openai` | OpenAI / Codex |
| `@modelcontextprotocol/sdk` | MCP server development |
| `zod-to-json-schema` | Convert Zod schemas → JSON Schema for SDK `tools[]` |
| `@vercel/sandbox` | Sandboxed code execution |
| `inngest` | Event-driven AI workflows |
| `octokit` | GitHub App / bot integration |

### Patterns

**Parallel agents**
```ts
const results = await Promise.allSettled([agentA(), agentB(), agentC()]);
// Always inspect rejections — do not silently discard
results.forEach(r => r.status === 'rejected' && logger.error(r.reason));
```

**Structured tool use (Zod → JSON Schema)**
```ts
import { zodToJsonSchema } from 'zod-to-json-schema';

const schema = z.object({ query: z.string(), limit: z.number().default(10) });

const tools = [{ name: 'search', input_schema: zodToJsonSchema(schema) }];
```

**Prompt caching (Anthropic)**
```ts
const response = await anthropic.messages.create({
  model: 'claude-opus-4-5',
  system: [{ type: 'text', text: LARGE_SYSTEM_PROMPT, cache_control: { type: 'ephemeral' } }],
  messages,
});
```

**Streaming (prefer over blocking)**
```ts
// Anthropic SDK
const stream = await anthropic.messages.stream({ ... });

// Vercel AI SDK (multi-provider)
const { textStream } = streamText({ model: 'anthropic/claude-opus-4-5', messages });
```

**Webhook signature verification**
```ts
// Always verify before processing — never trust raw payload alone
const isValid = verifySignature(req.headers['x-signature'], rawBody, secret);
if (!isValid) return Response.json({ error: 'Unauthorized' }, { status: 401 });
```

**Context / token management**
- Count tokens before sending: `anthropic.beta.messages.countTokens()`
- Define an explicit truncation or summarization strategy for long histories
- Never assume a conversation fits in context

### MCP server structure
```
.claude-plugin/
  plugin.json          # Claude Code plugin registration
src/
  server.ts            # @modelcontextprotocol/sdk Server class entry
  tools/               # One file per tool; Zod schemas for inputs
```

---

## Database Strategy (Supabase + Drizzle)

| Layer | Tool | Use |
|---|---|---|
| ORM / queries | Drizzle ORM | All backend data access, complex queries, migrations |
| Auth | `@supabase/supabase-js` | Sign-in, session management, RLS |
| Realtime | `@supabase/supabase-js` | Subscriptions, live updates |
| Storage | `@supabase/supabase-js` | File uploads, buckets |
| Vector search | Supabase pgvector | Semantic similarity via embeddings |
| Full-text search | PostgreSQL `tsvector` + `pg_trgm` | FTS with ranking |
| SSR / cookies | `@supabase/ssr` | Server-side auth in Next.js |

> Drizzle connects directly to the Postgres connection string.
> Supabase JS client is for auth/realtime/storage — not a substitute for an ORM.
