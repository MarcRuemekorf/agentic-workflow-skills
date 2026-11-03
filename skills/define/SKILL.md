---
name: define
description: Turns a feature request into an approved spec with testable acceptance criteria, written to docs/specs/<feature>/spec.md. Use when the user runs /define or asks to define or specify what to build, before any planning or code.
disable-model-invocation: true
---

# Define

## Purpose

Agree with the user on *what* to build before anyone decides *how*. The result is a spec that
`/prepare` plans from, `/test` verifies against, and `/inspect` reviews against.

## Inputs

- **The request**: what the user typed with the command, or the conversation so far. If there is no
  request, ask the user what they want to build and stop until they answer.
- **`CONSTRAINTS.md`** at the project root, if it exists. Read it so criteria don't contradict it.
  It is not required.
- **An existing spec** at `docs/specs/<feature>/spec.md`. If one exists, update it instead of starting
  over. If its `status` is `approved`, tell the user that changing it may invalidate the plan, and
  ask before editing.
- **The codebase**: the parts the feature touches.

## Steps

1. **Restate the request** in one or two sentences, in your own words, so misunderstandings surface early.
2. **Name the feature**: a short kebab-case name, e.g. `password-reset`. Check `docs/specs/` for an
   existing spec with the same or a similar name.
3. **Read the relevant code**: entry points, existing behavior close to what is asked, and the tests
   around it. The goal is to ground the spec in what already exists, not to design the solution.
   Anything you cannot confirm from the code becomes an open question.
4. **Draft the spec** by copying `spec-template.md` from this skill's directory and filling every
   section using the rules below. Set `feature` to the name from step 2 and `status` to `draft`.
5. **Write it** to `docs/specs/<feature>/spec.md`.
6. **Stop and ask.** Show the user the spec and ask every open question at once. Give a recommended
   answer for each, so the user can reply "agreed" or correct only what they disagree with.
7. **Apply the answers.** Move each answer into the section it affects and delete the question.
   Questions the user wants to defer stay in **Open questions**. Repeat steps 6 and 7 until no
   questions remain or the user defers the rest.
8. **Stop and ask for approval.** Only when the user explicitly approves, set `status` to `approved`.
   Never approve the spec yourself, and never approve it while **Open questions** has entries.

### Section rules

**Problem**: who has the problem, what it is, and why it matters now, in two to five sentences.
Describe the problem, not the solution.

**Scope**: a bulleted list of the capabilities the feature adds or changes, from the user's point of
view. Leave out implementation details such as file, class, or library names.

**Acceptance criteria**: a numbered list. Each criterion:

- Describes one observable behavior.
- Uses *Given <context>, when <action>, then <result>*, or a plain sentence when that is clearer,
  e.g. "`--help` prints usage and exits with code 0".
- Can be checked by an automated test or by concrete manual steps. Replace words like "fast",
  "simple", or "robust" with a number or a check, or leave them out.
- Includes failure and edge cases. For each behavior, ask what happens with invalid input, empty
  input, missing permissions, or a failing dependency, and add a criterion for each case that matters.

Numbering is permanent, because plans and evidence refer to criteria by number:

- Never renumber. A new criterion gets the next unused number.
- To drop a criterion, keep it, strike it through, and say why:
  `3. ~~Old criterion.~~ Removed: reason.`

**Open questions**: anything the request and the code don't settle. When none remain, write `None.`

## Output

- `docs/specs/<feature>/spec.md`, created or updated. No other files change: `/define` writes no code
  and no plan.

## Done when

- The spec exists with every section filled.
- **Open questions** says `None.`
- Every criterion follows the rules above.
- The user has explicitly approved it and `status` is `approved`.

Next: run `/prepare` to plan how to build it.
