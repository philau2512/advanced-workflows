---
description: 📋 Enterprise-grade multi-phase directory technical implementation planning & reality validation engine. Generates plan folder, master plan.md, comprehensive phase-NN-*.md files with full contract specs, and TDD matrices.
argument-hint: "[feature or task] [--deep] [--tdd] [--single] [--validate] [--fast]"
---

# /plan - Multi-Phase Implementation Planning & Reality Validation Engine

$ARGUMENTS

---

## 🎯 Purpose & Core Principles

Activates **DEEP ARCHITECTURAL PLANNING & REALITY-CHECK MODE** to produce grounded, production-grade, phase-by-phase implementation plans structured into dedicated plan directories, or audit existing plans against codebase reality before writing code.

> **IRON INVARIANTS:**
> 1. **ZERO HALLUCINATED PATHS:** Every cited file path, model, class, and function must be verified against actual workspace files.
> 2. **MULTI-PHASE DIRECTORY FIRST:** By default (for tasks spanning 2+ phases), create a dedicated plan folder `plans/<YYYYMMDD>-<slug>/` with a master `plan.md` index and individual `phase-NN-*.md` files.
> 3. **STATUS & PROGRESS TRACKING:** Master `plan.md` and all phase files must track active status (`pending` | `in-progress` | `completed` | `cancelled`) for atomic execution handoff.
> 4. **TECHNICAL DEPTH & ANTI-BREVITY:** Never produce shallow summaries (e.g. 30-50 line phases). Every phase file must be an exhaustive, self-contained implementation contract containing complete data structures, function signatures, step-by-step logic, edge cases, and rollback strategies.
> 5. **ATOMIC & SEQUENCED PHASES:** Each phase must leave the repository in a working, testable state with explicit file ownership (`[NEW]`, `[MODIFY]`, `[DELETE]`).
> 6. **TDD-FIRST VERIFICATION:** Every phase must declare its automated test commands, failure conditions, and observable acceptance criteria before implementation.
> 7. **PLAN AUDIT GATE:** Automatically validate symbol existence and caller impacts before declaring a plan ready for `/cook`.

---

## ⚙️ Argument Parsing & Usage

```text
# Standard multi-phase implementation plan folder:
/plan <feature description or technical task> [--tdd]

# Deep enterprise-grade planning (maximum depth, full signatures, sequence diagrams):
/plan <feature description or complex refactor> --deep [--tdd]

# Generate a single standalone plan file (strictly for trivial or localized changes):
/plan <simple task> --single

# Fast planning mode for low-risk tweaks:
/plan <minor tweak> --fast

# Audit & validate an existing plan file or plan directory against code reality:
/plan --validate <path/to/plan.md or plans/<slug>/>
```

### Flags:
- `--deep`: **(Recommended for non-trivial features)** Forces exhaustive architectural depth: complete function signatures, DTO/type schemas, Mermaid sequence/data flow diagrams, comprehensive edge-case matrices, and concrete rollback plans.
- `--tdd`: Enforces strict Test-Driven Development (writing characterization/failing tests before code edits in each phase).
- `--single`: Generates a single standalone plan file instead of a multi-file directory (only for small, localized tasks).
- `--fast`: Fast planning mode with minimal codebase scouting.
- `--validate`: Audits an existing plan against the workspace (symbol existence, call-site impacts, schema compatibility).

---

## 📁 Plan Directory Architecture

Default structure generated in the project root:

```text
plans/<YYYYMMDD>-<feature-slug>/
├── plan.md                     # Master index: Executive Summary, Architecture Diagram, Phases Table & Status
├── phase-01-<name>.md          # Phase 1: Foundation, Data Contracts, Schemas & Migrations
├── phase-02-<name>.md          # Phase 2: Core Domain Logic, Services & State Transitions
├── phase-03-<name>.md          # Phase 3: Integration, API Endpoints & UI Presentation
└── phase-04-<name>.md          # Phase 4: E2E Verification, Seeders, Polish & Rollback
```

---

## 🔬 4-Stage Planning Protocol

```text
┌────────────────────────┐     ┌────────────────────────────────┐
│ 1. Context & Baseline  │ ──► │ 2. Multi-File Directory Setup  │
│    (Deep codebase scan)│     │    (Master plan.md & phases)   │
└────────────────────────┘     └───────────────┬────────────────┘
                                               │
┌────────────────────────┐     ┌───────────────▼────────────────┐
│ 4. Reality Validation  │ ◄── │ 3. TDD Verification Matrix     │
│    (Gate before /cook) │     │    (Edge cases & rollback)     │
└────────────────────────┘     └────────────────────────────────┘
```

### Stage 1: Context & Codebase Reality Grounding
- Inspect codebase using search tools (`fast_context_search`, `grep_search`).
- Identify exact existing models, interfaces, serializers, service layers, and route handlers.
- Search all call-sites and consumers to determine the blast radius of any signature modification.
- Identify existing unit/integration test suites covering the affected areas.

### Stage 2: Multi-File Directory Setup & Phased Decomposition
- Create dedicated plan folder: `plans/<YYYYMMDD>-<slug>/`.
- Decompose work into sequential, independently testable phases (typically 2 to 4 phases):
  - **Phase 1: Foundation & Contracts:** Types, interfaces, DB migrations, DTOs.
  - **Phase 2: Core Domain Logic:** Services, business calculations, state machines.
  - **Phase 3: Integration & Presentation:** API endpoints, UI components, wiring.
  - **Phase 4: Polish & Edge Cases:** Error handling, logging, caching, cleanup.
- Author master `plan.md` as the navigation index and delivery contract.
- Author each `phase-NN-<name>.md` following the comprehensive 11-section standard.

### Stage 3: TDD Verification Matrix & Edge Cases
For each phase file, declare:
- Target test file (`tests/...` or `*.spec.ts`).
- Exact execution command (e.g. `uv run python manage.py test ...` or `npx vitest run ...`).
- Matrix of happy paths, boundary conditions, and regression failure guards.

### Stage 4: Reality Validation Gate (Self-Audit)
Run the **4-Role Validation Gate** before presenting the plan:
1. **Fact Checker:** Verify all files and imported symbols exist in the repository.
2. **Contract Verifier:** Verify parameter and signature changes do not break existing callers.
3. **Layer Auditor:** Verify architectural layer boundaries (no presentation logic in domain).
4. **Safety Auditor:** Verify rollback strategy, state handling, transactions, and auth guard coverage.

---

## 📋 Canonical Plan Templates

### 1. Master Plan Template (`plans/<YYYYMMDD>-<slug>/plan.md`)

```markdown
---
title: "{Brief Plan Title}"
description: "{One-sentence executive summary of the feature or refactor}"
status: pending  # pending | in-progress | completed | cancelled
priority: P1     # P1 (High) | P2 (Medium) | P3 (Low)
effort: 6h       # Estimated total effort
tags: [backend, frontend, database, api]
created: YYYY-MM-DD
---

# 📋 Implementation Plan: [Feature / Task Name]

## 1. Executive Summary & Delivery Contract
- **Goal:** [Brief statement of what will be achieved]
- **Target Outcome:** [Concrete, observable behavior or business capability delivered]
- **Verified Codebase Baseline:** [Key existing files, models, and entry points verified in repository]

---

## 2. User Review Required & Breaking Changes

> [!IMPORTANT]
> Highlight any breaking changes, public API modifications, database schema migrations, or architectural decisions requiring explicit sign-off.

---

## 3. High-Level Architecture & Data Flow

\`\`\`mermaid
graph TD
    Client["Client / UI"] --> API["API Controller / Endpoint"]
    API --> Service["Domain Service"]
    Service --> DB[("Database / Store")]
\`\`\`

---

## 4. Dependency Graph & Phases Summary

\`\`\`mermaid
graph TD
    P1["Phase 1: Foundation & Contracts"] --> P2["Phase 2: Core Domain Logic"]
    P2 --> P3["Phase 3: Integration & UI"]
    P3 --> P4["Phase 4: Verification & Polish"]
\`\`\`

### Phases Roadmap

| Phase | File | Status | Effort | Description |
| :---: | :--- | :---: | :---: | :--- |
| **1** | [Foundation & Contracts](./phase-01-foundation.md) | `pending` | 1.5h | Types, DTOs, Schemas and DB Migrations |
| **2** | [Core Domain Logic](./phase-02-domain-logic.md) | `pending` | 2h | Services, state transitions, scoring/business rules |
| **3** | [API & Presentation](./phase-03-integration.md) | `pending` | 1.5h | Endpoints, route handlers, UI components & tables |
| **4** | [Verification & Polish](./phase-04-verification.md) | `pending` | 1h | E2E test suite, seeders, edge cases & rollback |

---

## 5. Cross-Cutting Concerns
- **Security & RBAC:** [Permission checks, token verification, tenant isolation]
- **Performance & Query Optimization:** [Index usage, pagination, N+1 query prevention]
- **Observability:** [Structured logging, error boundary, audit logging]

---

## 6. Reality Validation Gate Checklist
- [ ] All file paths and imports verified against actual repository tree.
- [ ] All affected call-sites accounted for (zero dangling usages).
- [ ] Backward compatibility verified (safe defaults for existing database records).
- [ ] Rollback strategy defined if any phase encounters a blocker.

---

## 7. Handoff to Execution
Plan is validated and ready. Run `/cook` to begin phased, atomic implementation starting from Phase 1.
```

---

### 2. Comprehensive Phase Detail Template (`plans/<YYYYMMDD>-<slug>/phase-NN-<name>.md`)

> **Note:** Every phase file MUST follow this comprehensive 11-section format. Do NOT truncate or omit sections.

```markdown
---
phase: 1
title: "[Phase Title, e.g. Foundation & Database Schemas]"
status: pending  # pending | in-progress | completed | cancelled
priority: P1     # P1 | P2 | P3
effort: 1.5h
dependencies: [] # Preceding phase files, e.g. [./phase-01-foundation.md]
---

# Phase [N]: [Phase Title]

## 1. Phase Overview & Objective
[2-3 sentences describing what this phase delivers, its technical significance, and why it is sequenced at this stage]

- **Status:** `pending`
- **Target Deliverable:** [Clear observable output, e.g. applied migration, exported types]

---

## 2. Context & Codebase Grounding
- **Related Existing Code:**
  - `path/to/existing/model.py` (line XX-YY): [Current behavior]
  - `path/to/existing/service.py`: [Current callers]
- **Key Insights & Assumptions:**
  - [Insight 1 discovered during codebase inspection]
  - [Assumption validated against real files]

---

## 3. Requirements & Scope Boundaries
- **Functional Requirements:**
  - [ ] [Requirement 1]
  - [ ] [Requirement 2]
- **Non-Functional Requirements:**
  - Performance: [Latency / memory / query count target]
  - Compatibility: [Zero downtime, backward-compatible schema]
- **Non-Goals / Out of Scope:**
  - [What is explicitly NOT done in this phase to prevent scope creep]

---

## 4. Architecture & Data Flow

\`\`\`mermaid
sequenceDiagram
    participant C as Caller
    participant S as Service
    participant R as Repository/DB
    C->>S: execute(payload)
    S->>R: persist()
    R-->>S: record
    S-->>C: result
\`\`\`

---

## 5. Related Code Files & Concrete Contract Specifications

List affected files with explicit tags and exact repository paths:

### `[MODIFY]` `apps/core/models.py`
- **Description:** [Brief summary of modifications]
- **Concrete Code Signatures / Schema Diff:**
\`\`\`python
# Specify exact fields, types, and defaults
class ExampleModel(models.Model):
    new_field = models.PositiveIntegerField(default=0, help_text="...")
    excess_points = models.DecimalField(max_digits=14, decimal_places=2, default=Decimal('0'))
\`\`\`

### `[NEW]` `apps/core/types.ts`
- **Description:** [New contract interfaces]
- **Concrete Type Definitions:**
\`\`\`typescript
export interface ExampleDTO {
  id: string;
  quantity: number;
  excessQuantity: number;
}
\`\`\`

### `[DELETE]` `apps/core/legacy.py`
- **Description:** [Reason for deletion and confirmed zero usages]

---

## 6. Step-by-Step Implementation Guide

1. **Step 1: [Action Name]**
   - File: `path/to/file`
   - Detailed Instruction: [Explain exact logic, calculation, or condition to add]
   - Edge Case Handling: [How null, 0, or invalid input is handled]

2. **Step 2: [Action Name]**
   - File: `path/to/file`
   - Detailed Instruction: [Explain step]

3. **Step 3: [Action Name]**
   - Apply migrations or rebuild exports.

---

## 7. Task Checklist
- [ ] Task 1: Update schema / models
- [ ] Task 2: Implement core business logic in service
- [ ] Task 3: Expose fields in serializer / DTO
- [ ] Task 4: Export new public symbols

---

## 8. TDD Verification Matrix & Quality Gate

- **Target Test Files:**
  - `tests/unit/test_example_service.py`
  - `tests/integration/test_example_flow.py`
- **Verification Commands:**
  - Unit Tests: `pytest tests/unit/test_example_service.py -v`
  - Typecheck: `npm run type-check` or `mypy apps/`
  - Lint: `flake8` or `eslint`
- **Test Scenarios Matrix:**

| Scenario | Input | Expected Output | Failure Guard |
| :--- | :--- | :--- | :--- |
| **Standard Happy Path** | Valid normal data | Returns `status: 'success'` | Fails if null |
| **Boundary / Limit Exceeded** | Data exceeding quota | Splits standard & excess | Fails if blocked |
| **Idempotency** | Duplicate submit key | Returns existing record | Fails if duplicate created |

---

## 9. Risk Assessment & Failure Modes
- **Potential Risk 1:** [e.g. Migration lock on large table]
  - **Mitigation:** [e.g. Add column with default in separate non-blocking statement]
- **Potential Risk 2:** [e.g. Race condition during concurrent submission]
  - **Mitigation:** [e.g. Database row locking via `select_for_update()`]
- **Rollback Procedure:**
  - Revert step: `python manage.py migrate app <prev_migration>`
  - Git revert target: Revert commits associated with Phase [N]

---

## 10. Security, Concurrency & Data Integrity
- **Authentication & Authorization:** [Verify permission check]
- **Data Integrity:** [Atomic transaction boundaries via `transaction.atomic()`]
- **Input Sanitization:** [Validation schema enforcing boundary values]

---

## 11. Handoff & Next Steps
- **Prerequisites Satisfied for Next Phase:** [What Phase N+1 needs that this phase delivers]
- **Next Phase Trigger:** Proceed to `phase-02-[name].md` via `/cook`.
```

---

## 🛑 Plan Boundaries & Anti-Patterns

- **DO NOT** write monolithic single-file plans when work spans 2 or more distinct phases (use `--single` only for trivial edits).
- **DO NOT** write brief 30-line phase files. Each phase must contain concrete code signatures, data flows, and test matrices.
- **DO NOT** omit YAML frontmatter and `status` fields from `plan.md` or `phase-*.md`.
- **DO NOT** use raw filenames as link text in the master phases table (use descriptive names like `[Foundation & Contracts](./phase-01-foundation.md)`).
- **DO NOT** hallucinate file paths or libraries not present in the workspace.
- **DO NOT** start execution before the master plan is reviewed and approved.
