# TypeScript recipe

Read this when the project uses TypeScript. Use the project's package manager everywhere below: the
examples use `npm`; with `pnpm-lock.yaml`, `yarn.lock`, or `bun.lock` use `pnpm`, `yarn`, or `bun`.

Prefer each tool's own init command over writing config by hand, so the config matches the installed
version.

## Defaults

| Area         | Keep if present                        | Default if absent                 |
| ------------ | -------------------------------------- | --------------------------------- |
| Type-check   | an existing type-check script          | `tsc --noEmit`                    |
| Lint, format | ESLint, Prettier, or Biome config      | Biome                             |
| Test         | any test runner already configured     | Vitest                            |
| Pre-commit   | Husky, Lefthook, or other git hooks    | Husky + lint-staged               |
| CI           | any existing CI config                 | GitHub Actions or GitLab CI       |

Never replace an existing tool without the user's agreement. Replacing ESLint and Prettier with Biome,
for example, is a decision for the user, not a default.

## Type-check

1. Add the script `"typecheck": "tsc --noEmit"` to `package.json`.
2. If `tsconfig.json` does not set `"strict": true`, recommend it. On an existing codebase, run the
   type-check with it first and tell the user how many errors it causes before enabling it.

## Lint and format: Biome

1. Install: `npm install --save-dev --save-exact @biomejs/biome`
2. Create the config: `npx @biomejs/biome init`
3. Add the scripts `"lint": "biome check ."` and `"format": "biome check --write ."`.

`biome check` runs the linter and the formatter check together.

## Test: Vitest

1. Install: `npm install --save-dev vitest`
2. Add the script `"test": "vitest run"`. Use `vitest run`, not `vitest`, which starts watch mode and
   never exits.

## Pre-commit: Husky + lint-staged

1. Install: `npm install --save-dev husky lint-staged`
2. Initialize: `npx husky init`. This creates `.husky/pre-commit` and adds a `prepare` script.
3. Replace the contents of `.husky/pre-commit` with `npx lint-staged`.
4. Add to `package.json`, using the project's lint tool. For Biome:

   ```json
   "lint-staged": {
     "*.{js,jsx,ts,tsx,json,jsonc,css}": "biome check --write --no-errors-on-unmatched"
   }
   ```

The hook checks staged files only, so commits stay fast. The type-check and tests run in CI.

## CI

Choose the platform from `git remote get-url origin`: a `github.com` remote means GitHub Actions, a
`gitlab` remote means GitLab CI. For any other remote, or none, ask the user. If CI config already
exists, add the missing checks to it instead of creating a new file.

Both run, in order: install, type-check, lint and format, test. In CI, use `biome ci .` instead of
`biome check .`: it never writes files and reports in a CI-friendly format.

### GitHub Actions: `.github/workflows/checks.yml`

```yaml
name: Checks
on:
  push:
    branches: [main]
  pull_request:
jobs:
  checks:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      - uses: actions/setup-node@v4
        with:
          node-version: lts/*
          cache: npm
      - run: npm ci
      - run: npm run typecheck
      - run: npx biome ci .
      - run: npm test
```

Replace `main` with the project's default branch.

### GitLab CI: `.gitlab-ci.yml`

```yaml
checks:
  image: node:lts
  cache:
    key:
      files: [package-lock.json]
    paths: [.npm/]
  script:
    - npm ci --cache .npm --prefer-offline
    - npm run typecheck
    - npx biome ci .
    - npm test
```
