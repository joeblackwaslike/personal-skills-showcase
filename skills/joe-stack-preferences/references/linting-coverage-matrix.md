# Linting Coverage Matrix

Columns show the specific rule/code that covers each check.
~~strikethrough~~ = check doesn't translate to this language's paradigm.
`—` = check applies but tool doesn't cover it (gap).

| | **Python** | **Python** | **TypeScript** | **TypeScript** |
|---|---|---|---|---|
| **Check** | **ruff** | **WPS** | **Biome** | **ESLint** (plugin) |

---

## Formatting

| Check | ruff | WPS | Biome | ESLint |
|---|---|---|---|---|
| Indentation style / width | `E1` / `W1` | ~~own formatter~~ | `formatter.indentStyle` | ~~Biome owns~~ |
| Max line length | `E501` | `WPS504` | `formatter.lineWidth` | ~~Biome owns~~ |
| Quote style (single/double) | `Q000` | ~~own~~ | `quoteStyle` | ~~Biome owns~~ |
| Trailing commas | `COM812` | ~~own~~ | `trailingCommas` | ~~Biome owns~~ |
| Trailing whitespace | `W291` | ~~own~~ | `formatter` | ~~Biome owns~~ |
| Blank lines between top-level defs | `E302` | ~~own~~ | `formatter` | ~~Biome owns~~ |
| Import ordering / grouping | `I001` (isort) | `WPS300` | `organizeImports` | ~~Biome owns~~ |
| Semicolons | ~~n/a~~ | ~~n/a~~ | `semicolons` | ~~Biome owns~~ |
| Arrow function parentheses | ~~n/a~~ | ~~n/a~~ | `arrowParentheses` | ~~Biome owns~~ |

---

## Naming Conventions

| Check | ruff | WPS | Biome | ESLint |
|---|---|---|---|---|
| No abbreviated names (`req`, `cb`, `e`) | — | `WPS111` | — | `unicorn/prevent-abbreviations` |
| snake_case variables (Python) / camelCase (TS) | `N806` pep8-naming | `WPS110` | `style.useNamingConvention` | ~~Biome owns~~ |
| SCREAMING_SNAKE for constants | `N818` | `WPS115` | `style.useNamingConvention` | ~~Biome owns~~ |
| PascalCase for classes | `N801` | `WPS110` | `style.useNamingConvention` | ~~Biome owns~~ |
| File / module naming convention | — | `WPS100` | — | `unicorn/filename-case` |
| No names that shadow builtins | `A001` | `WPS125` | — | `no-shadow` (core) |
| No single-letter names (outside loops) | — | `WPS111` | — | ⚠️ `unicorn/prevent-abbreviations` |

---

## Complexity

| Check | ruff | WPS | Biome | ESLint |
|---|---|---|---|---|
| Cognitive complexity limit | — | `WPS221` | `noExcessiveCognitiveComplexity` (warn) | `sonarjs/cognitive-complexity` |
| Max function length (lines) | — | `WPS202` | — | `max-lines-per-function` |
| Max module / file length (lines) | — | `WPS202` | — | `max-lines` |
| Max function arguments | — | `WPS211` | — | `max-params` |
| Max return statements per function | — | `WPS225` | — | `max-statements` |
| Max local variables per function | — | `WPS221` | — | `max-statements` |
| Max conditions per expression | — | `WPS221` | — | ~~no equivalent~~ |
| Max nesting depth | — | `WPS220` | — | `max-depth` |
| Max class methods | — | `WPS214` | — | — |
| Max module members / exports | — | `WPS202` | — | — |

---

## Type Safety

| Check | ruff | WPS | Biome | ESLint |
|---|---|---|---|---|
| No `Any` / `any` | `ANN401` | `WPS457` | `noExplicitAny` | `@typescript-eslint/no-explicit-any` |
| Return type annotation required | `ANN201` | `WPS457` | — | `@typescript-eslint/explicit-function-return-type` |
| Argument type annotations required | `ANN001` | `WPS457` | — | ⚠️ `@typescript-eslint/typedef` |
| No unsafe `any` assignment | — | — | — | `@typescript-eslint/no-unsafe-assignment` |
| No unsafe `any` call | — | — | — | `@typescript-eslint/no-unsafe-call` |
| No unsafe `any` return | — | — | — | `@typescript-eslint/no-unsafe-return` |
| No unsafe member access on `any` | — | — | — | `@typescript-eslint/no-unsafe-member-access` |
| No type assertions / `cast` | ~~type: ignore~~ | ~~type: ignore~~ | — | `@typescript-eslint/consistent-type-assertions` |
| `# type: ignore` → `# type: ignore[code]` | `PGH003` | — | ~~n/a~~ | `@ts-expect-error` (convention) |
| Consistent `import type` | ~~n/a~~ | ~~n/a~~ | ⚠️ | `@typescript-eslint/consistent-type-imports` |
| No unnecessary type assertion | — | — | — | `@typescript-eslint/no-unnecessary-type-assertion` |

---

## Async / Concurrency

| Check | ruff | WPS | Biome | ESLint |
|---|---|---|---|---|
| No floating (unhandled) Promises | ~~asyncio different~~ | ~~asyncio different~~ | — | `@typescript-eslint/no-floating-promises` |
| `async` function where sync expected | ~~asyncio different~~ | ~~asyncio different~~ | — | `@typescript-eslint/no-misused-promises` |
| `await` on non-Promise / non-awaitable | ~~asyncio different~~ | ~~asyncio different~~ | — | `@typescript-eslint/await-thenable` |
| `require-await` (useless `async`) | `RUF029` | — | — | `@typescript-eslint/require-await` |
| No `asyncio.sleep(0)` / busy-wait | `ASYNC100` | — | ~~n/a~~ | ~~n/a~~ |
| No Promise constructor anti-pattern | ~~n/a~~ | ~~n/a~~ | — | `unicorn/no-promise-resolve-reject` |

---

## Code Patterns

| Check | ruff | WPS | Biome | ESLint |
|---|---|---|---|---|
| No magic numbers | — | `WPS432` | — | `@typescript-eslint/no-magic-numbers` |
| No nested functions | — | `WPS430` | — | — |
| No nested classes | — | `WPS431` | ~~n/a~~ | ~~n/a~~ |
| No lambda / arrow assigned to name | `E731` | `WPS111` | ~~n/a~~ | ⚠️ `prefer-arrow-callback` |
| No boolean trap (bool argument) | — | `WPS425` | — | — |
| No nested ternaries | — | `WPS509` | — | `unicorn/no-nested-ternary` |
| No walrus operator (`:=`) | ~~ruff allows~~ | `WPS332` | ~~n/a~~ | ~~n/a~~ |
| Prefer template literals over concat | `UP032` (f-string) | — | `useTemplate` | `@typescript-eslint/restrict-template-expressions` |
| Prefer `for…of` over `forEach` / `for i` | `PERF401` | — | — | `unicorn/no-array-for-each` |
| Must `throw new Error()` | ~~raise `Error()`~~ | — | — | `unicorn/throw-new-error` |
| No `else` after `return` / `raise` | `RET505` | `WPS219` | — | `unicorn/no-else-return` |
| No double negation (`!!`) | — | `WPS523` | — | ⚠️ `no-extra-boolean-cast` (core) |
| Prefer `??` over `\|\|` for defaults | — | — | — | `@typescript-eslint/prefer-nullish-coalescing` |
| Prefer `?.` optional chaining | — | — | — | `@typescript-eslint/prefer-optional-chain` |

---

## Imports

| Check | ruff | WPS | Biome | ESLint |
|---|---|---|---|---|
| Unused imports removed | `F401` | `WPS458` | `noUnusedImports` | ~~Biome owns~~ |
| Wildcard imports banned | `F403` | `WPS347` | `noWildcardImports` | ~~Biome owns~~ |
| Import ordering enforced | `I001` | `WPS300` | `organizeImports` | ~~Biome owns~~ |
| No relative imports beyond 1 level | — | `WPS301` | — | `import-x/no-relative-parent-imports` |
| Module import depth limit | — | `WPS302` | — | ~~no equivalent~~ |
| No `__future__` imports | `UP` | `WPS407` | ~~n/a~~ | ~~n/a~~ |
| No circular imports | — | — | — | `import-x/no-cycle` |

---

## Error Handling

| Check | ruff | WPS | Biome | ESLint |
|---|---|---|---|---|
| No bare / broad `except` / `catch` | `BLE001` | `WPS424` | — | `no-empty` (empty body only; broad type: ~~no equivalent~~) |
| Raise / throw with cause (`from` / `cause:`) | `B904` | `WPS420` | — | `unicorn/error-message` |
| No bare `raise` outside `except` | `E741` | `WPS425` | — | — |
| Error class naming (`Error` suffix) | `N818` | — | — | `unicorn/custom-error-definition` |
| No overly broad `try` scope | — | `WPS416` | — | — |

---

## Dead Code & Correctness

| Check | ruff | WPS | Biome | ESLint |
|---|---|---|---|---|
| Unused variables | `F841` | — | `noUnusedVariables` | `@typescript-eslint/no-unused-vars` |
| Unused function parameters | `ARG001` | — | `noUnusedParameters` | `@typescript-eslint/no-unused-vars` |
| Unreachable code | `F704` | — | `noUnreachable` | `@typescript-eslint/no-unreachable-code` |
| Undefined variables | `F821` | — | `noUndeclaredVariables` | ~~tsc owns~~ |
| Unnecessary `else` / `pass` | `RET504` | `WPS219` | — | `unicorn/no-else-return` |
| `const` over `let` where possible | — | — | `useConst` | `prefer-const` (core) |
| No non-null assertion (`!`) | ~~n/a~~ | ~~n/a~~ | `noNonNullAssertion` | `@typescript-eslint/no-non-null-assertion` |

---

## Duplication

| Check | ruff | WPS | Biome | ESLint |
|---|---|---|---|---|
| Duplicate string literals | — | `WPS226` | — | `sonarjs/no-duplicate-string` |
| Identical function bodies | — | — | — | `sonarjs/no-identical-functions` |
| Duplicate conditions in if-chain | — | — | — | `sonarjs/no-all-duplicated-branches` |
| Duplicate imports | `F811` | `WPS458` | `noUselessImports` | ~~Biome owns~~ |

---

## Security

| Check | ruff | WPS | Biome | ESLint |
|---|---|---|---|---|
| No `eval` | `S307` | `WPS421` | `noGlobalEval` | `unicorn/no-unsafe-regex` + `no-eval` |
| Hardcoded secrets / passwords | `S105`–`S107` | — | — | `no-secrets/no-secrets` |
| SQL injection patterns | `S608` | — | — | — |
| No `subprocess` with `shell=True` | `S602` | — | ~~n/a~~ | ~~n/a~~ |
| No `assert` in production | `S101` | `WPS473` | — | — |
| No `console.log` in production | ~~n/a~~ | ~~n/a~~ | `noConsole` | ~~Biome owns~~ |

---

## Summary: Gaps by Category

| Category | Python coverage | TypeScript coverage | Closable? |
|---|---|---|---|
| Formatting | ✅ ruff | ✅ Biome | — |
| Naming conventions | ✅ WPS | ✅ `style.useNamingConvention` | — |
| Complexity limits | ✅ WPS | ✅ sonarjs + ESLint `max-*` | — |
| Type safety | ✅ mypy + ANN | ✅ @typescript-eslint | — |
| Async / Promises | ⚠️ limited | ✅ @typescript-eslint | TS wins here |
| Code patterns | ✅ WPS | ⚠️ unicorn partial | No `no-nested-functions` equiv; boolean trap: unclosable |
| Magic numbers | ✅ WPS | ✅ `@typescript-eslint/no-magic-numbers` | — |
| Import depth limits | ✅ WPS | ✅ `import-x` | — |
| Error handling | ✅ ruff + WPS | ⚠️ partial | Broad catch type: unclosable |
| Duplication | ✅ WPS | ✅ sonarjs | — |
| Security | ✅ ruff (bandit) | ✅ `no-secrets/no-secrets` | — |
