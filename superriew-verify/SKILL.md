---
name: superriew-verify
description: >
  Phase 4 of superriew — 4-gate verification before claiming completion. Use after
  superriew-build, or standalone before any commit, PR, or "done" claim. Runs tests,
  types/lint, dispatches a code reviewer agent for the full diff, and build. No gate
  can be skipped.
---

# Superriew — Phase 4: VERIFY

No completion claim without fresh evidence from all four gates.
Run in order — each must pass before moving to the next.

## Gate 1 — Tests

```bash
[test command]
```

Requirement: 0 failures. Read the output. Count failures. Check exit code.
If any fail: stop → invoke `superriew-debug`. Do not continue to Gate 2.

## Gate 2 — Types / Lint

```bash
[tsc --noEmit | eslint | mypy | go vet | ruff | etc.]
```

Requirement: 0 errors.
Warnings: fix if <30s; skip only if they preexisted before your changes.

## Gate 3 — Code Review (agent)

Get the diff of all changes on this branch:
```bash
BASE=$(git merge-base HEAD main 2>/dev/null || git merge-base HEAD master)
git diff $BASE HEAD
```

Dispatch a reviewer agent:

```
Review this branch diff for ponytail code quality.

Spec (what was built):
[paste the agreed spec from superriew-spec]

Diff:
[paste git diff output]

Focus on:
1. Readability — can a future maintainer understand each function without explanation?
2. Minimal — is there anything more complex than the spec required?
3. Single responsibility — does each function/file do exactly one thing?
4. Missing ponytail: comments — any deliberate simplifications that aren't marked?

Rate each finding: Critical (must fix) / Important (fix before merge) / Minor (optional).
Report overall: Ready to merge / Needs fixes.
```

- **Critical or Important findings**: fix them, re-run Gate 3
- **Minor findings**: fix if easy, otherwise add `// ponytail:` comment and proceed
- **Ready to merge**: continue to Gate 4

## Gate 4 — Build / Integration

```bash
[build command or integration test]
```

If no build step: exercise the actual feature path end-to-end — not just unit tests.

## Completion Claim

Only after all four gates pass with fresh output:

> "Done. Gates: tests ✓ (N passed, 0 failed), types ✓ (0 errors), review ✓, build ✓."

Never use "should pass", "probably fine", or express satisfaction before running gates.

---

**When all gates pass → invoke `superriew-finish`.**
