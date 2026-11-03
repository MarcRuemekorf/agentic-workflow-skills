---
name: inspect
description: Reviews a branch before merge against its spec, plan, evidence, and CONSTRAINTS.md, reports ranked findings with a merge verdict, and offers to fix them test-first. Use when the user runs /inspect or asks for a review before merging.
disable-model-invocation: true
---

# Inspect

## Purpose

Decide whether the work is ready to merge. Check that it builds what the spec asked for, nothing more,
in the way the plan described, within the project's constraints, and without bugs a reviewer would
catch.

## Inputs

- **The changes**: the current branch compared with the project's default branch, plus any uncommitted
  changes. If you are on the default branch, ask the user what to review.
- **The spec and plan** at `docs/specs/<feature>/`. Use the feature the user named, or the one the
  branch's changes belong to. If you can't tell, ask. If there is no spec or plan, review against
  `CONSTRAINTS.md` and the correctness checks only, and say so in the report.
- **`CONSTRAINTS.md`** at the project root, if it exists.

## Steps

1. **Collect the changes**: the list of changed files and the full diff.
2. **Choose the reviewer.** If you can run sub-agents, give steps 3 and 4 to a new sub-agent. Give it
   the diff, the spec, the plan, `CONSTRAINTS.md`, and the checklist below, but not this conversation:
   a reviewer that didn't write the code doesn't share its blind spots. Otherwise do the review
   yourself and say in the report that the builder reviewed its own work.
3. **Review** against every item in the checklist below. For each problem, note the file and line.
4. **Verify each finding.** Keep it only if you can describe a concrete case where it goes wrong, or
   point to the exact criterion, task, or constraint it breaks. Drop findings you can't support.
5. **Rank the findings**:
   - **Must fix**: a bug, an unmet criterion, a failing test or check, a broken constraint, a security
     problem, or a change outside the spec's scope.
   - **Should fix**: makes the code harder to understand or change, or a missing edge-case test.
   - **Note**: worth knowing, no change needed.
6. **Report** the findings, most severe first, each with its location and the reason. End with a
   verdict: **ready to merge** if there are no must-fix findings, otherwise **not ready**.
7. **Stop and ask** which findings to fix. Recommend all must-fix findings. Also ask once whether to
   commit after each fix.
8. **Fix** each chosen finding. When it is about behavior, first write a test that fails because of
   it, then fix the code. Run the full test suite and the checks after every fix, and commit if the user
   agreed. If a fix turns out to be large or changes the approach, stop and suggest `/prepare` instead.
9. **Check the fixes again** with step 3, for the changed lines only, and give the final verdict.

### Checklist

- **Spec**: every criterion that is not struck through is implemented. Nothing is built that the
  **Scope** doesn't include.
- **Evidence**: the Evidence table has a `pass` row for every criterion. Run the test suite yourself,
  because the code may have changed since `/test` ran.
- **Plan**: every task is ticked, and the code follows the **Approach**. Any departure is explained.
- **Constraints**: the checks in `CONSTRAINTS.md` pass, and the rules that need judgment are followed.
- **Correctness**: logic errors, unhandled errors, missing edge cases, and resources that are never
  released.
- **Security**: unvalidated input, injection, missing authorization checks, and secrets in code or logs.
- **Tests**: no existing test was weakened, skipped, or deleted without a reason, and new tests assert
  results instead of only running code.
- **Leftovers**: debug output, commented-out code, unexplained TODOs, and changes unrelated to the feature.

## Output

- A review report in the conversation. `/inspect` writes no report file.
- Fixes and their tests, and one commit per fix if the user agreed.

## Done when

- Every checklist item has been reviewed.
- No must-fix findings remain, or the user chose to leave them.
- The test suite and the checks pass.
- The user has been given the final verdict.

Next: merge, following the project's own process.
