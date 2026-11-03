# AGENTS.md

Instructions for any agent working on this repository. The plan and its decisions live in
[docs/PLAN.md](docs/PLAN.md); read it before making structural changes.

## What this repo is

A library of skills, not an application. The "code" is Markdown instructions that other agents follow,
so wording is the product: ambiguity here becomes inconsistent behavior in someone else's project.

## Layout

```
skills/<name>/SKILL.md          one skill per directory; directory name == skill name
skills/<name>/references/       detail loaded only when a step needs it
templates/                      the artifact contract: spec.md, plan.md, CONSTRAINTS.md
examples/                       fixture project used to try skills out
docs/                           plan and design notes for this repo
```

Phase skills (`define`, `prepare`, `build`, `test`, `constraints`, `inspect`) are the six commands.
Every other skill is a helper that a phase skill loads explicitly.

## Writing skills

### Frontmatter

```yaml
---
name: acceptance-criteria        # lowercase-hyphenated, matches the directory
description: Writes testable acceptance criteria for a feature spec. Use when ...
---
```

The description says what the skill does **and** when to use it, in the third person. Add no other
fields until step 7 settles which ones each harness accepts.

### Model-neutral wording

Skills must work with any capable model in any harness.

- Describe actions, not tools: "read the file", "run the test command", never a harness tool name.
- Make optional capabilities conditional: "If you can run sub-agents, ...; otherwise ...".
- Use imperative steps with concrete outputs. Avoid "consider", "as appropriate", "if relevant"
  unless you also say how to decide.
- Don't rely on a model's defaults or "common sense" for anything that matters; state it.

### Explicit loading

A phase skill names each helper it needs at the step that needs it, by path relative to the repo root:

> Before writing criteria, read `skills/acceptance-criteria/SKILL.md` and follow it.

Never assume a helper will activate on its own.

### Size

Keep `SKILL.md` short enough to read in one pass (aim for under ~200 lines). Move stack-specific or
rarely needed detail into `references/` and say exactly when to read it.

### Phase skill anatomy

Every phase skill uses these sections, in this order:

1. **Purpose** — one or two sentences.
2. **Inputs** — the files it reads; what to do if one is missing (usually: stop and point to the
   phase that produces it).
3. **Steps** — numbered. Mark each point where the agent must stop and get the user's confirmation.
4. **Output** — the exact files it writes or updates, using the paths from `templates/`.
5. **Done when** — checkable exit criteria, and which phase comes next.

## The artifact contract

Phases communicate only through files. `templates/` is the source of truth for their paths and
structure. When you change a template, update every skill that reads or writes that artifact in the
same change.

## Trying changes

There is no test suite for prose. After writing or changing a skill, run it against the fixture in
`examples/` and check its output against the skill's **Done when** section. Note anything the agent
misread and fix the wording, not the fixture.
