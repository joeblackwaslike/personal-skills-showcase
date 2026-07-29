# Release Management

## Philosophy

[Conventional Commits](https://www.conventionalcommits.org/) is the contract. `commitizen` enforces it at commit time; `release-please` reads the history and automates versioning, CHANGELOG, and GitHub release PRs.

---

## commitizen — TypeScript / Node

Use `cz-git` as the adapter. It reads `commitlint` config from `package.json` — zero separate config files needed.

### Dependencies

```json
{
  "devDependencies": {
    "commitizen": "^4",
    "cz-git": "^1",
    "@commitlint/cli": "^19",
    "@commitlint/config-conventional": "^19"
  }
}
```

### `commitlint.config.ts`

```ts
import type { UserConfig } from '@commitlint/types';

const config: UserConfig = {
  extends: ['@commitlint/config-conventional'],
};

export default config;
```

### `package.json` — commitizen config

```json
{
  "config": {
    "commitizen": {
      "path": "node_modules/cz-git"
    }
  }
}
```

### Husky hooks

```sh
# .husky/commit-msg
npx --no -- commitlint --edit "$1"

# .husky/prepare-commit-msg
exec < /dev/tty && node_modules/.bin/cz --hook || true
```

Install hooks:

```sh
pnpm exec husky init
echo 'npx --no -- commitlint --edit "$1"' > .husky/commit-msg
echo 'exec < /dev/tty && node_modules/.bin/cz --hook || true' > .husky/prepare-commit-msg
chmod +x .husky/commit-msg .husky/prepare-commit-msg
```

---

## commitizen — Python

Use the `commitizen` PyPI package (`cz`).

### `pyproject.toml`

```toml
[tool.commitizen]
name = "cz_conventional_commits"
tag_format = "v$version"
version_scheme = "semver"
version_provider = "pep621"  # reads/writes [project] version
update_changelog_on_bump = true
```

### pre-commit hook

```yaml
# .pre-commit-config.yaml
repos:
  - repo: https://github.com/commitizen-tools/commitizen
    rev: v3.29.1  # pin to latest
    hooks:
      - id: commitizen
        stages: [commit-msg]
```

Install: `pre-commit install --hook-type commit-msg`

### Usage

```sh
cz commit        # interactive commit wizard
cz bump          # bump version + update CHANGELOG
cz changelog     # generate CHANGELOG only
```

---

## release-please — GitHub Actions

Uses `googleapis/release-please-action@v4`. When Conventional Commits land on the default branch, release-please opens a versioned "Release PR". Merging it bumps the version, tags the commit, and publishes an updated `CHANGELOG.md`.

### Workflow

```yaml
# .github/workflows/release-please.yml
name: Release Please

on:
  push:
    branches: [main]

permissions:
  contents: write
  pull-requests: write

jobs:
  release-please:
    runs-on: ubuntu-latest
    outputs:
      release_created: ${{ steps.release.outputs.release_created }}
      tag_name: ${{ steps.release.outputs.tag_name }}
    steps:
      - uses: googleapis/release-please-action@v4
        id: release
        with:
          config-file: release-please-config.json
          manifest-file: .release-please-manifest.json
```

Add a `publish` job gated on `if: ${{ needs.release-please.outputs.release_created }}` to deploy or `pnpm publish` / `uv publish` after the release PR merges.

### `release-please-config.json` — Node / TypeScript

```json
{
  "$schema": "https://raw.githubusercontent.com/googleapis/release-please/main/schemas/config.json",
  "release-type": "node",
  "packages": {
    ".": {}
  }
}
```

### `release-please-config.json` — Python

```json
{
  "$schema": "https://raw.githubusercontent.com/googleapis/release-please/main/schemas/config.json",
  "release-type": "python",
  "packages": {
    ".": {}
  }
}
```

### `.release-please-manifest.json` (initial)

```json
{
  ".": "0.1.0"
}
```

Set this to the current package version; release-please updates it automatically on each release.

### Monorepo

```json
{
  "$schema": "https://raw.githubusercontent.com/googleapis/release-please/main/schemas/config.json",
  "packages": {
    "packages/api": { "release-type": "node" },
    "packages/web": { "release-type": "node" },
    "services/worker": { "release-type": "python" }
  }
}
```

```json
{
  "packages/api": "1.2.0",
  "packages/web": "0.8.1",
  "services/worker": "0.3.0"
}
```

### Release types reference

| Stack | `release-type` |
|---|---|
| Node / TypeScript | `node` |
| Python | `python` |
| Generic (no version file) | `simple` |
| Go | `go` |

---

## Full Husky + lint-staged + commitlint wiring (TypeScript)

See `biome-eslint.md` for the `lint-staged` config. Complete pre-commit setup including commitlint:

```json
// package.json
{
  "scripts": {
    "prepare": "husky"
  },
  "lint-staged": {
    "*.{ts,tsx,js,jsx,json,css,md}": ["biome check --write --no-errors-on-unmatched"],
    "*.{tsx,jsx}": ["eslint --fix --max-warnings=0"]
  },
  "config": {
    "commitizen": {
      "path": "node_modules/cz-git"
    }
  }
}
```

Hook files:

```sh
# .husky/pre-commit
npx lint-staged

# .husky/commit-msg
npx --no -- commitlint --edit "$1"

# .husky/prepare-commit-msg
exec < /dev/tty && node_modules/.bin/cz --hook || true
```

---

## Skip flag

`git commit --no-verify` bypasses pre-commit and commit-msg hooks. Acceptable for local WIP commits only — never bypass in CI.
