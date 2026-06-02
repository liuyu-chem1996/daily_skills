---
name: composition-guidelines
description: Use when implementing or refactoring code that must stay tightly scoped while improving local readability, naming, duplication, and simple design.
license: MIT
---

# Composition Guidelines

This skill combines `karpathy-guidelines` and `kent-beck-style` into a single executable workflow. Its purpose is to prevent two common failure modes:
- being simple without clear boundaries, which leads to opportunistic refactors,
- being surgical without any design judgment, which leaves the code harder to read and maintain.

Core split of responsibilities:
- `karpathy-guidelines` defines boundaries, restraint, assumptions, and verification.
- `kent-beck-style` improves intent, readability, local design, and incremental refactoring.
- When both apply, protect scope first, then improve design only where the current task touches.

## When to Use

Use this skill when:
- the user wants a fix or feature and also wants the touched code to be cleaner,
- you need to balance minimal change with better readability,
- you notice code smells near the requested change but are unsure what is safe to improve now,
- you want a consistent rule for combining local refactoring with strict scope control.

## Default Workflow

Follow this order. Do not skip steps.

### 1. Define Scope Before Coding

Use `karpathy-guidelines` first.
- State the goal, assumptions, constraints, and non-goals.
- Clarify what this task will solve and what it will intentionally not solve.
- If the success condition cannot be stated clearly, do not start refactoring yet.

### 2. Deliver the Smallest Working Change

Start with the minimum code that satisfies the request.
- Do not add abstractions for hypothetical future reuse.
- Do not add configuration, extension points, or generic layers without a present need.
- Do not widen the change just because a broader cleanup looks attractive.

### 3. Review the Touched Area with Kent Beck's Lens

After the minimal solution works, review only the code path touched by the task.
Check these priorities in order:
1. Does it still pass verification?
2. Does the code reveal intent?
3. Did this change introduce or preserve obvious duplication?
4. Did this change add unnecessary elements?

Only refactor when the smell directly affects correctness, clarity, or maintainability of the current task.

### 4. Apply the Smallest Useful Refactor

Prefer small refactors over structural rewrites.
Allowed default moves:
- rename unclear variables, functions, or constants,
- extract a small method when the new name explains intent better than inline code,
- remove duplication introduced or expanded by this change,
- replace new magic numbers with named constants,
- use guard clauses to reduce fresh nesting,
- remove imports, variables, or helpers made unused by this change.

Avoid large refactors unless the user explicitly asked for them.

### 5. Verify, Do Not Just Admire the Code

Finish with explicit verification.
- Prove the requested behavior works.
- Check that adjacent existing behavior still works.
- Confirm that the refactor did not expand the scope beyond the request.

## Priority Rules for Conflicts

If the two styles seem to pull in different directions, use this order:

1. **Correctness first**
   - Working behavior and passing checks come before design improvement.

2. **User scope second**
   - No refactor is allowed to cross the boundary of the user's request.

3. **Simple implementation over elegant abstraction**
   - If an abstraction is only theoretically reusable, do not add it.

4. **Intent over mechanical DRY**
   - Do not extract a confusing abstraction just to remove a few repeated lines.

5. **Local improvement over system-wide cleanup**
   - If the real problem is architectural and larger than the task, mention it but do not fix it opportunistically.

## What This Skill Encourages

- Solve today's problem with today's level of design.
- Make the touched code easier to read, not more impressive.
- Refactor in small steps that are easy to verify.
- Keep every changed line traceable to the current request.
- Prefer clearer names and simpler control flow over additional architecture.

## What This Skill Forbids by Default

- speculative generality,
- broad cleanup unrelated to the request,
- cross-module rewrites triggered by a local change,
- removing old dead code that predates the task,
- adding layers, patterns, or configurability without an active use case,
- changing surrounding style, comments, or naming just because you are nearby.

## Division of Labor

### Use `karpathy-guidelines` as the brake
- It stops assumption drift.
- It stops scope creep.
- It stops overengineering.
- It forces explicit success criteria and verification.

### Use `kent-beck-style` as the local design filter
- It detects the most harmful nearby code smells.
- It improves naming and readability.
- It removes duplication when doing so makes the current change easier to understand.
- It keeps refactoring incremental and safe.

## Pre-Submission Checklist

Before you finish, check all of these:
- Did I state the goal and the non-goals?
- Did I implement the smallest change that solves the task?
- Is every refactor directly justified by the current request?
- Did I improve only the touched path, not unrelated areas?
- Did I verify behavior instead of stopping at a cleaner-looking diff?

## One-Sentence Rule

Use `karpathy-guidelines` to set boundaries and verification first, then use `kent-beck-style` only to improve the design of the code directly touched by the task; if an improvement goes beyond the current request, do not do it.
