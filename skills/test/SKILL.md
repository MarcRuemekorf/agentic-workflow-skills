---
name: test
description: Proves a built feature meets its spec by checking every acceptance criterion against a passing test or a manual check the user performs, and records the result in the Evidence table of docs/specs/<feature>/plan.md. Use when the user runs /test or asks to verify that a feature works.
---

# Test

## Purpose

Prove that the feature does what the spec says, criterion by criterion, with evidence someone else can
check. Passing tests alone are not enough: each criterion needs a test or manual check that actually
shows its result.

## Inputs

- **The spec** at `docs/specs/<feature>/spec.md`: the criteria to prove. Use the feature the user named.
  If they named none, list the plans in `docs/specs/` whose tasks are all ticked and ask which one.
- **The plan** at `docs/specs/<feature>/plan.md`: the test command in **Approach**, and the tasks and
  the criteria they cover. If no plan exists, stop and tell the user to run `/prepare` and `/build`
  first. If tasks are still unticked, tell the user and ask whether to verify anyway or finish with
  `/build` first.
- **`CONSTRAINTS.md`** at the project root, if it exists, for the checks that must pass.
- **The codebase**, especially the tests.

## Steps

1. **List the criteria** from the spec. Skip struck-through ones.
2. **Run the full test suite and the checks** from `CONSTRAINTS.md`. Note every failure.
3. **Find the evidence for each criterion.** Look for tests with the criterion number in their name or
   description, then for tests added by the tasks that cover it.
4. **Judge each test.** It counts as evidence only if it sets up the criterion's context, performs its
   action, and asserts its result. A test that runs the code without checking that result does not
   count. A criterion with error or edge cases needs those cases tested too.
5. **Close the gaps.** For a criterion with no adequate test:
   - **If it can be tested automatically**, write the missing test and run it.
   - **If it can only be checked by hand**, such as how something looks, write concrete manual steps:
     the setup, the exact actions, and the result to expect.
6. **Stop and ask for manual checks.** Give the user all manual steps at once, numbered by criterion.
   Record exactly what they report. Never mark a manual check as passed yourself.
7. **Fill the Evidence table** in the plan, one row per criterion that is not struck through. Replace any
   earlier rows.
   - **Evidence**: the test file and test name, e.g. `src/auth.test.ts`: "criterion 2: rejects an
     expired token", or `Manual:` followed by the steps.
   - **Result**: `pass` only if the test ran and passed, or the user reported the expected result.
     Otherwise `fail`, with a short reason.
8. **Report.** Summarize how many criteria pass and list every failure with its reason. If you noticed
   behavior that looks wrong but no criterion covers it, report that separately; don't add it to the
   table.
9. **Stop and ask** whether to commit the new tests and the updated plan.

### Staying on track

- **Change tests and the plan only.** Never change product code here, even when a fix looks small. A
  failing criterion goes back to `/build`: suggest adding a task to the plan with `/prepare`, then
  running `/build`.
- **Base every result on a run or an observation**, never on reading the code and expecting it to work.

## Output

- The **Evidence** table in `docs/specs/<feature>/plan.md`, filled.
- New tests for criteria that had none.
- A commit of both, if the user agreed.

## Done when

- Every criterion that is not struck through has a row in the Evidence table.
- Every row is `pass`, and every manual check was verified by the user.

Next: run `/inspect` to review before merge. If any row is `fail`, fix it with `/prepare` and `/build`,
then run `/test` again.
