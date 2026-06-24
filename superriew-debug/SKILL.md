---
name: superriew-debug
description: >
  Systematic debugging for superriew — use when any test fails, bug is found, or
  behavior is unexpected during superriew-build or superriew-verify. Also use
  standalone for any debugging task. Root cause first, fix second. No guessing.
---

# Superriew — Debug

Random fixes waste time. Ponytail applies here too: fix the root cause once, not the symptom in every caller.

## The Iron Law

```
NO FIX WITHOUT ROOT CAUSE IDENTIFIED FIRST
```

If you haven't completed Phase 1, you cannot propose a fix.

---

## Phase 1 — Find Root Cause

Do all of these before writing a single line of fix:

**1. Read the error completely**
Don't skim. Stack traces contain the exact file, line, and reason. Note them.

**2. Reproduce it**
Can you trigger it reliably with a minimal case? If not — gather more data, don't guess.

**3. Check what changed**
```bash
git diff HEAD~1  # or git log --oneline -10
```
The bug usually lives in the last change. New dependency? Config change? Env difference?

**4. Trace the data flow**
Where does the bad value originate? What called this with it?
Trace backward up the call stack until you find the source — not where it crashes, but where it was created wrong.

**5. In multi-component systems: add instrumentation first**
If the system has layers (API → service → DB, CI → build → sign), add logging at each boundary *before* guessing which layer is broken. Run once to see where the data goes wrong. Then investigate that layer.

---

## Phase 2 — Find the Pattern

**Find a working example** in the same codebase that does something similar.
Compare it line by line to the broken code. List every difference, however small.
Don't assume "that can't matter" — it probably does.

---

## Phase 3 — Form and Test One Hypothesis

Write it down explicitly: "I think X is failing because Y."

Test with the smallest possible change — one variable at a time.
- Worked? → Phase 4
- Didn't work? → Form a new hypothesis. Do NOT stack more fixes on top.

**If you've tried 3+ fixes and none worked:** stop attempting fixes.
This is an architectural problem, not a bug. The current approach is wrong.
Discuss with the user before trying anything else.

---

## Phase 4 — Implement the Fix

1. **Write a failing test that reproduces the bug** (RED)
   — this proves the fix actually works and prevents regression

2. **Apply the single root-cause fix** (GREEN)
   — at the source, not at the symptom
   — if the same guard is needed in 3 callers, put it in the shared function once

3. **Run ponytail ladder on the fix** (REFACTOR)
   — a bug fix doesn't justify surrounding cleanup, but the fix itself should be minimal

4. **Verify** — run the full test suite, not just the new test

---

## Red Flags — Stop and Return to Phase 1

You're rationalizing if you think:
- "Quick fix for now, investigate later"
- "Just try changing X and see"
- "It's probably X" (without tracing)
- "I don't fully understand but this might work"
- "One more fix attempt" (after 2 already failed)

Every one of these thoughts means: go back to Phase 1.

---

**When bug is fixed and tests pass → return to `superriew-build` or `superriew-verify`.**
