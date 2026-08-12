---
name: clean-code
description: Audit and refactor code for maintainability, clarity, duplication, complexity, coupling, naming, testability, and unnecessary abstraction while preserving behavior and public contracts.
argument-hint: "[scope] [--audit|--apply] [--changed] [--strict] [--tdd] [--review]"
---

# /clean-code — Behavior-Preserving Clean Code Workflow

Improve code quality without turning cleanup into a redesign.

The workflow has two responsibilities:

1. **Audit** code quality with evidence-backed findings.
2. **Refactor** confirmed issues using the smallest safe changes while preserving behavior.

The governing rule is:

> CLEAN CODE = IMPROVE CODE SHAPE, PRESERVE BEHAVIOR CONTRACT.

---

## Core Contract

By default, preserve all externally observable behavior unless the user explicitly requests a behavior change.

Preserve:

- public APIs
- routes and URLs
- request/response contracts
- exported interfaces
- function inputs/outputs
- persistence semantics
- database constraints and transaction meaning
- emitted events/messages
- configuration contracts
- error semantics relied on by callers
- user-visible behavior
- authorization rules
- timing/order semantics when meaningful

Do not silently combine clean-code work with feature work, architecture redesign, dependency migration, or bug fixing beyond issues directly caused by the refactor.

---

## Default Mode

When invoked without flags:

```text
/clean-code [scope]
```

perform:

```text
Understand
  -> Audit
  -> Rank findings
  -> Refactor confirmed high-value findings
  -> Verify behavior
  -> Code review
```

If the requested scope is very broad or the repository has weak tests, favor conservative changes and report larger opportunities rather than rewriting them immediately.

---

## Flags

| Flag | Behavior |
|------|----------|
| `--audit` | Read-only clean-code audit; report findings, do not modify source |
| `--apply` | Apply confirmed findings after audit |
| `--changed` | Restrict primary audit to changed code plus directly affected call sites |
| `--strict` | Include lower-severity maintainability debt normally omitted |
| `--tdd` | Establish/refine characterization tests before risky refactors |
| `--review` | Force a final `ak:code-review` pass even for small scopes |

`--audit` and `--apply` are mutually exclusive. Default behavior is equivalent to `--apply`, but remains conservative when behavior cannot be proven.

Examples:

```text
/clean-code src/features/orders
/clean-code src/services/payment.ts --audit
/clean-code --changed
/clean-code src/modules/auth --tdd --review
/clean-code src --audit --strict
```

---

# Phase 0 — Resolve Scope and Baseline

Before judging code style:

1. Resolve repository root and requested scope.
2. Detect language/framework and existing conventions.
3. Identify existing tests, lint rules, formatter, type checker, and build commands.
4. Identify public boundaries inside the scope.
5. Identify current uncommitted/changed files when relevant.
6. Record explicit exclusions.

Prefer repository conventions over generic clean-code preferences.

### Baseline Output

```text
Scope: ...
Languages/frameworks: ...
Tests: ...
Lint/format/typecheck: ...
Public boundaries: ...
Excluded areas: ...
```

---

# Phase 1 — Understand Before Refactoring

Do not clean code that has not been understood.

For each target area, determine:

- what the module owns
- entry points
- callers
- dependencies
- inputs and outputs
- state mutations
- external side effects
- error behavior
- persistence behavior
- concurrency assumptions
- tests that prove current behavior

Use `ak:scout` for broad discovery when available.

Use `ak:gkg` for reference/call-site impact analysis when useful.

For non-trivial behavior, use `/analyze` if installed or trace the flow manually.

### Refactor Safety Rule

If behavior cannot be confidently established from code/tests/docs:

```text
DO NOT GUESS.
```

Either:

- create characterization tests first, or
- report the opportunity without modifying it.

---

# Phase 2 — Clean-Code Audit

Audit actual maintainability problems, not personal style preferences.

## 2.1 Naming

Flag names when they materially reduce understanding.

Look for:

- misleading names
- ambiguous abbreviations
- names inconsistent with domain language
- boolean names that do not read as predicates
- generic containers such as `data`, `info`, `obj`, `tmp` when intent is unclear
- functions whose names hide mutation or side effects
- inconsistent terminology for the same concept

Do not rename purely because another synonym sounds nicer.

Public/exported renames require explicit compatibility handling.

---

## 2.2 Functions and Methods

Look for functions that are difficult to understand or change because they:

- have multiple unrelated responsibilities
- mix orchestration with low-level details
- contain deep nesting
- contain excessive branching
- contain duplicated branches
- rely on many boolean flags
- mutate hidden/global state
- have surprising side effects
- use excessive parameters
- pass the same parameter cluster repeatedly
- mix validation, persistence, transport, and presentation concerns unnecessarily

Do not apply arbitrary line-count limits.

A long function with one coherent responsibility may be clearer than five artificial helpers.

---

## 2.3 Duplication

Distinguish harmful duplication from coincidental similarity.

Flag duplication when multiple copies represent the **same concept or business rule** and are likely to drift.

Examples:

- repeated authorization rule
- repeated pricing calculation
- repeated validation contract
- repeated serialization mapping
- repeated error translation
- repeated API request construction
- repeated UI state transition logic

Do NOT automatically extract code merely because several lines look similar.

Prefer duplication over the wrong abstraction.

---

## 2.4 Complexity

Look for complexity that increases reasoning cost:

- deeply nested control flow
- branch explosion
- large switch/if chains encoding a hidden state machine
- boolean combinations that are difficult to reason about
- mixed sync/async control paths
- non-local state mutation
- temporal coupling
- repeated null/error checks obscuring the main path
- conditionals duplicating domain rules

Simplify by clarifying decisions, not by hiding them behind abstractions.

---

## 2.5 Responsibilities and Cohesion

Flag modules/classes/components that own unrelated responsibilities.

Typical symptoms:

- HTTP controller also performs database queries and business calculations
- UI component handles presentation, networking, persistence, analytics, and domain rules together
- utility module becomes a dumping ground
- service knows details of unrelated subsystems
- domain code imports framework/transport concerns unnecessarily

Do not force a universal layered architecture.

Preserve the project's existing architectural style unless restructuring is explicitly requested.

---

## 2.6 Coupling and Dependencies

Look for harmful coupling:

- circular dependencies
- broad modules imported everywhere
- hidden global dependencies
- direct dependence on unstable implementation details
- business logic tightly bound to framework or IO when this blocks testing/change
- modules requiring knowledge of another module's internals
- dependency direction inconsistent with the repository's established boundaries

Do not introduce dependency injection or interfaces where they add no real value.

---

## 2.7 Abstraction Quality

Audit both under-abstraction and over-abstraction.

### Under-abstraction

- same business rule implemented repeatedly
- callers repeatedly reconstruct the same protocol
- repeated coordination logic that should have one owner

### Over-abstraction

- one-use wrappers with no semantic value
- generic factories obscuring simple construction
- interface + implementation pairs with no alternative or boundary benefit
- excessive indirection
- speculative extension points
- configuration abstractions harder to understand than direct code

Do not reward more layers. Reward clearer ownership and cheaper change.

---

## 2.8 Error Handling

Look for:

- swallowed errors
- catch-and-ignore
- inconsistent error translation
- exceptions used for ordinary control flow when harmful
- loss of original error context
- duplicated error mapping
- returning success after partial failure
- ambiguous sentinel values
- user/internal errors mixed together

Preserve externally relied-on error contracts unless explicitly changing them.

---

## 2.9 State and Side Effects

Flag code where important mutations are hard to see or reason about:

- hidden global mutation
- mutation during getters/selectors
- non-obvious cache writes
- mutation of caller-owned inputs
- state updated in multiple distant places
- side effects inside formatting/mapping helpers
- ordering-dependent calls without documentation/tests

Prefer explicit boundaries around effects when that makes behavior easier to verify.

---

## 2.10 Dead and Redundant Code

Look for evidence-backed:

- unreachable code
- unused private functions
- obsolete compatibility branches
- stale feature-flag branches
- unused imports/variables
- duplicate validation
- redundant wrappers
- obsolete comments

Do not delete public/exported code merely because no local reference is found; it may be an external contract.

---

## 2.11 Comments and Documentation in Code

Prefer code that explains **what** while comments explain **why**.

Flag:

- comments that contradict code
- comments narrating obvious syntax
- large commented-out code blocks
- TODOs already completed or no longer actionable
- comments compensating for misleading names or unnecessarily complex structure

Do not remove useful rationale, domain constraints, safety notes, protocol details, or non-obvious tradeoffs.

---

## 2.12 Constants and Magic Values

Flag unexplained literals when meaning matters or the value is reused as a domain rule.

Good extraction targets:

- protocol values
- business thresholds
- timeout/retry settings
- repeated domain constants

Do not extract every literal into a constant merely to satisfy style ideology.

---

## 2.13 Data Shape and Transformation

Look for:

- repeated object reshaping
- unclear conversions between DTO/domain/persistence models
- inconsistent nullability/default handling
- lossy conversions
- repeated parsing/normalization
- data shape assumptions spread across many call sites

Prefer a clear owner for important transformations.

---

## 2.14 Testability

Flag code that is hard to test because behavior is unnecessarily entangled with:

- clocks
- randomness
- network
- filesystem
- process globals
- singleton mutable state
- framework runtime
- hidden side effects

Do not redesign code solely to make mocking easier. Improve seams only when they also improve ownership/clarity or are needed for safe verification.

---

## 2.15 Frontend-Specific Cleanliness

For frontend code, additionally inspect:

- giant components with unrelated responsibilities
- duplicated server-state logic
- state derived redundantly from other state
- effects used for pure derivation
- unstable or unnecessary memoization
- event handlers duplicating business rules
- presentation mixed with complex domain decisions
- prop drilling caused by unclear ownership
- duplicated form validation
- UI-only authorization mistaken for security enforcement

When React/Next.js is present, use `ak:react-best-practices` for framework-specific guidance rather than duplicating its rules here.

---

## 2.16 Backend-Specific Cleanliness

For backend code, additionally inspect:

- transport concerns mixed with domain rules
- transaction boundaries spread across unrelated layers
- repository/data access duplicated inconsistently
- authorization duplicated or missing at trust boundaries
- domain calculations inside controllers/serializers
- inconsistent request/response mapping
- repeated integration retry/error policies
- hidden N+1 or repeated IO caused by poor ownership

Use `ak:backend-development` and its code-quality references for stack/domain-specific guidance when available.

---

# Phase 3 — Classify Findings

Every finding must have evidence and a practical impact.

Use this structure:

| Field | Meaning |
|------|---------|
| ID | `CC-001`, `CC-002`, ... |
| Severity | P1/P2/P3 |
| Category | duplication, complexity, naming, coupling, etc. |
| Location | file + symbol/line |
| Evidence | concrete code behavior/structure |
| Why It Matters | maintenance/change/testability impact |
| Proposed Change | smallest reasonable refactor |
| Behavior Risk | low/medium/high |
| Verification | tests/checks needed |
| Status | confirmed / deferred / rejected |

### Priority

**P1** — significant defect risk or major maintenance hazard:

- duplicated security/business invariant likely to drift
- severe hidden side effects
- critical code impossible to safely change
- dangerous state/transaction ambiguity
- circular or cross-boundary coupling causing real failures/change hazards

**P2** — meaningful maintainability cost:

- high complexity
- repeated domain logic
- poor cohesion
- difficult testability
- misleading structure

**P3** — lower-impact clarity debt:

- naming
- local simplification
- redundant private wrappers
- stale comments

Do not report cosmetic preferences as P1/P2.

---

# Phase 4 — Challenge the Findings

Before editing, remove weak findings.

For each proposed refactor ask:

1. Does this make the code easier to understand or change?
2. Is the problem evidenced, or only stylistic preference?
3. Does the proposed abstraction have a clear owner and semantic meaning?
4. Will it reduce duplication/complexity, or merely move it?
5. Does it increase indirection?
6. Does it change public behavior?
7. Does it increase blast radius beyond the benefit?
8. Would leaving the code alone be safer and clearer?

Reject findings that fail this challenge.

A clean-code audit is successful even when it concludes that some unusual code should remain unchanged.

---

# Phase 5 — Establish Behavior Safety

Before medium/high-risk refactors:

1. Locate tests proving current behavior.
2. Run the narrow relevant suite when practical.
3. Add characterization tests if behavior is insufficiently protected and code modification is in scope.
4. Record public contracts and important side effects.
5. Inspect callers/call sites before changing shared symbols.

If `--tdd` is set, this gate is mandatory for every non-trivial refactor.

Prefer `ak:cook --tdd` for implementation when available.

### Characterization Test Principle

The test should capture **current intended/relied-on behavior**, not freeze accidental implementation details.

Avoid snapshots or overly broad mocks when direct behavioral assertions are possible.

---

# Phase 6 — Refactor in Small Atomic Steps

Apply confirmed changes from lowest blast radius upward.

Recommended order:

1. remove proven dead/redundant private code
2. improve misleading local names
3. simplify local control flow
4. extract coherent local helpers
5. consolidate confirmed duplicated business rules
6. clarify state/effect ownership
7. separate responsibilities where current coupling causes real maintenance cost
8. adjust module boundaries only when evidence justifies it

After each meaningful step:

- keep code buildable when practical
- run narrow verification
- check behavior parity
- inspect diff for unrelated changes

Do not accumulate a giant rewrite before testing.

---

# Phase 7 — Clean-Code Transformation Patterns

Use patterns only when they improve clarity in the actual context.

## Guard Clauses

Use guard clauses to reduce deep nesting when early exit expresses the business flow more clearly.

Do not flatten control flow if nesting communicates meaningful transaction/resource scope.

## Extract Function

Extract when a block has a coherent semantic name or separates a distinct level of abstraction.

Do not create tiny helpers that force readers to jump files/functions without gaining meaning.

## Extract Domain Rule

Centralize a business invariant when multiple sites implement the same rule.

Verify all callers before consolidation.

## Replace Boolean Flags

When flags create unclear combinations, consider explicit modes/options/types.

Do not replace simple booleans that remain obvious.

## Clarify State Machine

When state transitions are scattered or invalid states are easy to create, make transitions explicit.

Do not introduce a state-machine framework for a simple two-state flow.

## Introduce Value Object / Domain Type

Use when primitive values carry important validation/meaning across many boundaries.

Avoid ceremony for values with no real domain behavior.

## Separate Pure Logic from IO

Useful when business calculations are buried inside network/DB/UI effects.

Keep orchestration understandable; do not fragment it across needless layers.

## Consolidate Error Translation

Centralize repeated mappings when one boundary owns the translation.

Do not erase useful context or force unrelated errors into one generic type.

---

# Phase 8 — Verification

Verification is mandatory after modifications.

Run the smallest relevant checks first, then broaden based on blast radius.

Possible checks:

```text
focused unit/component tests
integration/API tests
typecheck
lint
build
E2E for affected critical paths
```

Use `ak:test` as the test execution source of truth when available.

### Behavior-Parity Gate

Before completion verify:

- public signatures unchanged unless explicitly approved
- routes/contracts unchanged
- output/error behavior preserved
- auth/permission behavior preserved
- persistence/transaction semantics preserved
- side-effect count/order preserved when relevant
- existing relevant tests still pass
- new characterization tests pass
- no new lint/type/build errors

Never claim parity solely from visual inspection.

---

# Phase 9 — Final Code Review

Use `ak:code-review` after meaningful refactors.

Reviewer focus:

- regressions
- unintended contract changes
- abstraction quality
- overengineering
- missed call sites
- error-path drift
- test gaps
- new coupling introduced by cleanup

Do not ask the reviewer merely whether the code "looks cleaner".

For small purely local mechanical cleanup, this step may be skipped unless `--review` is supplied.

---

# Output for `--audit`

Produce:

```text
## Clean Code Audit

Scope: ...
Files inspected: N
Confirmed findings: N
Rejected/deferred findings: N

### Findings
[P1/P2/P3 table]

### Highest-value refactor sequence
1. ...
2. ...
3. ...

### Behavior risks / missing tests
- ...

### Areas intentionally left unchanged
- ... + reason
```

Do not modify source in audit mode.

---

# Output for Apply Mode

Summarize:

```text
## Clean Code Result

Scope: ...
Confirmed findings addressed: N
Deferred: N
Behavior-changing proposals skipped: N

Changed:
- ...

Preserved contracts:
- ...

Verification:
- command/check -> result

Remaining debt:
- ...
```

If verification cannot be completed, state exactly what remains unverified.

---

# Anti-Patterns

Do NOT:

- rewrite code only because you prefer another style
- apply SOLID/design patterns mechanically
- create interfaces for every class
- create repositories/services/controllers because a generic architecture says so
- split every large function/component
- extract every repeated 3-line block
- replace readable code with clever abstractions
- introduce generic helpers without a clear owner
- rename public APIs without compatibility analysis
- change behavior under the label of cleanup
- refactor unrelated areas while touching a file
- mix dependency upgrades into cleanup unless required
- delete public/exported code based only on local reference search
- remove comments containing rationale or safety constraints
- use test mocks to conceal behavior regressions
- weaken tests so refactored code passes
- optimize performance without measurement when performance is not the stated problem
- convert straightforward code into a design-pattern showcase

---

# Stop / Escalation Conditions

Stop automatic cleanup and report instead when:

- expected behavior is ambiguous
- required tests do not exist and characterization is unsafe/impossible
- proposed cleanup requires public contract changes
- architecture changes are needed across many modules
- database/schema behavior must change
- concurrency semantics are unclear
- security-sensitive logic cannot be proven
- the change becomes feature work rather than refactoring

Suggested escalation:

```text
Architecture problem      -> /architecture or ak:plan
Behavior bug              -> ak:debug / ak:fix
Performance problem       -> /optimize
Large risky rewrite       -> ak:plan --hard/--deep
Test coverage uncertainty -> /test-audit
```

---

# Skill Routing

Use specialized skills as sources of truth:

```text
Discovery / orientation       -> ak:scout
Call-site impact analysis     -> ak:gkg
Quality review                -> ak:code-review
Safe implementation           -> ak:cook --tdd
Test execution                -> ak:test
React / Next.js guidance      -> ak:react-best-practices
Backend clean-code guidance   -> ak:backend-development
Behavior diagnosis            -> ak:debug
Bug fixing                    -> ak:fix
Test coverage mapping         -> /test-audit
```

If a referenced skill is unavailable, continue with native repository analysis rather than pretending it exists.

---

# Recommended Routing by User Intent

```text
"review my code"
    -> ak:code-review

"clean this code"
"refactor this module"
"simplify this code"
"remove duplication"
"improve maintainability"
    -> /clean-code

"find clean-code problems only"
    -> /clean-code --audit

"clean changed files"
    -> /clean-code --changed

"clean but make sure behavior cannot change"
    -> /clean-code --tdd --review
```

---

# Completion Definition

A `/clean-code` run is complete only when:

- scope is explicit
- current behavior and public boundaries are understood sufficiently
- findings are evidence-backed
- stylistic-only findings are filtered out
- applied refactors address confirmed problems
- unrelated code is untouched
- public behavior is preserved unless explicitly approved otherwise
- relevant verification has run successfully or remaining gaps are disclosed
- meaningful refactors receive a final review
- remaining debt and deferred high-risk opportunities are reported

The goal is not maximum abstraction or minimum line count.

The goal is code that is easier to understand, safer to change, and no less correct than before.
