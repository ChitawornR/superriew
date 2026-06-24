---
name: superriew-spec
description: >
  Phase 1 of superriew — deep spec interview. Use after invoking superriew, or
  standalone when you need to fully understand a problem before writing code.
  Covers YAGNI check, codebase exploration, thorough interview, and spec agreement.
---

# Superriew — Phase 1: SPEC

Do not move to planning until you genuinely understand the problem end-to-end.
The bar is: you can explain it back and the user confirms you're right.

## Step 1 — YAGNI Gate (before any questions)

Ask: "Is there a version of this that's significantly simpler and still solves the real need?"
If yes, surface it first:

> "You asked for X. Y would cover this with much less code — does that work?"

Wait for the answer before continuing.

## Step 2 — Explore Existing Code

Before asking the user anything, read what already exists:
- Search for related files, utils, types, or patterns
- Check installed dependencies that might already solve part of this
- Look for conventions to follow

You're looking for: things to reuse, constraints you'd miss without reading the code.

## Step 3 — Deep Interview

Ask until you have zero remaining uncertainty. No question limit.
Ask one question at a time. Wait for the answer before asking the next.

Cover all relevant areas:

**Purpose and behavior**
- What is the exact behavior? Walk through it step by step.
- What does success look like from the user/caller's perspective?
- Are there multiple valid success states?

**Data and interfaces**
- What data comes in and out? What are the types/shapes?
- Must this match existing interfaces or contracts?
- What are valid inputs? How should invalid inputs behave?

**Edge cases and failure modes**
- What happens at boundaries? (empty, null, max size, concurrent calls)
- What errors can occur? Thrown, returned, or logged?
- Is there shared state or race conditions to worry about?

**Constraints**
- Performance: does this need to handle scale, or is naive fine?
- Compatibility: must existing callers remain unchanged?
- Security: does this touch trust boundaries (user input, auth, external data)?
- Reversibility: does this mutate shared state or write to persistent storage?

**Scope**
- What is explicitly out of scope?
- Anything to defer to a follow-up?
- What must not break?

## Step 4 — Propose Approaches

Once you understand the problem, propose 2-3 concrete approaches before writing the spec.
Lead with your recommendation. Be direct about the trade-offs — don't list every option as equally good.

Format each option conversationally:

> **Option 1 (Recommended): [name]**
> [2-3 sentences: what it does, why it's the right call here]
> Trade-off: [what you give up]
>
> **Option 2: [name]**
> [2-3 sentences: what it does]
> Trade-off: [what you give up]
>
> **Option 3: [name]** *(only if genuinely distinct)*
> [2-3 sentences]
> Trade-off: [what you give up]

Ask the user which approach to proceed with before writing the spec.
If they pick a different option than your recommendation, note any concerns once — then follow their choice.

## Step 5 — Spec Agreement

Write the spec. User approves before proceeding.

```
Spec: [feature name]

Behavior:
1. [input → output or state change]
2. [...]

Acceptance criteria:
- [command or assertion that proves it works]
- [edge case handled correctly]

Out of scope:
- [explicit exclusions]

Constraints:
- [perf, compat, security, or "none"]

Assumptions I'm making:
- [anything inferred, not stated — user can correct]
```

If the user changes anything: update the spec and confirm again.
Only proceed when spec is agreed.

---

**When spec is approved → invoke `superriew-plan`.**
