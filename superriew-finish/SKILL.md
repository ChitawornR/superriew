---
name: superriew-finish
description: >
  Phase 5 of superriew — finishing a development branch after all gates pass. Presents
  options: merge locally, push and create PR, keep as-is, or discard. Use after
  superriew-verify, or standalone when implementation is complete and you need to
  integrate the work.
---

# Superriew — Phase 5: FINISH

## Step 1 — Confirm tests still pass

```bash
[test command]
```

If failing: stop → invoke `superriew-debug`. Don't proceed.

## Step 2 — Detect environment

```bash
GIT_DIR=$(cd "$(git rev-parse --git-dir)" && pwd -P)
GIT_COMMON=$(cd "$(git rev-parse --git-common-dir)" && pwd -P)
```

- `GIT_DIR == GIT_COMMON` → normal repo
- `GIT_DIR != GIT_COMMON` → inside a worktree (cleanup required for options 1 & 4)

## Step 3 — Present options

```
Implementation complete. What would you like to do?

1. Merge to <base-branch> locally
2. Push and create a Pull Request
3. Keep branch as-is (handle it later)
4. Discard this work

Which option?
```

## Step 4 — Execute

### Option 1 — Merge locally

```bash
MAIN_ROOT=$(git -C "$(git rev-parse --git-common-dir)/.." rev-parse --show-toplevel)
cd "$MAIN_ROOT"
git checkout <base-branch> && git pull
git merge <feature-branch>
[run tests again on merged result]
```

If in a worktree:
```bash
git worktree remove <worktree-path>
git worktree prune
```

Then delete branch: `git branch -d <feature-branch>`

### Option 2 — Push and create PR

```bash
git push -u origin <feature-branch>
# create PR with gh or via browser
```

Do NOT remove the worktree — you'll need it for PR feedback.

### Option 3 — Keep as-is

Report: "Branch `<name>` kept. Resume when ready."

### Option 4 — Discard

Confirm first — require the user to type `discard`:

```
This will permanently delete branch <name> and all its commits.
Type 'discard' to confirm.
```

After confirmation:
```bash
cd "$MAIN_ROOT"
[worktree remove if applicable]
git branch -D <feature-branch>
```

---

## Red Flags

- Never merge with failing tests
- Never delete work without typed confirmation
- Never remove a worktree before the merge succeeds
- Never force-push without explicit user request
- Never run `git worktree remove` from inside that worktree — `cd` to main root first
