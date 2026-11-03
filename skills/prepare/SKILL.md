---
name: prepare
description: Turns an approved spec into an approved plan of small, ordered, test-first tasks, written to docs/specs/<feature>/plan.md. Use when the user runs /prepare or asks to plan how to build a feature that already has a spec.
disable-model-invocation: true
---

# Prepare

## Purpose

Decide *how* to build an approved spec, as a short approach and a list of small tasks that `/build`
works through one at a time.

## Inputs

- **The spec** at `docs/specs/<feature>/spec.md`. Use the feature the user named. If they named none,
  list the specs in `docs/specs/` that have no plan yet and ask which one. If there is no spec, stop
  and tell the user to run `/define` first. If its `status` is not `approved`, stop and ask the user
  to approve it first.
- **`CONSTRAINTS.md`** at the project root, if it exists. The approach must follow it.
- **An existing plan** at `docs/specs/<feature>/plan.md`. If one exists, update it instead of starting
  over. If any task is already ticked, tell the user that changing finished tasks may mean redoing
  work, and ask before editing.
- **The codebase**: the parts the feature touches, and how its tests run.

## Steps

1. **Read the spec** in full, including struck-through criteria, so you know what was ruled out.
2. **Study the code the feature touches**: where the changes will go, existing patterns and libraries
   for similar behavior, and how tests are written and run. Write down the exact test command. If the
   project has no way to run tests, the first task sets one up.
3. **Check the spec against the code.** If a criterion is impossible, contradicts existing behavior,
   or misses a case you found, stop and tell the user. Do not edit the spec yourself; suggest running
   `/define` to update it.
4. **Choose the approach.** Follow existing patterns and libraries unless there is a concrete reason
   not to, and state that reason. Treat every new dependency as a decision and name it. Where two
   approaches are both reasonable, stop and ask the user, with a recommendation.
5. **Break the work into tasks** using the rules below.
6. **Write the plan** by copying `plan-template.md` from this skill's directory. Fill **Approach** and
   **Tasks**. Leave **Evidence** as the table header only: `/test` fills it. Set `feature` to match
   the spec and `status` to `draft`. Write it to `docs/specs/<feature>/plan.md`.
7. **Stop and ask for approval.** Show the user the approach and the tasks, and point out the
   decisions you made in step 4. Apply their changes. Only when they explicitly approve, set `status`
   to `approved`. Never approve the plan yourself.

### Task rules

- **One task is one test-first cycle**: write a failing test, make it pass, tidy up. It is usually one
  commit.
- **Order matters.** Each task builds only on tasks before it, and the tests pass after every task.
- **Keep tasks small.** If a task needs "and" to describe two separate changes, split it.
- **Describe the outcome, not the code**: what works after the task, not which lines change.
- **List the criteria each task covers**, as `(criteria: 1, 3)`. Tasks that only support others,
  such as setup or refactoring, may list none.
- **Cover every criterion.** Before writing the plan, check that every criterion in the spec that is
  not struck through appears in at least one task. If one doesn't, add a task.

## Output

- `docs/specs/<feature>/plan.md`, created or updated. No other files change: `/prepare` writes no code
  and does not edit the spec.

## Done when

- The plan exists with **Approach** and **Tasks** filled, and **Evidence** empty.
- Every criterion that is not struck through is covered by at least one task.
- The user has explicitly approved it and `status` is `approved`.

Next: run `/build` to build it task by task.
