# Plan

Reusable, agent-agnostic skills for an agentic software development workflow.
Six phase commands, each backed by a skill that names the helper skills it needs.

## Commands

| Command        | Purpose                                                     | Reads                         | Writes                              |
| -------------- | ----------------------------------------------------------- | ----------------------------- | ----------------------------------- |
| `/constraints` | Set the quality bar: decide once, enforce everywhere        | the codebase                  | `CONSTRAINTS.md`, tool config, CI   |
| `/define`      | Define what to build                                        | `CONSTRAINTS.md`              | `docs/specs/<feature>/spec.md`      |
| `/prepare`     | Plan how to build it                                        | spec                          | `docs/specs/<feature>/plan.md`      |
| `/build`       | Build incrementally, test-first, one task at a time         | plan, constraints             | code + tests, ticks tasks in plan   |
| `/test`        | Prove it works: verify every acceptance criterion           | spec, plan                    | evidence table in `plan.md`         |
| `/inspect`     | Review before merge, against spec and constraints           | diff, spec, plan, constraints | review report                       |

`/constraints` is phase 0: run once per project, revisit when the bar changes.

## Decisions

- **Harnesses (v1):** Claude Code and Codex CLI.
- **Single source of truth:** skills in the open `SKILL.md` format. Each phase command is itself a skill;
  harness-specific wrappers stay thin or are unnecessary.
- **Explicit skill loading:** phase skills reference helper skills by path instead of relying on
  description-based auto-activation, which varies by model.
- **Artifacts are committed** under `docs/specs/<feature>/` in the target project; `CONSTRAINTS.md` at its root.
- **`/constraints` enforces mechanically:** it writes the doc *and* generates linter, formatter, type-check,
  pre-commit and CI config. Only judgment-based rules stay prose-only.
- **Stack recipes (v1):** TypeScript. Other stacks fall back to doc-only.
- **Naming:** `/prepare` and `/inspect` avoid collisions with built-in `/plan`, `/review` and `code-review`.

## Steps

| #   | Step                                                                     | Done when                                                       |
| --- | ------------------------------------------------------------------------ | --------------------------------------------------------------- |
| 1   | Repo skeleton, `AGENTS.md`, skill-writing conventions                    | Conventions agreed                                              |
| 2   | Templates: the artifact contract (spec, plan, constraints)               | Templates reviewed                                              |
| 3   | `/constraints` + TypeScript recipe                                       | On the fixture: doc + working lint/type-check/CI config         |
| 4   | `/define` → `/prepare`                                                   | Fixture feature gets a spec and a task plan                     |
| 5   | `/build` (test-first loop) → `/test` (verify against criteria)           | Feature built, evidence table filled                            |
| 6   | `/inspect`                                                               | Catches a planted spec violation and a planted constraint one   |
| 7   | Packaging: Claude Code plugin + Codex install                            | Clean install works in both from a fresh clone                  |
| 8   | Cross-harness dogfood: full loop once in each                            | Same fixture feature completed in both                          |
| 9   | README, license, v0.1 tag                                                |                                                                 |

Each skill is tried on the fixture project (`examples/`) as soon as it is written.

## Open questions

- Packaging details per harness (plugin frontmatter, Codex skill locations, keeping phase skills
  user-invoked only) — verify against current docs in step 7.
- A `/debug` command for "it broke" — out of scope for v1.
