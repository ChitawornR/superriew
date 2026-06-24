---
name: superriew-worktree
description: >
  Phase 1.5 of superriew — set up an isolated git worktree before implementation.
  Use after superriew-spec is approved, before superriew-plan. Also use standalone
  when starting any feature work that needs branch isolation from the current workspace.
  Detects existing isolation, prefers native tools, falls back to git worktree.
---

# Superriew — Worktree Setup

Work happens in an isolated branch. This protects main from incomplete changes and lets
agents work without interfering with each other.

## Step 0 — Detect Existing Isolation

```bash
GIT_DIR=$(cd "$(git rev-parse --git-dir)" && pwd -P)
GIT_COMMON=$(cd "$(git rev-parse --git-common-dir)" && pwd -P)
# Check if inside a submodule (not a worktree)
git rev-parse --show-superproject-working-tree 2>/dev/null
```

- `GIT_DIR != GIT_COMMON` and NOT a submodule → already in a worktree. **Skip to Step 3.**
- `GIT_DIR == GIT_COMMON` → normal repo checkout. Continue to Step 1.

## Step 1 — Create Isolated Workspace

**Try in this order — stop at the first that works:**

### 1a. Native tool (preferred)

Does the platform provide a worktree tool? (`EnterWorktree`, `/worktree`, `--worktree` flag, etc.)
If yes, use it. Skip to Step 2. Native tools handle placement and cleanup automatically.

### 1b. Git worktree fallback

Use only if no native tool is available.

**Pick a directory:**
1. Explicit instruction from user? Use it.
2. `.worktrees/` exists at project root? Use it.
3. `worktrees/` exists? Use it.
4. Neither? Default to `.worktrees/`.

**Verify it's git-ignored before creating:**
```bash
git check-ignore -q .worktrees 2>/dev/null || echo "NOT IGNORED"
```
If not ignored: add to `.gitignore`, commit, then proceed.

**Create:**
```bash
BRANCH=<feature-name-from-spec>   # kebab-case, derived from spec
git worktree add .worktrees/$BRANCH -b $BRANCH
cd .worktrees/$BRANCH
```

If `git worktree add` fails with a permission error: work in the current directory instead, create the branch normally.

## Step 2 — Install Dependencies

Auto-detect and run:
```bash
[ -f package.json ]      && npm install
[ -f Cargo.toml ]        && cargo build
[ -f requirements.txt ]  && pip install -r requirements.txt
[ -f pyproject.toml ]    && poetry install
[ -f go.mod ]            && go mod download
```

## Step 3 — Verify Clean Baseline

```bash
[test command]
```

- **Tests pass** → report "Worktree ready at `<path>`, branch `<name>`, N tests passing."
- **Tests fail** → report failures, ask whether to proceed or investigate first.

---

**When worktree is ready → invoke `superriew-plan`.**
