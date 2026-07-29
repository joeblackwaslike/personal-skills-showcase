# AI & Agent Development in TypeScript

Use Vercel AI SDK as the primary abstraction for streaming, tools, structured outputs, multi-provider support, and agents. Use provider SDKs directly only when their specific features matter.

## Libraries

- Multi-provider AI: Vercel AI SDK `ai`.
- Anthropic Claude: `@anthropic-ai/sdk` for Claude-specific features such as prompt caching, vision, batch API, and token counting.
- OpenAI / Codex: `openai`, directly or through AI SDK.
- MCP servers: `@modelcontextprotocol/sdk`.
- Anthropic sandboxed execution: `@anthropic-ai/sandbox-runtime`.
- Event-driven AI workflows: Inngest.
- Embeddings/vector search: Supabase pgvector + AI SDK embedding helpers.
- GitHub API: Octokit.

## Tool Use & Structured Output

- Define schemas with Zod.
- Use AI SDK `tool()` for Zod to JSON Schema conversion.
- If calling Anthropic/OpenAI SDKs directly, convert Zod schemas with `zod-to-json-schema` before passing schemas to `tools[]`.
- Prefer AI SDK `generateObject()` and `streamObject()` over manual JSON parsing.

## Streaming

- Prefer AI SDK `streamText()` or Anthropic `client.messages.stream()` over blocking calls.
- In Vercel serverless, return `Response` with `ReadableStream`.
- Ensure proper flushing before timeout.

## Parallel Agents

- Use `Promise.allSettled()`.
- One failure must not abort the entire flow.
- Inspect every settled result explicitly.
- Log rejected cases and emit partial results.
- `allSettled` without inspecting rejections is just `Promise.all` with extra steps.

## Anthropic Prompt Caching

- Apply `cache_control: { type: "ephemeral" }` to system prompts and large static context.
- Caching applies per request.
- Reuse a client singleton across invocations.

## Context Management

- Count large Anthropic contexts with `client.beta.messages.countTokens()`.
- Long agent histories need explicit truncation or summarization.
- Do not let context grow unbounded.

## MCP & Claude Plugins

- Use `.claude-plugin/plugin.json` for Claude Code plugin registration.
- Use the MCP SDK Server class.
- Export tools via a tools handler.
- Use Zod for input schemas.
- Prefer thin shell wrappers to TypeScript logic via `tsx` for hook scripts.
- Always verify webhook signatures before processing payloads.
