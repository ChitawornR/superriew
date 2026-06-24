---
name: superriew-plan
description: >
  Phase 2 of superriew — file map and TDD task breakdown. Use after superriew-spec
  is complete and spec is approved. Maps which files to touch and breaks work into
  Red-Green-Refactor tasks, marking which can run in parallel.
---

# Superriew — Phase 2: PLAN

Turn the agreed spec into a concrete file map and TDD task list.

## File Map

List every file you'll create or touch:

```
path/to/file.ts  →  what this file is responsible for (one line)
```

Before creating a new file, ask: "Does this need to exist? Could it live in an existing file?"
No file that exists only as a placeholder or to satisfy a collapsible import.

## Task Breakdown

Each task = one TDD cycle = one test going red then green.
A correctly-sized task has exactly one assertion to watch fail.

```
Task 1: [name]
  Files:    [list]
  RED:      [what behavior this test asserts]
  GREEN:    [minimal code to implement]
  REFACTOR: ponytail ladder (see superriew-build)
  Commit:   [commit message]
```

Mark tasks `[PARALLEL]` only when they:
- Touch different files with no shared state, AND
- Don't depend on each other's output

Tasks are sequential when one depends on another, or they write to the same file.

## Review the Plan

Before proceeding, check:
- Every spec bullet has at least one task covering it
- No task is doing more than one thing
- Parallel markers are accurate (conflicts at merge would be costly)

---

**When plan is written → invoke `superriew-build`.**
