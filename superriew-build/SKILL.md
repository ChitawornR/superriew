---
name: superriew-build
description: >
  Phase 3 of superriew — strict TDD execution with ponytail code quality. Use after
  superriew-plan, OR standalone when you already have a plan and want to implement or
  fix specific code without replanning. Runs Red-Green-Refactor for sequential tasks,
  dispatches agents for parallel tasks. Enforces ponytail ladder at every REFACTOR step,
  per-task review after each task, and a progress ledger to survive context compaction.
---

# Superriew — Phase 3: BUILD

## Before Starting — Progress Ledger

Check for an existing ledger first:
```bash
cat .superriew/progress.md 2>/dev/null
```
If it exists, tasks listed as `✓` are DONE — skip them, resume at the first incomplete task.
If it doesn't exist: `mkdir -p .superriew && touch .superriew/progress.md`

After each task completes review: append one line to the ledger:
```
✓ Task N: [name] — commits <base7>..<head7>, review clean
```
Trust the ledger over memory after any context compaction.

---

## Sequential Tasks

One TDD cycle per task. After each cycle: review, then ledger entry.

### RED — Write the failing test first

Write the test. Run it. Confirm it fails for the right reason:
- Not "file not found" or a syntax error
- The actual assertion failure you intended

One logical assertion per test. Test behavior, not internals.

```typescript
// good — tests observable behavior
test('returns empty array when input is empty', () => {
  expect(parse([])).toEqual([])
})

// bad — tests implementation detail (brittle)
test('calls normalize() once', () => {
  expect(normalize).toHaveBeenCalledTimes(1)
})
```

### GREEN — Minimum code to pass

Write only what makes this test pass. The ugly version is fine — REFACTOR handles it.
No helpers "for later." No abstractions the test doesn't require.
If you wrote more than needed: delete it. Start fresh from the test.

### REFACTOR — Ponytail ladder (run before every commit)

Stop at the first rung that holds:

1. **Exists?** Does this file/function need to exist? Delete if not.
2. **Stdlib?** Does the standard library already do this? Use it.
3. **Codebase pattern?** Is there an existing util or pattern here? Use it.
4. **One line?** Can this be expressed in one line? Make it one line.
5. **Abstraction?** Is there an abstraction nobody asked for? Remove it.
6. **Readable?** Does intent read clearly without a comment? If not: one line max, explain WHY not WHAT.

Mark deliberate trade-offs:
```
// ponytail: naive O(n²) scan — fine for <100 items, switch to Map if throughput matters
// ponytail: global lock — fine for single-user, per-tenant locks if concurrency matters
```

Run all tests. Still green? Commit.

### Per-Task Review

After each commit, dispatch a reviewer agent:

```
Review this diff for Task [N]: [task name]

Spec requirement for this task:
[paste the task's RED/GREEN description from the plan]

Diff:
[git diff <base_sha>..<head_sha>]

Check two things only:
1. Spec compliance — does the implementation match what was planned?
2. Code quality — is anything harder to read or more complex than necessary?
   Apply ponytail standard: shortest readable code, no unrequested abstractions,
   mark any // ponytail: comments that are missing.

Report: Spec ✓/✗, Quality ✓/✗, and specific findings (if any).
```

- **Spec ✗ or Quality ✗**: dispatch a fix agent, re-review until both pass
- **Both ✓**: append to ledger, move to next task

If tests fail during build → stop, invoke `superriew-debug`.

---

## Parallel Tasks — Agent Dispatch

For each `[PARALLEL]` task, dispatch a separate agent. Give each agent exactly what it needs — nothing more.

**Note on isolation:** If tasks modify files that could conflict at the filesystem level, use git worktrees (`git worktree add .worktrees/<branch> -b <branch>`) so agents work in isolated copies.

**Agent prompt template:**

```
Task: [task name]
Files to create/modify: [list]
Do NOT modify: [files owned by other parallel tasks]

Step 1 — RED: Write this test first:
[describe or paste the exact test]
Run it. Confirm it fails for the right reason (not a syntax error or missing import).

Step 2 — GREEN: Implement minimum code to pass:
[describe minimal implementation]
No helpers, no abstractions beyond what the test requires.

Step 3 — REFACTOR (ponytail ladder):
1. Need to exist? 2. Stdlib? 3. Codebase pattern? 4. One line? 5. Abstraction nobody asked for?
Mark deliberate simplifications: // ponytail: [reason, ceiling, upgrade path]

Run all tests. Still green? Commit. Report base SHA and head SHA.

Codebase context:
[paste: relevant existing types, interfaces, or code this task depends on]
```

After all agents finish:
- Check for conflicts in shared files
- If two agents independently wrote the same util: consolidate
- Dispatch a reviewer for each agent's diff (same template as per-task review above)
- Fix any issues before continuing

---

**When all tasks pass review → invoke `superriew-verify`.**
**If any test fails or bug is found during build → invoke `superriew-debug`.**
