# Linting & Formatting Design

## Problem

TypeScript projects need two things from a linting setup:
1. **Formatting + general TS/JS rules** — fast, opinionated, consistent across every repo
2. **Framework-specific rules** — React hooks exhaustiveness, accessibility, Next.js conventions

No single tool covers both well. Biome covers the first category faster and more completely than ESLint + Prettier. ESLint covers the second via plugins that Biome has no equivalent for.

**Goal:** Biome handles everything it can. ESLint is scoped strictly to framework-specific plugin rules — no formatting, no TypeScript rules, no overlap.

---

## Tool Responsibilities

| Concern | Tool | Rationale |
|---|---|---|
| Formatting (whitespace, quotes, commas) | Biome | Replaces Prettier; 50–100x faster |
| Import organization | Biome | Built-in, automatic |
| General TS/JS linting | Biome | `recommended` ruleset covers most cases |
| No `any`, unused vars, const correctness | Biome | Rules in `linter.rules` |
| React hooks (`exhaustive-deps`, `rules-of-hooks`) | ESLint | No Biome equivalent |
| Accessibility (`jsx-a11y`) | ESLint | No Biome equivalent |
| Next.js conventions (`@next/eslint-plugin-next`) | ESLint | Framework-specific only |
| Type-checking | `tsc --noEmit` | Neither Biome nor ESLint does full type checking |

**Explicit non-overlap rule:** ESLint config must not include any formatting rules or TypeScript rules already owned by Biome. ESLint touches `.tsx`/`.jsx` files only (and only for plugin rules).

---

## Project Variations

| Project type | Biome | ESLint |
|---|---|---|
| Node.js / plain TypeScript | ✅ | ✗ (not needed) |
| React library | ✅ | `react-hooks` + `jsx-a11y` |
| Next.js app | ✅ | `react-hooks` + `jsx-a11y` + `@next/eslint-plugin-next` |

---

## Configuration

### `biome.json` (root of every project)

```json
{
  "$schema": "https://biomejs.dev/schemas/1.9.4/schema.json",
  "organizeImports": {
    "enabled": true
  },
  "formatter": {
    "enabled": true,
    "indentStyle": "space",
    "indentWidth": 2,
    "lineEnding": "lf",
    "lineWidth": 100
  },
  "javascript": {
    "formatter": {
      "quoteStyle": "single",
      "trailingCommas": "all",
      "semicolons": "always",
      "arrowParentheses": "always"
    }
  },
  "linter": {
    "enabled": true,
    "rules": {
      "recommended": true,
      "correctness": {
        "noUnusedVariables": "error",
        "noUnusedImports": "error",
        "useExhaustiveDependencies": "off"
      },
      "style": {
        "useConst": "error",
        "noNonNullAssertion": "warn",
        "useTemplate": "error",
        "useNamingConvention": {
          "level": "error",
          "options": {
            "strictCase": false
          }
        }
      },
      "suspicious": {
        "noExplicitAny": "error",
        "noConsole": "warn"
      },
      "complexity": {
        "noExcessiveCognitiveComplexity": "warn"
      }
    }
  },
  "files": {
    "ignore": [
      "node_modules",
      ".next",
      ".turbo",
      "dist",
      "build",
      "coverage",
      "*.generated.ts"
    ]
  }
}
```

> `useExhaustiveDependencies` is `off` because `eslint-plugin-react-hooks` owns that rule
> in React/Next.js projects. Do not enable both.
>
> `useNamingConvention` enforces camelCase for variables/functions/params, PascalCase for types/classes/enums. `strictCase: false` allows mixed-case abbreviations like `myHTTPClient`. `@typescript-eslint/naming-convention` is NOT used — Biome owns naming per the no-overlap rule.

---

### `eslint.config.mjs` — Next.js app

Uses ESLint 9 flat config format. Install once per Next.js project.

```js
import { FlatCompat } from '@eslint/eslintrc';
import reactHooks from 'eslint-plugin-react-hooks';
import jsxA11y from 'eslint-plugin-jsx-a11y';
import { dirname } from 'path';
import { fileURLToPath } from 'url';

const __filename = fileURLToPath(import.meta.url);
const __dirname = dirname(__filename);
const compat = new FlatCompat({ baseDirectory: __dirname });

export default [
  // Next.js core web vitals + React rules (includes react/recommended internally)
  ...compat.extends('next/core-web-vitals'),

  {
    plugins: {
      'react-hooks': reactHooks,
      'jsx-a11y': jsxA11y,
    },
    rules: {
      // React hooks — the main reason ESLint exists here
      ...reactHooks.configs.recommended.rules,

      // Accessibility
      ...jsxA11y.configs.recommended.rules,

      // Promote warnings to errors for the rules we care about most
      'react-hooks/exhaustive-deps': 'error',
      'react-hooks/rules-of-hooks': 'error',
    },
  },

  {
    // ESLint only runs on component/JSX files — TypeScript files are Biome's domain
    files: ['**/*.tsx', '**/*.jsx'],
  },

  {
    ignores: ['.next/**', 'node_modules/**', 'dist/**', '*.config.mjs'],
  },
];
```

### `eslint.config.mjs` — React library (no Next.js)

```js
import reactHooks from 'eslint-plugin-react-hooks';
import jsxA11y from 'eslint-plugin-jsx-a11y';

export default [
  {
    plugins: {
      'react-hooks': reactHooks,
      'jsx-a11y': jsxA11y,
    },
    files: ['**/*.tsx', '**/*.jsx'],
    rules: {
      ...reactHooks.configs.recommended.rules,
      ...jsxA11y.configs.recommended.rules,
      'react-hooks/exhaustive-deps': 'error',
    },
  },
  {
    ignores: ['node_modules/**', 'dist/**'],
  },
];
```

---

## Dependencies

### Every project

```json
{
  "devDependencies": {
    "@biomejs/biome": "^1.9.4"
  }
}
```

### Next.js / React projects (additional)

```json
{
  "devDependencies": {
    "eslint": "^9",
    "eslint-plugin-react-hooks": "^5",
    "eslint-plugin-jsx-a11y": "^6",
    "@next/eslint-plugin-next": "^15",
    "@eslint/eslintrc": "^3"
  }
}
```

---

## `package.json` Scripts

```json
{
  "scripts": {
    "lint":        "biome check . && eslint .",
    "lint:fix":    "biome check --write . && eslint --fix .",
    "format":      "biome format --write .",
    "typecheck":   "tsc --noEmit",
    "check":       "pnpm typecheck && pnpm lint"
  }
}
```

> For plain Node/TypeScript projects without React, omit the `eslint` commands.

---

## Pre-commit (Husky + lint-staged)

### `.husky/pre-commit`

```sh
#!/usr/bin/env sh
. "$(dirname -- "$0")/_/husky.sh"
pnpm lint-staged
```

### `lint-staged` config (in `package.json`)

```json
{
  "lint-staged": {
    "*.{ts,tsx,js,jsx,json,css,md}": [
      "biome check --write --no-errors-on-unmatched --files-ignore-unknown=true"
    ],
    "*.{tsx,jsx}": [
      "eslint --fix --max-warnings=0"
    ]
  }
}
```

The two entries run in order. Biome runs first on everything; ESLint runs second on JSX files only.

---

## CI (GitHub Actions)

```yaml
- name: Typecheck
  run: pnpm typecheck

- name: Biome
  run: biome ci .

- name: ESLint (Next.js / React only)
  run: eslint . --max-warnings=0
  # Remove this step for plain Node.js projects
```

`biome ci` (not `biome check`) is the correct CI command — it never auto-fixes, always exits non-zero on violations.

---

## IDE Setup (VS Code)

`.vscode/extensions.json`
```json
{
  "recommendations": [
    "biomejs.biome",
    "dbaeumer.vscode-eslint"
  ]
}
```

`.vscode/settings.json`
```json
{
  "editor.defaultFormatter": "biomejs.biome",
  "editor.formatOnSave": true,
  "editor.codeActionsOnSave": {
    "source.fixAll.biome": "explicit",
    "source.organizeImports.biome": "explicit",
    "source.fixAll.eslint": "explicit"
  },
  "[typescript]":  { "editor.defaultFormatter": "biomejs.biome" },
  "[typescriptreact]": { "editor.defaultFormatter": "biomejs.biome" },
  "[javascript]":  { "editor.defaultFormatter": "biomejs.biome" },
  "[json]":        { "editor.defaultFormatter": "biomejs.biome" }
}
```

Biome owns formatting for all file types. ESLint extension runs in the background for React/Next.js rule violations but does not format.

---

## Monorepo (Turborepo + pnpm workspaces)

Place `biome.json` at the repo root — it applies to all packages automatically.

Each Next.js/React package gets its own `eslint.config.mjs`. Plain Node packages get none.

`turbo.json` task config:
```json
{
  "tasks": {
    "lint": {
      "dependsOn": [],
      "outputs": []
    },
    "typecheck": {
      "dependsOn": [],
      "outputs": []
    }
  }
}
```

Both `lint` and `typecheck` have no dependencies — they run in parallel across all packages.

---

## Maximum Strictness Mode (wemake-python-styleguide equivalent)

### Python → TypeScript Tool Mapping

| Python | TypeScript | Role |
|---|---|---|
| `ruff` | Biome | Fast formatter + baseline linter (Rust-based) |
| `wemake-python-styleguide` | `eslint-plugin-unicorn` | Opinionated style rules that force rewrites |
| `mypy --strict` | `@typescript-eslint/strictTypeChecked` | Deep type-aware linting via compiler API |
| `flake8-bugbear` | `eslint-plugin-sonarjs` | Complexity limits, duplicate detection |
| `pre-commit` hooks | Husky + lint-staged | Block commits on violations |

The Python setup works because ruff handles speed/formatting and wemake handles the "this code is wrong even if it works" opinions. The same split applies here: **Biome for formatting/speed, ESLint strict plugins for the opinionated rules that force rewrites.**

---

### Why These Three Plugins

**`@typescript-eslint` — `strictTypeChecked` preset**

This is the wemake of TypeScript. It runs the TypeScript compiler API during linting (not just parsing), which unlocks rules that normal ESLint cannot do:
- Cannot `await` a non-Promise
- Cannot call a value typed as `any`
- Cannot assign `any` to a typed variable
- Must handle all floating Promises (`no-floating-promises`)
- Cannot pass `async` functions where sync is expected
- Cannot return `any` from a typed function

This is the closest thing to mypy strict mode at the linting level.

**`eslint-plugin-unicorn`**

~100 opinionated rules maintained by Sindre Sorhus. This is the direct wemake equivalent — it forces modern patterns and will make you rewrite working code:
- No abbreviated variable names (`req`, `cb`, `e` → must whitelist exceptions)
- No nested ternaries
- No `Array.forEach` (use `for...of`)
- No `process.exit()` in library code
- Consistent filename casing (`kebab-case` enforced)
- Must `throw new Error()` not `throw Error()`
- Prefer modern APIs throughout

**`eslint-plugin-sonarjs`**

Complexity and duplication detection:
- Cognitive complexity limit per function (default: 15, recommend: 10)
- No duplicate string literals (catches magic strings)
- No identical function bodies
- No redundant boolean expressions

---

### Strict ESLint Config (`eslint.config.mjs` — strict mode)

This replaces the baseline ESLint configs for projects where wemake-level strictness is wanted. For Next.js, merge this with the React/Next.js plugin config.

```js
import tseslint from 'typescript-eslint';
import unicorn from 'eslint-plugin-unicorn';
import sonarjs from 'eslint-plugin-sonarjs';
import importX from 'eslint-plugin-import-x';
import noSecrets from 'eslint-plugin-no-secrets';

export default tseslint.config(
  // Type-checked strict rules — requires TypeScript compiler API
  ...tseslint.configs.strictTypeChecked,
  ...tseslint.configs.stylisticTypeChecked,

  // Unicorn — wemake-equivalent opinionated style rules
  unicorn.configs['flat/recommended'],

  // SonarJS — complexity + duplication
  sonarjs.configs.recommended,

  // Import discipline — ESLint 9 flat config fork of eslint-plugin-import
  importX.flatConfigs.recommended,
  importX.flatConfigs.typescript,

  {
    plugins: {
      'no-secrets': noSecrets,
    },

    languageOptions: {
      parserOptions: {
        // Enables type-aware rules — required for strictTypeChecked
        project: true,
        tsconfigRootDir: import.meta.dirname,
      },
    },

    rules: {
      // ── Type safety (wemake mypy-equivalent) ──────────────────────────────
      '@typescript-eslint/no-floating-promises': 'error',
      '@typescript-eslint/no-misused-promises': ['error', {
        checksVoidReturn: { attributes: false }, // allow async event handlers in JSX
      }],
      '@typescript-eslint/require-await': 'error',
      '@typescript-eslint/no-unnecessary-condition': 'error',
      '@typescript-eslint/consistent-type-imports': ['error', {
        prefer: 'type-imports',
        fixStyle: 'inline-type-imports',
      }],
      '@typescript-eslint/explicit-function-return-type': ['error', {
        allowExpressions: true,          // allow: const fn = () => value
        allowTypedFunctionExpressions: true,
        allowHigherOrderFunctions: true,
      }],
      '@typescript-eslint/restrict-template-expressions': ['error', {
        allowNumber: true,               // allow `${count} items`
      }],
      // Object literal type assertions bypass excess property checking — disallow them
      '@typescript-eslint/consistent-type-assertions': ['error', {
        assertionStyle: 'as',
        objectLiteralTypeAssertions: 'never',
      }],

      // ── Complexity (wemake complexity limits) ─────────────────────────────
      'sonarjs/cognitive-complexity': ['error', 10],
      'sonarjs/no-duplicate-string': ['error', { threshold: 3 }],

      // ── Structural complexity (WPS202/211/220/225 equivalents) ────────────
      // warn not error — React components legitimately exceed function/statement limits
      'max-lines-per-function': ['warn', { max: 80, skipBlankLines: true, skipComments: true, IIFEs: true }],
      'max-lines': ['warn', { max: 400, skipBlankLines: true, skipComments: true }],
      'max-params': ['error', { max: 5 }],
      'max-depth': ['error', { max: 5 }],
      'max-statements': ['warn', { max: 20 }, { ignoreTopLevelFunctions: true }],

      // ── Error handling (WPS424/WPS420 equivalents) ────────────────────────
      'no-empty': ['error', { allowEmptyCatch: false }],
      'unicorn/error-message': 'error',
      'unicorn/catch-error-name': 'error',
      'unicorn/custom-error-definition': 'error',

      // ── Import discipline (WPS301/WPS302 equivalents) ─────────────────────
      'import-x/no-relative-parent-imports': 'error',
      'import-x/no-cycle': ['error', { maxDepth: 10 }],
      // Biome's organizeImports owns ordering and deduplication
      'import-x/order': 'off',
      'import-x/no-duplicates': 'off',

      // ── Security (S105–S107 / hardcoded secrets equivalent) ──────────────
      // tolerance 4.5: avoids flagging UUIDs (entropy ~3.8–4.2), catches real API keys/tokens
      'no-secrets/no-secrets': ['error', { tolerance: 4.5 }],

      // ── Magic numbers (WPS432 equivalent) ────────────────────────────────
      '@typescript-eslint/no-magic-numbers': ['warn', {
        ignore: [-1, 0, 1, 2, 10, 100, 1000],
        ignoreArrayIndexes: true,
        ignoreDefaultValues: true,
        ignoreClassFieldInitialValues: true,
        ignoreEnums: true,
        ignoreNumericLiteralTypes: true,
        ignoreReadonlyClassProperties: true,
        enforceConst: true,
      }],

      // ── Unicorn tuning ────────────────────────────────────────────────────
      // Whitelist common abbreviations that are universally understood
      'unicorn/prevent-abbreviations': ['error', {
        replacements: {
          props:  false,
          ref:    false,
          ctx:    false,
          req:    false,
          res:    false,
          err:    false,
          db:     false,
          id:     false,
          env:    false,
          fn:     false,
        },
      }],
      // null is valid TypeScript (Supabase, DB nulls, DOM APIs)
      'unicorn/no-null': 'off',
      // kebab-case for all files, allow multiple extensions (.test.ts, .config.ts)
      'unicorn/filename-case': ['error', {
        case: 'kebabCase',
        multipleFileExtensions: true,
      }],
      // process.exit is fine in CLI tools and scripts
      'unicorn/no-process-exit': 'off',
    },
  },

  {
    ignores: [
      'node_modules/**',
      'dist/**',
      '.next/**',
      '.turbo/**',
      '*.generated.ts',
      '*.config.mjs',
      'vitest.config.ts',
    ],
  },
);
```

---

### Additional Dependencies (strict mode)

```json
{
  "devDependencies": {
    "typescript-eslint": "^8",
    "eslint-plugin-unicorn": "^56",
    "eslint-plugin-sonarjs": "^3",
    "eslint-plugin-import-x": "^4",
    "eslint-plugin-no-secrets": "^1",
    "eslint": "^9"
  }
}
```

> Use `typescript-eslint` (unified package, v8+) — not the old separate
> `@typescript-eslint/eslint-plugin` + `@typescript-eslint/parser` pair.
>
> Use `eslint-plugin-import-x` (not `eslint-plugin-import`) — import-x is the ESLint 9 flat config fork. The original `eslint-plugin-import` lacks flat config support. Rule prefix changes from `import/` to `import-x/`.

---

### Rules That Will Make You Rewrite Code

These are the "wemake moments" — rules that reject working code and force a better pattern:

| Rule | What it rejects | What it forces |
|---|---|---|
| `@typescript-eslint/no-floating-promises` | `fetchData()` (unhandled) | `await fetchData()` or `void fetchData()` |
| `@typescript-eslint/no-unsafe-assignment` | `const x = response.data` (any-typed) | Proper typing or Zod parse |
| `@typescript-eslint/explicit-function-return-type` | `function process(x) { ... }` | `function process(x: Input): Output { ... }` |
| `unicorn/prevent-abbreviations` | `const e = new Error()` | `const error = new Error()` |
| `unicorn/no-array-for-each` | `items.forEach(fn)` | `for (const item of items) { ... }` |
| `unicorn/prefer-module` | `__dirname`, `require()` | `import.meta.dirname`, `import` |
| `sonarjs/cognitive-complexity` | Deep nested conditionals | Extract functions, simplify branching |
| `unicorn/no-nested-ternary` | `a ? b ? c : d : e` | Early returns or explicit conditions |
| `max-depth` | 6-deep nested `if/for/try` | Extract functions, early returns |
| `max-params` | `function f(a, b, c, d, e, f)` | Options object or smaller functions |
| `import-x/no-relative-parent-imports` | `import x from '../../utils'` | Absolute imports via path aliases |
| `import-x/no-cycle` | Module A imports B which imports A | Restructure to break the cycle |
| `@typescript-eslint/consistent-type-assertions` | `const x = {} as MyType` | Properly typed construction |
| `no-secrets/no-secrets` | `const token = 'sk-AbC...xyz'` | Environment variable |

---

### Updated Pre-commit for Strict Mode

```json
{
  "lint-staged": {
    "*.{ts,tsx,js,jsx,json,css,md}": [
      "biome check --write --no-errors-on-unmatched --files-ignore-unknown=true"
    ],
    "*.{ts,tsx}": [
      "eslint --fix --max-warnings=0"
    ],
    "*.{tsx,jsx}": [
      "eslint --fix --max-warnings=0"
    ]
  }
}
```

> `--max-warnings=0` is the key: any warning is a commit block.
> Strict mode should have zero `warn` rules — everything is `error` or `off`.

---

### Unclosable Gaps (No TypeScript Equivalent)

These WPS checks have no viable ESLint equivalent — documented here so maintainers
don't spend time searching for rules that don't exist.

| WPS Rule | Description | Why No TS Equivalent |
| --- | --- | --- |
| `WPS425` | Boolean trap (bool positional args) | No rule distinguishes bool from non-bool parameter position |
| `WPS430` | No nested functions | `no-inner-declarations` only catches `function` declarations, not `const fn = () =>` |
| `WPS416` | No overly broad `try` scope | No rule tracks lines covered by a try block |
| `WPS424` | No broad `catch` type | No rule checks the *type* of caught error (only empty bodies via `no-empty`) |
| `WPS214` | Max class methods | No ESLint rule counts class methods |
| `WPS202` (members) | Max module-level exports | No ESLint rule counts top-level exports |
| `WPS221` | Max conditions per expression | No equivalent (cognitive-complexity is a proxy) |
| `S608` | SQL injection | Convention-only: always use parameterized queries / ORM |

For broad catch: convention is to use discriminated unions or `instanceof` checks inside catch
blocks. TypeScript's type system does not narrow the `catch` clause variable, so enforcement is
via code review.

---

### Progressive Adoption

Do not enable strict mode on an existing codebase all at once. Use ESLint's `overrides` pattern to enable rules per-directory, or use `// eslint-disable-next-line` with a tracking comment.

For new projects: start strict from day one. The pre-commit hook prevents bad patterns from entering the codebase, which is the entire point — you never accumulate debt to fix later.

```
New repo checklist:
☐ biome.json at root (with useNamingConvention enabled)
☐ eslint.config.mjs with strictTypeChecked + unicorn + sonarjs + import-x + no-secrets
☐ tsconfig.json with strict: true + noUncheckedIndexedAccess: true
☐ Husky pre-commit with lint-staged (--max-warnings=0)
☐ CI: biome ci + eslint --max-warnings=0 + tsc --noEmit
```
