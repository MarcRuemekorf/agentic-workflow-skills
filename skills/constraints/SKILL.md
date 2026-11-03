---
name: constraints
description: Sets a project's quality bar once and enforces it everywhere, by writing CONSTRAINTS.md and setting up the type-check, lint, format, test, pre-commit, and CI tooling that checks it. Use when the user runs /constraints, sets up a project for the workflow, or wants to change the project's quality rules.
---

# Constraints

## Purpose

Decide the project's quality bar once, and make tools enforce as much of it as possible. Whatever a
tool can check becomes a command in `CONSTRAINTS.md` and runs in a pre-commit hook and in CI. Only rules
that need judgment stay as prose. `/build`, `/test`, and `/inspect` all read `CONSTRAINTS.md`.

## Inputs

- **The codebase**: `package.json` and lockfiles, `tsconfig.json`, existing lint, format, and test
  config, git hooks, and CI config.
- **An existing `CONSTRAINTS.md`** at the project root. If one exists, update it instead of starting
  over, and ask before removing any check or rule.

## Steps

1. **Detect the stack.** The project uses TypeScript if it has a `tsconfig.json` or depends on
   `typescript`. If it does, read `references/typescript.md` in this skill's directory and follow it in
   the steps below. For any other stack there is no recipe yet: skip the tooling steps, record the
   project's existing check commands in `CONSTRAINTS.md`, and tell the user no tooling was set up.
2. **Take stock.** For each area, type-check, lint and format, test, pre-commit, and CI, note which
   tool and command the project already uses, or that it has none. Run each existing check and note
   whether it passes.
3. **Stop and ask.** Propose, for each area, whether to keep what exists or add the recipe's default,
   and say why. Propose a short list of rules for **Rules**, using the rules for rules below, and ask
   which rules the user wants to add. Ask everything at once, each with a recommendation.
4. **Set up the tooling** the user agreed to, following the recipe.
5. **Run every check.** Existing code often fails new checks. If any check fails, stop and offer the
   choices, with a recommendation:
   - **Apply safe automatic fixes**, such as formatting, in a separate commit so they don't hide real
     changes.
   - **Turn off a specific rule**, named, with the user's reason recorded under **Rules**.
   - **Fix the code** where no automatic fix exists. This is often the right choice for a few errors.

   Never turn rules off without asking. `/build` needs every check to pass, so leaving checks failing
   blocks the rest of the workflow.
6. **Write `CONSTRAINTS.md`** by copying `constraints-template.md` from this skill's directory. List
   every check with its exact command and what enforces it, and the agreed rules.
7. **Stop and ask** whether to commit. Recommend separate commits for the tooling setup and for
   automatic fixes.

### Rules for rules

- **Only what tools can't check.** If a linter rule, type setting, or test could enforce it, use that
  instead.
- **Checkable by a reviewer**: concrete enough that two reviewers would agree whether code breaks it.
  "Keep functions small" fails this; "No function over 50 lines without a comment explaining why"
  passes.
- **Each with its reason**, in one short clause.
- **Few**: every phase reads every rule. Aim for no more than ten.

## Output

- `CONSTRAINTS.md` at the project root.
- For TypeScript projects: config files, `package.json` scripts, the pre-commit hook, and CI config, as
  agreed with the user.

## Done when

- Every check in `CONSTRAINTS.md` runs with its listed command and passes.
- The pre-commit hook and CI run the checks, unless the user chose not to have them.
- The user agreed to every rule under **Rules**.

Next: run `/define` to define the first feature.
