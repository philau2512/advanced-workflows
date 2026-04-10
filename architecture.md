---
description: 🏛️ Practical architecture workflow for reviewing, enforcing, and improving boundaries, ownership, dependency direction, and structural integrity in real codebases.
---

# /architecture - Structural Integrity Mode

$ARGUMENTS

---

## Purpose

This command activates ARCHITECTURE mode for evaluating or improving the structural design of a system.

Use it when the goal is to understand, enforce, or evolve module boundaries, dependency direction, ownership, layering, and long-term maintainability.

---

## Core Rule

> **ARCHITECTURE = CLARIFY OWNERSHIP, ENFORCE BOUNDARIES, REDUCE STRUCTURAL RISK**

Default behavior:

- map the current architecture from evidence
- identify boundary violations and coupling hotspots
- distinguish design intent from implementation drift
- recommend the smallest structural change that improves integrity
- avoid architecture theater

---

## What This Is Not

`/architecture` is not:

- a generic refactor
- a code style review
- a rewrite mandate
- a framework preference debate
- an excuse to over-engineer a small product

If the real goal is to change behavior, use `/enhance`.
If the real goal is to improve structure without broader architectural concerns, use `/refactor`.

---

## When To Use

Use `/architecture` when:

- module boundaries are unclear
- feature ownership is scattered
- dependency direction is wrong
- shared utilities are becoming a dumping ground
- domain logic leaks into UI/controllers/routes
- data access appears in the wrong layer
- circular dependencies exist or seem likely
- scale is making the codebase harder to reason about
- a big feature/refactor needs structural guardrails first

---

## Relationship To Other Workflows

| Need | Workflow |
|------|----------|
| Understand flow and code first | `/analyze` |
| Improve general structure safely | `/refactor` |
| Split a large file | `/split-file` |
| Add capability | `/enhance` |
| Deep feature work with safety | `/advanced-enhance` |
| Review quality and regressions | `/review` |
| Audit security boundaries | `/security` |
| Execute a prepared plan | `/execute-plan` |

---

## Architecture Review Goals

When `/architecture` is triggered, try to answer:

1. What are the main layers or domains?
2. Who owns which responsibility?
3. Where does dependency direction break down?
4. Which modules are too coupled?
5. Which abstractions are useful vs accidental?
6. Which boundaries should be protected going forward?
7. What is the smallest structural move that improves the system?

---

## Architecture Scope Levels

### Level 1: Boundary Check

Use for:

- one feature area
- one module
- one route/service/component chain

Deliverables:

- ownership map
- boundary issues
- targeted fixes

### Level 2: Subsystem Architecture Review

Use for:

- auth subsystem
- checkout/billing
- dashboard/state management
- API/service/repository layering

Deliverables:

- module map
- dependency direction
- structural risks
- improvement path

### Level 3: Codebase Architecture Audit

Use for:

- large legacy codebase
- scaling product
- pre-refactor guardrails
- repeated structural regressions

Deliverables:

- current architecture summary
- hotspots
- guardrails
- recommended target shape

---

## Review Protocol

Follow this order unless the user asks for a narrower question.

### Phase 1: Define Scope

Identify:

- target subsystem or codebase slice
- whether the goal is understand, enforce, or redesign
- current pain points
- scale/risk level

### Phase 2: Map Current Structure

Find:

- entry points
- modules/domains
- service boundaries
- data access boundaries
- shared utilities
- state ownership
- configuration boundaries

### Phase 3: Identify Ownership

Ask:

- which module truly owns this logic?
- which module only consumes it?
- where are responsibilities duplicated or mixed?

### Phase 4: Check Dependency Direction

Look for:

- UI depending on data access directly
- controllers/routes containing business logic
- domains depending on infrastructure details
- services depending on presentation concerns
- circular references
- shared modules importing domain-specific code

### Phase 5: Find Structural Risks

Look for:

- god modules
- unstable shared utilities
- boundary leakage
- coupling through hidden state
- duplicated cross-cutting logic
- weak abstraction around external systems
- architecture drift from original intent

### Phase 6: Recommend Guardrails

Recommend:

- ownership rules
- dependency direction rules
- file/module placement rules
- when to extract vs colocate
- what should stay stable
- what should be moved later

### Phase 7: Suggest Minimal Structural Moves

Prefer:

- small boundary corrections
- ownership clarification
- isolated extraction
- dependency cleanup

Avoid:

- broad rewrites
- architecture migration without clear need
- creating more layers than the product needs

---

## Practical Architecture Heuristics

### Frontend

Good signs:

- components focus on rendering and interaction
- domain logic has a clear home
- data loading/state ownership is explicit
- feature folders match real product concerns

Warning signs:

- components own API + state + business logic + formatting all at once
- shared UI folder contains business-specific logic
- hooks become hidden service layers without clear ownership

### Backend

Good signs:

- request handling is thin
- business logic has clear ownership
- data access is isolated
- validation is consistent

Warning signs:

- controllers/routes contain business rules
- DB calls appear everywhere
- service layer is only pass-through noise
- shared utils silently own domain behavior

### Cross-Cutting

Good signs:

- auth, validation, logging, caching, and config have clear boundaries
- domain logic is not scattered across layers

Warning signs:

- every layer knows about every other layer
- config is coupled to business logic
- security-sensitive checks live only in UI

---

## Anti-Patterns To Avoid

Do not:

- force a textbook architecture onto a small codebase
- assume folder names equal correct architecture
- create layers that add indirection without ownership clarity
- centralize logic that belongs to a domain just to make folders look clean
- treat architecture review as permission for a rewrite
- recommend moves without identifying actual pain

---

## Output Format

```markdown
## 🏛️ Architecture Report: [Target]

### Scope
- Target: [module/subsystem/codebase]
- Depth: Boundary Check | Subsystem Architecture Review | Codebase Architecture Audit
- Goal: [understand/enforce/improve]

### Current Shape
- Main layers/domains: [summary]
- Ownership model: [summary]
- Dependency direction: [summary]

### Findings
| Area | Issue | Impact |
|------|-------|--------|
| `src/...` | Business logic in controller | weak boundary, harder testing |
| `src/...` | Shared util owns domain rule | blurred ownership |

### Structural Risks
- [risk]
- [risk]

### Recommended Guardrails
- [rule]
- [rule]

### Suggested Moves
1. [small structural improvement]
2. [small structural improvement]
3. [later follow-up]

### Next Step
- Use `/refactor` for [targeted change]
- Use `/advanced-enhance` after boundaries are clarified
- Use `/review` to audit residual risk
```

---

## Short Output Format

```markdown
## 🏛️ Architecture: [Target]

- Current shape: [one line]
- Main issue: [one line]
- Risk: [one line]
- Best next move: [one line]
```

---

## Definition Of Done

`/architecture` is complete only when:

- current ownership is mapped
- dependency direction is assessed
- major boundary issues are identified
- recommendations are tied to real risks
- next structural step is clear

---

## Final Rule

Good architecture makes the next change easier to place, easier to review, and harder to break.
