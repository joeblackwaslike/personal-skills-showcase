---
name: agentic-workflow
description: How Joe Black runs coding agents as a disciplined system — semantic code navigation, process-first workflows (brainstorm/debug/research before acting), persistent cross-session memory, context-efficient tool execution, a routed skills library, and issue-tracker-driven task management. A public, sanitized look at the agentic dev environment behind the work.
---

# Agentic Workflow

I run coding agents (Claude Code, Codex, and friends) as a **disciplined system**, not ad-hoc prompting. This is a sanitized look at how the environment is wired — the philosophy, not the private plumbing.

## Semantic code navigation & editing

Agents explore and edit through **semantic tools** (LSP-backed symbol search, references, targeted symbol edits) rather than blind `grep`/full-file reads and rewrites. The result: smaller context, fewer accidental edits, and changes scoped to the symbol that actually matters.

## Process before code

The workflow front-loads thinking so implementation is cheap and correct:

- **Brainstorm before designing** — explore intent, requirements, and trade-offs before touching code.
- **Systematic debugging before fixing** — reproduce and root-cause first; never patch symptoms.
- **Research before proposing** — check current official docs and search the community for known gotchas/footguns before recommending a library or API. Training data goes stale; the docs don't.

## Persistent memory across sessions

Context survives session boundaries:

- **Recall before answering** "what did we do with X?" — a cross-session memory search surfaces prior decisions that aren't in the current files.
- **Orientation** on an unfamiliar codebase via a read-only structural pass before diving in.
- **Long-term capture** runs automatically at session end, so significant decisions are retrievable later.

## Context efficiency

Large tool outputs never get to bloat the window. Sandboxed MCP execution runs multi-tool workloads and returns **only the final result** — up to ~99% fewer tokens on heavy workflows. (See my [`mcp-exec`](https://github.com/joeblackwaslike/mcp-exec) — the reference implementation of Anthropic's *Code execution with MCP*.) A companion [`ctx-tree`](https://github.com/joeblackwaslike/ctx-tree) keeps raw payloads out of context by returning identifiers, snippets, and hints instead.

## A routed skills library

Instead of one giant always-on prompt, domain knowledge lives in a large, **auto-updating** skills library that's invoked on demand — git/GitHub, CI, Vercel, DDD/SOLID, plugin development, docs tooling, and more. See [`agent-skills`](https://github.com/joeblackwaslike/agent-skills) (public) and the companion [`engineering-standards`](../engineering-standards) and [`joe-stack-preferences`](../joe-stack-preferences) in this repo.

## Quality gates

- Dedicated **code-review** and **security-review** passes (the latter whenever touching auth, crypto, input validation, permissions, secrets, or external APIs).
- The **Tier 0-3 Verification Gate** (static → tests → adversarial diff → behavioral) before anything is called "done" — see [`engineering-standards`](../engineering-standards).
- Receiving review feedback with rigor: verify the suggestion technically before implementing it, rather than performative agreement.

## Task management & isolation

- Work is tracked in an **issue tracker**, not ephemeral to-do lists — durable, queryable, survives compaction.
- Parallel agent work runs in **isolated git worktrees** so concurrent edits/stashes can't clobber each other.

## Multi-provider by design

The same standards drive Claude Code, Codex, Cursor, and Gemini from a **single shared `AGENTS.md`**, projected into each tool's format — so the bar is identical no matter which agent is at the keyboard.

## Related public skills

- [best-practices-for-agentic-development](https://github.com/joeblackwaslike/agent-skills/tree/main/skills/best-practices-for-agentic-development) — designing, testing, and reviewing agents & agentic workflows
- [working-with-serena](https://github.com/joeblackwaslike/agent-skills/tree/main/skills/working-with-serena) — the semantic code-intelligence toolkit
- [web-research](https://github.com/joeblackwaslike/agent-skills/tree/main/skills/web-research) — the research-routing approach
- [beads-operations](https://github.com/joeblackwaslike/agent-skills/tree/main/skills/beads-operations) — issue-tracker-driven task management
- [multi-provider-plugins](https://github.com/joeblackwaslike/agent-skills/tree/main/skills/multi-provider-plugins) — one skills library across Claude / Codex / Cursor / Gemini
