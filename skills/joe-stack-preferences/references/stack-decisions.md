# Stack Decisions

Use these defaults when designing architecture or adding dependencies.

| Scenario | Default |
|---|---|
| New full-stack web app | Next.js 15 + Vercel + Supabase + shadcn/ui |
| Full-stack Next.js app with type-safe API | tRPC + `@trpc/react-query` on Next.js Route Handlers |
| TypeScript microservice to TypeScript client | Hono + tRPC adapter |
| Public REST API or cross-language service | Hono plain REST |
| TypeScript to Python boundary | REST/JSON only |
| New MCP server | Hono + `@modelcontextprotocol/sdk` + TypeScript |
| New Claude Code plugin | Node.js + TypeScript + `.claude-plugin/plugin.json` |
| Short user-facing AI bot/tool | TypeScript + Vercel AI SDK + Vercel serverless |
| Long-running/stateful/tool-loop AI agent | TypeScript + Vercel AI SDK + Node.js long-running process |
| New GitHub App/bot | TypeScript + Octokit + Vercel Functions |
| Event-driven AI workflow | TypeScript + Inngest |
| Durable workflow orchestration | TypeScript + Temporal TypeScript SDK |
| LangGraph | TypeScript `@langchain/langgraph` unless Python-only deps are required |
| Local model inference, torch, HuggingFace | Python |
| pydantic-ai pipeline | Python |
| Non-agent CLI | TypeScript + `tsx`; Python + Typer only if ML-adjacent |

Use tRPC only when every caller will always be TypeScript. If there is any chance of a non-TypeScript caller, use plain REST with Hono.

Python is the exception, not the default, for agents, APIs, tools, bots, MCP servers, Claude plugins, GitHub apps, and web apps.
