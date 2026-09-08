---
description: 🔍 Deep Codebase Understanding & Tracing Engine. Analyze architecture boundaries, trace Backend endpoints, or map Frontend data flows end-to-end.
argument-hint: "[scope or endpoint] [--arch] [--be] [--fe] [--deep] [--out]"
---

# /analyze - Deep System Analysis & Flow Tracing Engine

$ARGUMENTS

---

## 🎯 Purpose & Core Principles

Activates **DEEP ANALYZE & TRACE MODE** for rigorous code comprehension before planning, refactoring, debugging, or reviewing.

> **IRON INVARIANTS:**
> 1. **STRICTLY READ-ONLY:** Understand first, change nothing. Do NOT write patches, refactors, or new features during analysis.
> 2. **EMPIRICAL EVIDENCE:** Cite exact file paths, line ranges (`file.ts:12-45`), and verifiable call chains. Zero speculation.
> 3. **DYNAMIC & INDIRECT RESOLUTION:** Explicitly uncover indirect invocations via Dependency Injection (IoC containers), dynamic reflection, event emitters/subscribers, and GraphQL dynamic schema resolvers.
> 4. **EXPLICIT BOUNDARIES & UNKNOWNS:** Delineate verified code paths from dynamic runtime assumptions, external dependencies, and remaining unknowns.

---

## ⚙️ Argument Parsing & Execution Modes

```text
# General Codebase & System Analysis:
/analyze <scope, feature, or subsystem> [--deep] [--out]

# Architecture & Boundary Audit:
/analyze --arch <module or workspace> [--out]

# Backend Execution Trace:
/analyze --be <endpoint or file> (e.g. POST /api/orders or OrderController.ts) [--out]

# Frontend UI & State Flow Trace:
/analyze --fe <component or page> (e.g. OrderPage.tsx or src/views/) [--out]
```

### Auto-Detection Heuristic:
- If target is an HTTP route (`GET /...`) or backend file (`*controller*`, `*service*`, `*repository*`, `*route*`) -> Auto-engages **Backend Trace Mode**.
- If target is a UI file (`*.tsx`, `*.vue`, `*.svelte`, `*Page*`, `*Component*`) -> Auto-engages **Frontend Trace Mode**.
- If `--arch` flag is present -> Engages **Architecture Boundary Mode**.
- Flag `--out`: Saves output to `reports/ANALYZE-<YYYYMMDD>-<target-slug>.md`.

---

## 📖 Reading Strategy & Evidence Gathering

```
┌─────────────────┐     ┌─────────────────┐     ┌─────────────────┐
│ 1. Entry Points │ ──► │ 2. Direct Calls │ ──► │ 3. Types/Schema │
└─────────────────┘     └─────────────────┘     └────────┬────────┘
                                                         │
┌─────────────────┐     ┌─────────────────┐              ▼
│ 6. Tests/Mocks  │ ◄── │ 5. DB/Services  │ ◄── ┌─────────────────┐
└─────────────────┘     └─────────────────┘     │ 4. State/Stores │
                                                └─────────────────┘
```

### Investigation Depth Levels:
| Level | Scope | Deliverables |
| :--- | :--- | :--- |
| **Level 1: Surface Scan** | Quick orientation, entry points | Relevant files list, top-level architecture overview |
| **Level 2: Flow Trace** | Feature understanding, request paths | Step-by-step call graph, data mutation, side effects |
| **Level 3: Deep System Audit** | Subsystems, legacy code, coupling | Full domain map, dependency direction, invariants, hotspots |

### ⚠️ Anti-Patterns to Avoid:
- Never infer behavior from file or folder names alone.
- Never stop at the first plausible file without tracing callers and callees.
- Never trust outdated comments over actual code implementation.
- Never present unverified speculation as established fact.

---

## 🔬 Protocol 1: Architecture & System Analysis (`--arch` or General)

```
┌────────────────────────┐     ┌────────────────────────────────┐
│ 1. Scope & Entry Points│ ──► │ 2. Dependency Graph & Coupling │
└────────────────────────┘     └───────────────┬────────────────┘
                                               │
┌────────────────────────┐     ┌───────────────▼────────────────┐
│ 4. Findings & Risk Map │ ◄── │ 3. Layer Contracts & Invariants│
└────────────────────────┘     └────────────────────────────────┘
```

### Investigation Dimensions:
1. **Entry Points & Routing:** How requests or users enter the module.
2. **Dependency Direction:** Verify adherence to Clean Architecture / Hexagonal rules (dependencies point inward; domain logic does not depend on DB or transport).
3. **Coupling Hotspots:** Identify circular dependencies, God-objects, and tight coupling between disparate domains.
4. **State & Invariants:** Document single-source-of-truth, concurrency locks, and transaction boundaries.

---

## 🔬 Protocol 2: Backend Request Tracing (`--be` or Route/Service)

Traces the execution path of an API endpoint, background worker, or queue consumer:

```
[Client / Caller]
       │
       ▼
1. Entry & Routing      ──► Route definition, HTTP method, URL parameters
       │
       ▼
2. Guards & Middleware  ──► Auth/JWT check, RBAC, CORS, Rate limits, Body validation (Zod/DTO)
       │
       ▼
3. Business Domain      ──► Service layer, Domain rules, Orchestration, Transaction blocks
       │
       ▼
4. Data Persistence     ──► ORM/Query builder, SQL queries, Redis cache lookups, Indexes
       │
       ▼
5. Side Effects         ──► Message queues (Kafka/RabbitMQ), Webhooks, 3rd party APIs, Emails
```

### Deliverables:
- **Call Graph Table:** `Step` -> `File:Line` -> `Function` -> `Input/Output` -> `Failure Handlers`.
- **Database Query Audit:** Identified N+1 query risks, missing indexes, unindexed scans.
- **Side Effect Manifest:** External network calls, transactional outbox events.

---

## 🔬 Protocol 3: Frontend Data Flow Tracing (`--fe` or Component/Page)

Traces component rendering, interaction, state mutations, and network syncing:

```
[DOM / User Interaction] ──► Click, input change, lifecycle mount
       │
       ▼
1. Event Handlers        ──► Local callback functions, input sanitization
       │
       ▼
2. State Mutation        ──► useState / useReducer / Zustand / Redux dispatch
       │
       ▼
3. Custom Hooks / Sync   ──► useEffect, React Query / TanStack Query, SWR fetcher
       │
       ▼
4. API Client Gateway    ──► Axios / Fetch wrapper, request interceptors, Auth headers
       │
       ▼
5. Re-render & UI Tree   ──► Cache invalidation, optimistic updates, DOM re-render
```

### Deliverables:
- **Interaction Flow Map:** User Action -> State Changed -> Network Call -> Re-rendered Subtree.
- **Re-render Hotspots:** Unnecessary prop drilling, missing `useCallback`/`useMemo` in heavy subtrees.
- **State Integrity:** Optimistic UI rollback mechanisms on API failure.

---

## 📋 Standard Output Template

```markdown
## 🔍 Analysis Report: [Target / Scope]

### 1. Architectural Summary & Scope
- **Domain / Component:** [Name & Role]
- **Entry Points:** `[path/to/entry:line]`
- **Architectural Layer:** [Presentation / Application / Domain / Infrastructure]

---

### 2. End-to-End Execution Trace

```
[Layer 1: Entry] -> [Layer 2: Validation] -> [Layer 3: Core Logic] -> [Layer 4: Storage/Effects]
```

| Step | Layer | File:Line | Function / Symbol | Operational Details |
| :--- | :--- | :--- | :--- | :--- |
| 1 | Route/Entry | `src/routes/order.ts:25` | `POST /orders` | Route registration & schema validation |
| 2 | Guard | `src/middleware/auth.ts:40` | `requireAuth()` | JWT verification & injects `req.user` |
| 3 | Domain | `src/services/order.ts:88` | `createOrder()` | Validates stock, calculates tax |
| 4 | DB / Cache | `src/repos/order.ts:110` | `db.insert()` | Atomic transaction with balance lock |

---

### 3. Critical Findings & Boundary Risks
- ⚠️ **Coupling / Bottleneck:** [Identified issue with file citation]
- 🔒 **Security / State Invariant:** [Observations regarding auth/concurrency/sanitization]
- ⚡ **Performance / Query Risk:** [Identified N+1, slow path, or unnecessary re-render]

---

### 4. Verified vs Unknowns
- **Verified Facts:** [List of proven behaviors from code inspection]
- **Remaining Unknowns:** [Dynamic env configs, external 3rd-party API responses]

➡️ **Recommended Next Workflow:** `/plan` (for feature/fix implementation) or `/refactor` (for cleaning).
```
