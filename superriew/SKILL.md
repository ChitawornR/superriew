---
name: superriew
description: >
  Lean TDD development workflow — deep spec, git worktree isolation, strict
  Red-Green-Refactor, per-task review, agent dispatch, 4-gate verification, and branch
  finishing. Use whenever someone says "build X", "implement Y", "add feature", "fix bug",
  "refactor", or explicitly says "superriew". Invoke BEFORE writing any code, even for
  seemingly simple tasks. Chains through: superriew-spec → superriew-worktree →
  superriew-plan → superriew-build → superriew-verify → superriew-finish.
  If bugs appear: superriew-debug.
---

# Superriew

Lean TDD workflow combining deep spec with ponytail code quality.

```
superriew-spec → superriew-worktree → superriew-plan → superriew-build → superriew-verify → superriew-finish
                                                                ↕
                                                        superriew-debug  (when tests fail or bugs found)
```

## Jump Guide — skip phases you've already done

| Situation | Start here |
|-----------|-----------|
| New feature from scratch | `superriew-spec` |
| Have a spec, need a plan | `superriew-plan` |
| Have a plan, ready to build | `superriew-build` |
| Fixing code in an existing plan | `superriew-build` |
| Bug found, need to debug | `superriew-debug` |
| Code done, need to verify before committing | `superriew-verify` |
| Tests pass, need to merge/PR | `superriew-finish` |

Each skill is independent — invoke whichever phase fits your current state.
**Starting fresh? Invoke `superriew-spec` now.**
