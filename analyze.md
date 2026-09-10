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
> 3. **DYNAMIC & INDIRECT RESOLUTION:** Explicitly trace indirect invocations via Dependency Injection (IoC containers), dynamic reflection, event emitters/subscribers, message queues, and GraphQL dynamic schema resolvers.
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
│                 │     │ (IoC & Queues)  │     │ 4. State/Stores │
└─────────────────┘     └─────────────────┘     └─────────────────┘
```

### Investigation Depth Levels:
| Level | Scope | Deliverables |
| :--- | :--- | :--- |
| **Level 1: Surface Scan** | Quick orientation, entry points | Relevant files list, top-level architecture overview |
| **Level 2: Flow Trace** | Feature understanding, request paths | Step-by-step call graph, data mutation, side effects |
| **Level 3: Deep System Audit** | Subsystems, legacy code, coupling | Full domain map, dependency direction, invariants, hotspots |

---

## 🔬 Protocol 1: Architecture & Boundary Audit (`--arch` or General)

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
2. **Dependency Direction:** Verify adherence to Clean/Hexagonal rules (dependencies point inward; domain logic does not depend on DB or HTTP transport).
3. **Coupling Hotspots:** Identify circular dependencies (`A -> B -> A`), God-objects, and tight coupling across domain boundaries.
4. **State & Invariants:** Document single-source-of-truth, concurrency locks, and transaction scopes.

---

## 🔬 Protocol 2: Backend Request & Persistence Tracing (`--be`)

Traces the execution path of an API endpoint, background worker, or queue consumer:

```
[Client / Caller]
       │
       ▼
1. Entry & Routing      ──► Route definition, HTTP method, URL parameters, DTO schema
       │
       ▼
2. Guards & Middleware  ──► Auth/JWT check, RBAC permissions, CORS, Rate limits, Body validation
       │
       ▼
3. Business Domain      ──► Service layer, Domain rules, Orchestration, Transaction blocks
       │
       ▼
4. Data Persistence     ──► ORM/Query builder, SQL queries, Redis cache lookups, Index audit
       │
       ▼
5. Side Effects         ──► Message queues (Kafka/RabbitMQ/BullMQ), Webhooks, 3rd party APIs, Emails
```

### Deep Backend Audit Checklist:
- **IoC & Indirect Resolvers:** Uncover injected dependencies (NestJS `@Injectable()`, Spring `@Autowired`, Go Wire) and dynamic reflection.
- **Database & Query Audit:**
  - Detect **N+1 queries** in loops (e.g. querying child records inside an un-batched `map` or `for` loop).
  - Verify **Transaction Boundaries**: Ensure mutating operations are wrapped in `@Transactional` or `db.transaction()` blocks.
  - Check **Connection Pool & Indexes**: Flag unindexed table scans on large tables and unclosed DB connections.
- **Event & Queue Pipeline:** Trace emitted events (`eventEmitter.emit()`), message broker producers, and downstream queue consumer handlers.

---

## 🔬 Protocol 3: Frontend Data Flow & Interaction Tracing (`--fe`)

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

### Deep Frontend Audit Checklist:
- **AST Component Extraction:** Deconstruct component trees, props drilling, and custom hook resolution (utilizing `traceui` MCP tools when available).
- **Re-Render Hotspots:** Flag unstable object references passed down props, unmemoized context providers, and missing `useMemo`/`useCallback` on heavy child components.
- **State Integrity & Optimistic UI:** Verify rollback mechanisms on API failure (ensuring UI reverts correctly if server returns 4xx/5xx).

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
| 5 | Side Effect | `src/events/orderProducer.ts:45` | `publishOrderCreated()` | Dispatches Kafka event |

---

### 3. Critical Findings & Boundary Risks
- ⚠️ **Coupling / Bottleneck:** [Identified issue with file citation]
- 🔒 **Security / State Invariant:** [Observations regarding auth/concurrency/sanitization]
- ⚡ **Database & Query Risk:** [Identified N+1 query, missing index, or un-isolated transaction]
- 🔄 **Re-render / Async Risk:** [Unmemoized context or unhandled rejection risk]

---

### 4. Verified vs Unknowns
- **Verified Facts:** [List of proven behaviors from direct code inspection]
- **Remaining Unknowns:** [Dynamic env configs, external 3rd-party API responses]

➡️ **Recommended Next Workflow:** `/plan` (for feature/fix implementation) or `/refactor` (for cleaning).
```
