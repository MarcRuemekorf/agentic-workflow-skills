---
name: build
description: Builds a feature from its approved plan, one test-first task at a time, ticking each task in docs/specs/<feature>/plan.md and committing per task if the user agrees. Use when the user runs /build or asks to implement a feature that already has an approved plan.
---

# Build

## Purpose

Turn an approved plan into working code, one small test-first task at a time, so the codebase works
after every task and the plan always shows where the work stands.

## Inputs

- **The plan** at `docs/specs/<feature>/plan.md`. Use the feature the user named. If they named none,
  list the plans in `docs/specs/` with unticked tasks and ask which one. If there is no plan, stop and
  tell the user to run `/prepare` first. If its `status` is not `approved`, stop and ask the user to
  approve it first.
- **The spec** at `docs/specs/<feature>/spec.md`, for the criteria each task covers.
- **`CONSTRAINTS.md`** at the project root, if it exists. All code must follow it, and its checks
  (for example lint and type-check commands) must pass after every task.
- **The codebase**, and the test command recorded in the plan's **Approach**.

## Steps

1. **Find the next task**: the first unticked task in the plan. If every task is ticked, tell the user
   and point them to `/test`.
2. **Check the starting point.** Run the test command and the checks from `CONSTRAINTS.md`. If anything
   fails before you have changed a line, stop and report it: don't build on a broken base. If there are
   uncommitted changes, ask the user whether to commit, stash, or keep them before you start, because
   they would otherwise end up in your commits.
3. **Stop and ask, once per run**:
   - "Commit after each task?" Recommend yes.
   - "Pause after each task for review, or continue through the remaining tasks?"

   Follow the answers for the rest of this run without asking again.
4. **Build the task** with this cycle:
   1. **Write a failing test** for what the task says will work. When the task covers a criterion,
      put the criterion number in the test's name or description, e.g. `criterion 2: rejects an
      expired token`, so `/test` can find it.
   2. **Run it and confirm it fails for the expected reason**, not because of a typo or a missing
      import. If it already passes, the behavior exists: stop and ask the user whether the task is
      still needed.
   3. **Write the least code that makes it pass**, following the plan's **Approach** and
      `CONSTRAINTS.md`.
   4. **Run the full test suite and the checks.** Everything must pass, not just the new test.
   5. **Tidy up** while the tests stay green: names, duplication, dead code from this task.

   Tasks that add no behavior, such as setup or a refactor, skip the failing test. The existing tests
   must still pass afterward.
5. **Tick the task** in the plan: `- [ ]` becomes `- [x]`.
6. **Commit**, if the user agreed in step 3. Include the code, the tests, and the ticked plan in one
   commit. Follow the project's commit message style, as shown by recent commits. The message says what
   works after the task.
7. **Pause or continue**, as agreed in step 3. When pausing, summarize what the task changed and ask
   whether to continue. Then go back to step 1.

### Staying on track

- **Do only what the task says.** If you notice something else worth changing, mention it to the user
  when you pause or finish. Don't change it.
- **Never weaken, skip, or delete an existing test to make the suite pass.** If an existing test
  conflicts with the task, stop and ask.
- **Stop when the plan is wrong.** If a task can't be done as planned, is much bigger than it looked,
  or the approach turns out not to work, stop and explain. Suggest updating the plan with `/prepare`.
  Don't quietly build something different.
- **Stop when stuck.** If the tests still fail after three different attempts at a fix, stop and report
  what you tried and what you think is wrong.

## Output

- Code and tests for each finished task.
- Ticked tasks in `docs/specs/<feature>/plan.md`.
- One commit per task, if the user agreed.

## Done when

- Every task in the plan is ticked.
- The full test suite and the checks from `CONSTRAINTS.md` pass.
- Each task is committed, if the user agreed.

Next: run `/test` to prove every criterion is met.
