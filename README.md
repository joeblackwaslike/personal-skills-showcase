<!-- palette matched to joeblack.nyc: base #0a0c0d · teal #15808a · cyan #45e1ed -->

<div align="center">

<img src="https://capsule-render.vercel.app/api?type=waving&color=0:0a0c0d,45:15808a,100:45e1ed&height=170&section=header&text=Personal%20Skills%20Showcase&fontColor=e6ecf0&fontSize=40&fontAlignY=40&desc=How%20I%20build%20%C2%B7%20design%20system%20%2B%20engineering%20standards%20as%20agent%20skills&descAlignY=62&descSize=16" alt="Personal Skills Showcase" width="100%" />

[![Website](https://img.shields.io/badge/joeblack.nyc-15808A?style=for-the-badge&logo=firefoxbrowser&logoColor=white&labelColor=0a0c0d)](https://joeblack.nyc) [![Profile](https://img.shields.io/badge/@joeblackwaslike-0a0c0d?style=for-the-badge&logo=github&logoColor=45e1ed)](https://github.com/joeblackwaslike)

</div>

---

A curated, public look at **how I build** — my design system and engineering standards, packaged as [Claude Code](https://claude.ai/code) / Codex **agent skills**.

These skills are lifted from my private personal-skills library and shared as a **portfolio reference**: the connective tissue that keeps my agents on-brand and on-standard. It's a window into how I work — not a drop-in plugin.

## What's inside

### 🎨 `agent-marketplace-design-system` — "Phosphor"

My design system for [joeblack.nyc](https://joeblack.nyc) / the Agent Marketplace hub: a terminal-grade, dark-first aesthetic with a teal→cyan accent and mono-forward type.

- **`colors_and_type.css`** — full color + type tokens (light + dark themes) and semantic type roles
- **`assets/brand-icons/`** — brand & social SVG glyphs
- **`preview/`** — HTML specimens (color, type, spacing, components)
- **`ui_kits/website/`** — a hi-fi, interactive recreation of the joeblack.nyc hub in reusable JSX components

### ⚙️ `joe-stack-preferences` — stack & architecture defaults

The opinionated stack choices I reach for.

- **TypeScript-first** for agents, APIs, tools, bots, MCP servers, plugins, and web apps; Python when the task genuinely needs it
- **Strict quality gates:** Biome + ESLint `strictTypeChecked` with unicorn / sonarjs / import-x / no-secrets, plus complexity and error-handling rules
- A **cross-language rule-coverage matrix** (ruff/WPS ↔ Biome/ESLint)
- Preferred libraries, AI-agent SDK patterns, and release tooling (Conventional Commits, release-please)

### 🛠️ `engineering-standards` — how I execute

The bar I hold code to, beyond which stack to pick.

- **Code-generation principles** — confirm scope, no ASCII banner comments, readable-over-commented, never touch unmentioned files
- **Strict Python type-safety** + a **wemake-python-styleguide** philosophy: refactor to satisfy the linter, never blanket-disable
- The **Tier 0-3 Verification Gate** — static → tests → adversarial diff review → behavioral runtime check before anything is "done"
- **PR/merge autonomy** and **git-worktree discipline** for parallel agent work

## Why this exists

Most of my agent-skills library is private. This is the shareable slice — enough to show the systems thinking behind the work without the internal plumbing. More at **[github.com/joeblackwaslike](https://github.com/joeblackwaslike)** · **[joeblack.nyc](https://joeblack.nyc)**.

## Usage & license

Shared as a portfolio reference — learn from the patterns freely. Brand assets, icons, and the joeblack.nyc identity are © Joe Black and not for reuse.
