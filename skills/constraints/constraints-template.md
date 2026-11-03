# Constraints

## Checks

Every check must pass before a task is done and before merge.

| Check        | Command             |
| ------------ | ------------------- |
| Type-check   | `npm run typecheck` |
| Lint, format | `npm run lint`      |
| Test         | `npm test`          |

Enforced by: pre-commit hook (staged files), CI (every push and pull request).

## Rules

Rules that need judgment, because no tool can check them.

1. A rule a reviewer can check, and why it matters.
