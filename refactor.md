---
description: ♻️ Safe code refactoring workflow. Improve structure, readability, and maintainability without changing intended behavior.
---

# /refactor - Behavior-Preserving Refactor Mode

$ARGUMENTS

---

## Purpose

This command activates REFACTOR mode for structural improvement without intentionally changing user-visible behavior.

Use it when the goal is to make code easier to understand, safer to modify, less duplicated, better organized, and more maintainable.

---

## What Refactor Means

Refactor is:

- improving structure
- clarifying responsibilities
- reducing duplication
- extracting reusable units
- simplifying control flow
- improving naming
- strengthening module boundaries
- making future changes safer

Refactor is **not**:

- adding new features
- intentionally changing business behavior
- silently redesigning product requirements
- mixing unrelated bug fixes into the same pass
- rewriting large areas without evidence

If behavior must change, switch to `/enhance` or `/debug`.

---

## Core Rule

> **REFACTOR = CHANGE THE CODE SHAPE, KEEP THE BEHAVIOR CONTRACT**

Default expectation:

- internal structure may change
- external behavior should remain equivalent
- public contracts should remain stable unless explicitly approved
- every structural change should have a reason

---

## Why This Exists

Without a dedicated refactor workflow, teams often fall into one of these traps:

- call feature work "refactor" and hide behavior changes inside it
- keep duplicating logic because no one isolates shared patterns
- avoid cleanup because the path feels risky and unstructured
- over-refactor without proving what behavior must stay the same
- make sweeping changes without sufficient validation

`/refactor` exists to make cleanup disciplined instead of dangerous.

---

## When To Use

Use `/refactor` when:

- a module is too large or mixed-responsibility
- the same logic appears in multiple places
- naming is misleading
- dependencies point in the wrong direction
- control flow is hard to follow
- tests are hard to write because structure is tangled
- a future feature would be unsafe without cleanup first
- a subsystem needs separation before performance, security, or architecture work

Do **not** use `/refactor` when the primary goal is to add behavior or fix a user-facing bug.

---

## Supported Intents

```text
/refactor auth service
/refactor checkout flow without changing behavior
/refactor giant user component into smaller pieces
/refactor duplicated validation logic
/refactor api client structure
/refactor state management in dashboard
/refactor billing module for maintainability
/refactor before adding role permissions
```

---

## Relationship To Other Workflows

| Need | Best Workflow |
|------|---------------|
| Understand current system first | `/analyze` |
| Fix a broken behavior | `/debug` |
| Add or extend functionality | `/enhance` |
| Restructure safely without behavior change | `/refactor` |
| Validate or generate coverage | `/test` |
| Audit code quality and risks | `/review` |

If the target area is unfamiliar, run `/analyze` before `/refactor`.

---

## Refactor Goals

When `/refactor` is triggered, the assistant should improve one or more of these:

1. Readability
2. Cohesion
3. Separation of concerns
4. Duplication removal
5. Dependency direction
6. Naming clarity
7. Testability
8. Maintainability
9. Local reasoning
10. Safety of future change

Each refactor should explicitly state which of these goals it serves.

---

## Refactor Safety Contract

Before editing, define what must stay stable.

### Preserve By Default

- public API shape
- function inputs and outputs
- route behavior
- response shape
- rendered user behavior
- persistence semantics
- error semantics users rely on
- feature flag meaning
- existing configuration contract

### Only Change With Explicit Approval

- schema behavior
- endpoint contract
- UI flow
- business logic rules
- error message contract used externally
- file format or storage layout

If a proposed refactor would alter any of these, stop and ask for approval.

---

## Refactor Categories

### 1. Structural Extraction

Use when:

- one file is too large
- one function does too much
- view and logic are mixed

Examples:

- extract helper functions
- extract hooks/services/utilities
- split component by responsibility
- move persistence logic out of UI layer

### 2. Duplication Reduction

Use when:

- multiple modules repeat the same transformation
- validation logic is copied
- error handling pattern is repeated

Examples:

- create shared utility
- centralize schema validation
- consolidate repeated condition blocks

### 3. Boundary Cleanup

Use when:

- layers are leaking into each other
- module ownership is unclear
- shared utility folder is becoming a dump

Examples:

- move logic to owning domain
- remove forbidden dependency direction
- isolate side effects from pure logic

### 4. Naming And Intent Cleanup

Use when:

- names hide true responsibilities
- generic names increase confusion
- variables/functions/classes are misleading

Examples:

- rename symbols to reflect domain meaning
- split boolean conditions into named helpers
- replace ambiguous "data", "handle", "process" style names

### 5. Control Flow Simplification

Use when:

- nesting is too deep
- branching is hard to reason about
- state transitions are implicit

Examples:

- use early returns
- separate guards from main path
- make state transitions explicit

---

## Refactor Protocol

Follow this sequence unless evidence requires a safer variation.

### Phase 1: Understand Current Behavior

Before changing code:

- identify the target area
- trace the execution path
- map dependencies
- identify observable behavior that must stay stable
- find existing tests or usage that define expected behavior

If understanding is weak, switch to `/analyze` first.

### Phase 2: Define The Refactor Objective

State:

- what is wrong with the current structure
- why it slows development or increases risk
- what specific improvement is intended
- what must remain unchanged

Good examples:

- "Extract validation from controller so business rules are testable"
- "Split UI rendering from data loading to reduce component complexity"
- "Centralize duplicate transformation logic to remove divergence risk"

Bad examples:

- "Clean up code"
- "Make it better"
- "Rewrite for readability"

### Phase 3: Identify Safety Boundaries

List:

- public entry points
- exported symbols
- callers/consumers
- persistence boundaries
- side effects
- tests that should keep passing

Ask:

- What external contract could break?
- What hidden consumer might depend on this?
- What order-of-operations must remain intact?

### Phase 4: Choose The Smallest Safe Strategy

Prefer the smallest change set that achieves the goal.

Order of preference:

1. Rename for clarity
2. Extract helper/pure function
3. Extract module/service/hook
4. Reorganize dependencies
5. Split file or layer
6. Larger internal redesign

Avoid jumping to a full rewrite when an extraction or boundary cleanup is enough.

### Phase 5: Refactor In Small Atomic Steps

Each step should be behavior-preserving on its own.

Examples of safe atomic steps:

- rename a symbol and update references
- extract one pure helper and keep call site behavior
- move one side effect behind one abstraction
- split one component section into one child component
- replace duplicated logic with one shared function

After each step:

- re-check the changed file
- verify references
- ensure no accidental behavior drift

### Phase 6: Validate Behavior Parity

Validation priority:

1. Existing tests still pass
2. Critical flows still behave the same
3. Public interfaces remain compatible
4. Side effects still occur in correct order
5. Error handling still matches expectations

If tests do not exist, validate with:

- call path review
- usage comparison
- manual scenario checklist

### Phase 7: Document Tradeoffs And Residual Risk

At the end, state:

- what improved
- what stayed intentionally unchanged
- what still needs future cleanup
- where confidence is high vs partial

---

## Required Refactor Mindset

Always optimize for:

- smaller blast radius
- higher clarity
- lower coupling
- reversible steps
- stronger local reasoning

Do not optimize for:

- maximum novelty
- total rewrite aesthetics
- abstracting everything
- clever patterns without need

---

## Reading Strategy Before Refactor

Read enough to preserve behavior safely.

### Read Order

1. Entry point or public API
2. Main implementation
3. Direct callers and consumers
4. Shared types/interfaces/schemas
5. Tests that define behavior
6. Config only if it affects execution

### Things To Capture While Reading

- ownership of logic
- duplicated logic sites
- hidden dependencies
- side effects
- order-sensitive behavior
- state assumptions
- coupling hotspots

### For Large Files

If the file is large:

- identify the exact symbols to refactor
- read surrounding sections first
- expand until behavior boundaries are clear
- do not rewrite unrelated regions

---

## Behavior Preservation Checklist

Before completing the refactor, verify:

- same inputs still produce same outputs
- same routes/events still reach the same outcome
- same validation rules still apply
- same persistence actions still happen
- same state transitions still occur
- same error paths still exist where expected
- no unrelated feature behavior changed

---

## Refactor Heuristics

### When Extraction Is Safe

Usually safe when:

- logic is pure or nearly pure
- inputs/outputs are already clear
- repeated code is semantically identical
- the extracted unit has a single responsibility

### When Extraction Is Risky

Be careful when:

- code depends on implicit shared state
- ordering of side effects matters
- local variables encode hidden assumptions
- duplicate code looks similar but differs in business intent

### When Renaming Is High-Value

Renaming is often enough when:

- logic is correct but misleading
- maintenance mistakes come from unclear intent
- business concepts are blurred by generic names

### When Splitting Files Helps

Split when:

- one file mixes UI, side effects, and domain logic
- one module has multiple unrelated reasons to change
- code review becomes difficult because of file size

Do not split purely for aesthetics if ownership remains unclear.

---

## Anti-Patterns To Avoid

Do not:

- mix feature work into refactor without calling it out
- claim behavior-preserving change without validation
- replace readable code with abstract indirection for style points
- centralize logic that belongs to different domains
- deduplicate similar-looking logic that has different business meaning
- change public contract silently
- rewrite entire modules just because the code looks old
- remove comments that still explain important constraints

---

## Output Format

```markdown
## ♻️ Refactor Report: [Target]

### Objective
- Target: [module/file/subsystem]
- Goal: [why this refactor exists]
- Category: Structural Extraction | Duplication Reduction | Boundary Cleanup | Naming Cleanup | Control Flow Simplification

### Behavior Contract Preserved
- [what must remain unchanged]
- [what else must remain unchanged]

### Problems In The Original Structure
1. [specific structural problem]
2. [specific structural problem]
3. [specific maintenance risk]

### Refactor Plan
1. [atomic step]
2. [atomic step]
3. [atomic step]

### Changes Made
| Area | Change | Reason |
|------|--------|--------|
| `src/...` | Extracted helper | Reduce duplication |
| `src/...` | Renamed symbol | Clarify intent |
| `src/...` | Split responsibility | Improve cohesion |

### Validation
- Tests: [passed/not run/not available]
- Behavior parity check: [what was verified]
- Contract risk: Low | Medium | High

### Result
- [what improved]
- [what remains for later]

### Follow-Up
- Use `/test` to expand regression coverage around [area]
- Use `/review` to audit remaining complexity
```

---

## Short Output Format

For smaller refactors:

```markdown
## ♻️ Refactor: [Target]

- Goal: [one line]
- Preserved: [behavior contract]
- Main change: [one line]
- Validation: [one line]
- Remaining risk: [one line]
```

---

## Escalation Rules

Stop and ask for approval if the refactor would likely:

- change public behavior
- alter API/schema contract
- require broad cross-module rewiring
- remove code whose usage is uncertain
- combine with bug fixes or feature changes
- depend on assumptions not validated by code or tests

If confidence is low, reduce scope instead of pushing through.

---

## Handoff Matrix

After `/refactor`, choose next steps intentionally:

| Next Need | Workflow |
|-----------|----------|
| Verify unchanged behavior | `/test` |
| Audit residual risk | `/review` |
| Understand a confusing remaining area | `/analyze` |
| Fix a bug discovered during refactor | `/debug` |
| Add new behavior after cleanup | `/enhance` |

---

## Example Prompts

```text
/refactor auth controller without changing endpoint behavior
/refactor giant dashboard component into smaller modules
/refactor duplicated pricing calculations
/refactor notification service for clearer ownership
/refactor checkout state handling while preserving UX
/refactor api client layer before adding retry support
```

---

## Definition Of Done

`/refactor` is complete only when:

- structural objective is clearly stated
- behavior contract is explicitly preserved
- changes are scoped and justified
- affected dependencies were checked
- validation confirms no intentional behavior drift
- remaining risks or follow-ups are documented

If those are missing, the refactor is not complete.

---

## Final Rule

Refactor to make the next change cheaper, safer, and clearer.

Do not refactor to impress.

Do not refactor beyond what you can still explain and validate.
