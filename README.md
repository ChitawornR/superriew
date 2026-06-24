# Superriew

Lean TDD workflow for AI coding assistants. Combines deep spec-first thinking with ponytail code quality (shortest working diff, no over-engineering) across isolated git worktrees.

```
superriew-spec → superriew-worktree → superriew-plan → superriew-build → superriew-verify → superriew-finish
                                                                 ↕
                                                         superriew-debug  (when tests fail)
```

## What each skill does

| Skill | Phase | Purpose |
|-------|-------|---------|
| `superriew` | Entry | Overview and jump guide |
| `superriew-spec` | 1 | Deep interview — YAGNI check, codebase exploration, spec agreement |
| `superriew-worktree` | 1.5 | Git worktree isolation before implementation starts |
| `superriew-plan` | 2 | File map and TDD task breakdown |
| `superriew-build` | 3 | Red-Green-Refactor with per-task review and agent dispatch |
| `superriew-verify` | 4 | 4-gate verification: tests, types/lint, code review agent, build |
| `superriew-finish` | 5 | Merge locally, push PR, keep, or discard |
| `superriew-debug` | — | Systematic root-cause debugging (invoked from build or verify) |

## Installation

### Claude Code

Copy the skill folders into your Claude skills directory:

```bash
git clone https://github.com/ChitawornR/superriew.git
cp -r superriew/superriew* ~/.claude/skills/
```

Each skill becomes available as `/superriew`, `/superriew-spec`, etc. in Claude Code.

Verify by running `/superriew` — Claude will load the skill and show the jump guide.

### Gemini CLI

Copy the skill folders into Gemini's skills directory (check your Gemini CLI config for the exact path, usually `~/.gemini/skills/`):

```bash
cp -r superriew/superriew* ~/.gemini/skills/
```

Skills activate via the `activate_skill` tool once placed there.

### Codex

Copy into the Codex skills directory:

```bash
cp -r superriew/superriew* ~/.codex/skills/
```

Skills load natively — Codex discovers them automatically.

### Copilot CLI

Copy into the Copilot skills directory:

```bash
cp -r superriew/superriew* ~/.copilot/skills/
```

Use via the `skill` tool. Skills are auto-discovered from installed plugins.

### Other AI tools

Each skill is a folder containing a single `SKILL.md` file. Copy the `superriew*` folders into whatever directory your tool uses for skills or prompts.

## Usage

Start any new feature or bug fix with:

```
/superriew
```

or jump directly to any phase:

```
/superriew-spec        # new feature — start here
/superriew-plan        # already have a spec
/superriew-build       # already have a plan
/superriew-debug       # tests are failing
/superriew-verify      # code done, ready to check
/superriew-finish      # all gates pass, ready to ship
```

## Requirements

- Git (for worktree isolation)
- A test runner appropriate for your project (Jest, pytest, cargo test, etc.)

## License

MIT
