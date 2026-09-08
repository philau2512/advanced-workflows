---
description: 📋 Multi-phase directory technical implementation planning & reality validation engine. Generates plan folder, master plan.md, individual phase-NN-*.md files, and TDD matrices.
argument-hint: "[feature or task] [--single] [--validate] [--tdd] [--fast]"
---

# /plan - Multi-Phase Implementation Planning & Reality Validation Engine

$ARGUMENTS

---

## 🎯 Purpose & Core Principles

Activates **PLANNING & REALITY-CHECK MODE** to produce grounded, actionable, phase-by-phase implementation plans structured into dedicated plan directories, or audit existing plans against codebase reality before writing code.

> **IRON INVARIANTS:**
> 1. **ZERO HALLUCINATED PATHS:** Every cited file path, model, class, and function must be verified against actual workspace files.
> 2. **MULTI-PHASE DIRECTORY FIRST:** By default (for tasks spanning 2+ phases), create a dedicated plan folder `plans/<YYYYMMDD>-<slug>/` with a master `plan.md` index and individual `phase-NN-*.md` files.
> 3. **STATUS & PROGRESS TRACKING:** Master `plan.md` and all phase files must track active status (`pending` | `in-progress` | `completed`) for atomic execution handoff.
> 4. **ATOMIC & SEQUENCED PHASES:** Each phase must leave the repository in a working, testable state with explicit file ownership (`[NEW]`, `[MODIFY]`, `[DELETE]`).
> 5. **TDD-FIRST VERIFICATION:** Every phase must declare its automated test commands and acceptance criteria before implementation.
> 6. **PLAN AUDIT GATE:** Automatically validate symbol existence and caller impacts before declaring a plan ready for `/cook`.

---

## ⚙️ Argument Parsing & Usage

```text
# Create a new multi-phase implementation plan folder:
/plan <feature description or technical task> [--tdd]

# Generate a single standalone plan file (for small/trivial tasks only):
/plan <simple task> --single

# Audit & validate an existing plan file or plan directory against code reality:
/plan --validate <path/to/plan.md or plans/<slug>/>
```

### Flags:
- `--single`: Generates a single standalone plan file instead of a multi-file directory (only for small, localized tasks).
- `--tdd`: Enforces strict Test-Driven Development (writing failing/characterization tests before changes in each phase).
- `--fast`: Fast planning mode for low-risk changes with minimal scouting.
- `--validate`: Audits an existing plan against the workspace (symbol existence, call-site impacts, schema compatibility).

---

## 📁 Plan Directory Architecture

Default structure generated in the project root:

```text
plans/<YYYYMMDD>-<feature-slug>/
├── plan.md                     # Master index: Overview, Frontmatter, Dependency Graph, Phases Table
├── phase-01-<name>.md          # Phase 1: Foundation, Types, DTOs & Schemas
├── phase-02-<name>.md          # Phase 2: Core Domain Services & Business Logic
├── phase-03-<name>.md          # Phase 3: Integration, Endpoints & UI Presentation
└── phase-04-<name>.md          # Phase 4: Verification, Polish, E2E & Rollback
```

---

## 🔬 4-Stage Planning Protocol

```text
┌────────────────────────┐     ┌────────────────────────────────┐
│ 1. Context & Baseline  │ ──► │ 2. Multi-File Directory Setup  │
│    (Inspect real code) │     │    (Master plan.md & phases)   │
└────────────────────────┘     └───────────────┬────────────────┘
                                               │
┌────────────────────────┐     ┌───────────────▼────────────────┐
│ 4. Reality Validation  │ ◄── │ 3. TDD Verification Matrix     │
│    (Gate before /cook) │     │    (Test criteria & rollback)  │
└────────────────────────┘     └────────────────────────────────┘
```

### Stage 1: Context & Codebase Reality Grounding
- Inspect codebase with search tools (`fast_context_search`, `grep_search`).
- Confirm exact file paths, exported symbol names, and directory conventions.
- Audit call sites and blast radius for any modified existing functions or schemas.

### Stage 2: Multi-File Directory Setup & Sequencing
- Create dedicated plan folder: `plans/<YYYYMMDD>-<slug>/`.
- Decompose work into sequential, independent phases (typically 2 to 4 phases):
  - **Phase 1: Foundation & Contracts:** Types, interfaces, DB migrations, DTOs.
  - **Phase 2: Core Domain Logic:** Services, business calculations, state machines.
  - **Phase 3: Integration & Presentation:** API endpoints, UI components, wiring.
  - **Phase 4: Polish & Edge Cases:** Error handling, logging, caching, cleanup.
- Author master `plan.md` as the index and navigation center.
- Author each `phase-NN-<name>.md` file with atomic implementation steps and explicit file ownership.

### Stage 3: TDD Verification Matrix
For each phase file, declare:
- Target test file (`tests/...` or `*.spec.ts`).
- Exact execution command (e.g. `npx vitest run tests/...`).
- Clear pass/fail acceptance criteria to prevent regressions.

### Stage 4: Reality Validation Gate (Self-Audit)
Run the **4-Role Validation Gate** before presenting the plan:
1. **Fact Checker:** Verify all files and imported symbols exist in the repository.
2. **Contract Verifier:** Verify parameter and signature changes do not break existing callers.
3. **Layer Auditor:** Verify architectural layer boundaries (no presentation logic in domain).
4. **Safety Auditor:** Verify rollback strategy, state handling, and auth guard coverage.

---

## 📋 Canonical Plan Templates

### 1. Master Plan Template (`plans/<YYYYMMDD>-<slug>/plan.md`)

```markdown
---
title: "{Brief Plan Title}"
description: "{One-sentence summary of the feature or refactor}"
status: pending  # pending | in-progress | completed | cancelled
priority: P2     # P1 (High) | P2 (Medium) | P3 (Low)
effort: 4h       # Estimated total effort
tags: [backend, api, database]
created: YYYY-MM-DD
---

# 📋 Implementation Plan: [Feature / Task Name]

## 1. Executive Summary & Objective
- **Goal:** [1-2 sentences summarizing what will be achieved]
- **Target Outcome:** [Concrete observable behavior or capability delivered]
- **Verified Codebase Baseline:** [Key existing files, models, or entry points confirmed]

---

## 2. User Review Required

> [!IMPORTANT]
> Highlight any breaking changes, public API modifications, database schema migrations, or architectural decisions requiring sign-off.

---

## 3. Dependency Graph & Phases Summary

\`\`\`mermaid
graph TD
    P1["Phase 1: Foundation & Schemas"] --> P2["Phase 2: Core Domain Logic"]
    P2 --> P3["Phase 3: API & UI Integration"]
    P3 --> P4["Phase 4: E2E & Polish"]
\`\`\`

### Phases

| Phase | File | Status | Effort | Description |
| :---: | :--- | :---: | :---: | :--- |
| **1** | [Foundation & Contracts](./phase-01-foundation.md) | `pending` | 1h | Types, DTOs, and Schema definitions |
| **2** | [Core Domain Logic](./phase-02-domain-logic.md) | `pending` | 1.5h | Business services, state transitions, validation |
| **3** | [API & Presentation](./phase-03-integration.md) | `pending` | 1h | Endpoints, route handlers, UI wiring |
| **4** | [Verification & Polish](./phase-04-verification.md) | `pending` | 0.5h | E2E test suite, edge cases, error boundary |

---

## 4. Reality Validation Gate Checklist
- [ ] All file paths and imports verified against actual repository tree.
- [ ] All affected call-sites accounted for (zero dangling usages).
- [ ] Rollback strategy defined if any phase encounters a blocker.

---

## 5. Handoff to Execution
Plan is validated and ready. Run `/cook` to begin phased, atomic implementation starting from Phase 1.
```

---

### 2. Phase File Template (`plans/<YYYYMMDD>-<slug>/phase-NN-<name>.md`)

```markdown
---
phase: 1
title: "[Phase Title, e.g. Foundation & Contracts]"
status: pending  # pending | in-progress | completed | cancelled
priority: P1     # P1 | P2 | P3
dependencies: [] # List preceding phase files if any, e.g. [./phase-01-foundation.md]
---

# Phase [N]: [Phase Title]

## 1. Phase Overview
[1-2 sentences describing what this phase delivers and why it is sequenced here]

- **Status:** \`pending\`
- **Target Deliverable:** [Clear observable output]

---

## 2. Related Code Files & Actions

List affected files with explicit tags and exact repository paths:
- **[NEW]** [`src/types/order.ts`](file:///path/to/src/types/order.ts)
  - Export `OrderDTO`, `CreateOrderInput`, `OrderStatus`
- **[MODIFY]** [`src/db/schema.prisma`](file:///path/to/src/db/schema.prisma)
  - Add optional `receiptUrl` column to `Order` model
- **[DELETE]** [`src/types/legacyOrder.ts`](file:///path/to/src/types/legacyOrder.ts)

---

## 3. Step-by-Step Implementation

1. **Step 1: Define Contracts**
   - Create schema/type definitions.
2. **Step 2: Add Validation Guards**
   - Implement runtime schema validation.
3. **Step 3: Barrel Export**
   - Re-export symbols from index module.

---

## 4. Task Checklist
- [ ] Create types in `src/types/order.ts`
- [ ] Apply database schema updates
- [ ] Export all symbols cleanly without circular dependencies

---

## 5. TDD Verification & Quality Gate

- **Target Test File:** `tests/unit/order-types.spec.ts`
- **Verification Command:** \`npm run test:types\` or \`npx vitest run tests/unit/order-types.spec.ts\`
- **Acceptance Criteria:**
  - [ ] Zero TypeScript compiler / linter errors
  - [ ] 100% test assertions pass
  - [ ] No regression on existing suite

---

## 6. Risk Assessment & Rollback
- **Risk:** [Potential failure mode or conflict]
- **Mitigation / Rollback:** [How to revert safely if this phase fails]
```

---

## 🛑 Plan Boundaries & Anti-Patterns

- **DO NOT** write monolithic single-file plans when work spans 2 or more distinct phases.
- **DO NOT** omit YAML frontmatter and `status` fields from `plan.md` or `phase-*.md`.
- **DO NOT** use filenames as link text in the master phases table (use descriptive human-readable names like `[Foundation & Contracts](./phase-01-foundation.md)`).
- **DO NOT** dump full code implementations into plan files (keep snippets strictly to interfaces, signatures, and contracts).
- **DO NOT** start execution before the master plan is reviewed and approved.
