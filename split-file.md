---
description: 📁 A safe autonomous file-splitting workflow that refactors code into clean frontend and backend architectures with zero behavior change, strict preservation, staged extraction, and internal verification.
---

## FILE SPLITTING FLOW (AUTONOMOUS SAFE MODE - PRODUCTION GRADE)

### CORE PRINCIPLES
- DO NOT interrupt user unless ambiguity is critical
- AI MUST plan → execute → verify internally
- ZERO behavior change is mandatory
- Safety is enforced via multi-level verification, not user confirmation
- Prefer correctness over speed
- NEVER lose logic, side effects, or execution order

---

## 0. PRE-SCAN (LIGHTWEIGHT)

- Read entire file once
- Detect:
  - Language (JS, TS, Python, Java, etc.)
  - Framework (React, Next, Vue, Spring, FastAPI, etc.)
  - Module system (ESM, CJS, etc.)
  - File size & complexity
- Identify FE or BE architecture

- Detect:
  - Entry points (main functions, exports, routes, components)
  - High-risk areas (async flows, global state, side effects)

Optional:
- Backup if file is large (>800 LOC) or critical

---

## 0.1 LARGE FILE STRATEGY (MANDATORY IF > 800 LOC)

- Split analysis into logical chunks:
  - By feature / domain / class group / region
- DO NOT split by line count blindly
- Process each chunk independently

Rules:
- Preserve logical boundaries
- Track cross-chunk dependencies
- Merge all findings before planning

---

## 1. DEEP ANALYSIS (MANDATORY)

Identify:
- All functions / classes / types / constants
- Feature groups (domain-based grouping)
- Execution flows (function chains)
- Entry points → final outputs
- Side effects:
  - State mutations
  - Global variables
  - Context usage
  - Logging / caching
- Async behavior (await chains, lifecycle)

Build dependency map:
- Internal (function → function)
- External (API, DB, libs)

Output (concise):
- Function list + role
- Feature groups
- Execution flows
- Key dependencies
- Architecture type (FE / BE)
- Risk notes

---

## 2. ARCHITECTURE DETECTION (CRITICAL)

### FRONTEND (React / Next / Vue)

Split by FEATURE first, then by LAYER

Layers:
- features/ → domain logic (PRIMARY)
- components/ → UI only (no business logic)
- hooks/ → reusable logic
- services/ → API calls
- store/ → state management
- utils/ → pure helpers

Rules:
- JSX/UI → components
- Business logic → features/hooks
- API → services
- State → store
- Helpers → utils

STRICT:
- NO API calls inside components
- NO JSX inside hooks
- NO mixed concerns

---

### BACKEND (Java / Python / Node)

Follow layered architecture STRICTLY:

Layers:
- controller/router → request handling ONLY
- service → business logic
- repository/DAO → DB access
- model/entity → schema
- DTO/schema → data transfer
- utils → helpers

STRICT:
- NO business logic in controller
- NO DB access outside repository
- Service MUST sit between controller and repository

---

## 3. AUTO PLANNING (NO CONFIRMATION)

Generate:
- File structure (clear naming, domain-based)
- Responsibility per file
- Function/class mapping

Define:
- Import/export structure
- Dependency direction (top-down only)

Constraints:
- Minimize cross-file dependency
- Avoid circular imports
- Preserve cohesion
- Respect architecture boundaries

---

## 4. SAFE EXTRACT (COPY FIRST)

- Copy code to new files (DO NOT DELETE original)

MUST preserve EXACTLY:
- Code
- Comments
- Function signatures
- Behavior

- Add ONLY minimal required imports

---

## 5. INTEGRATION

- Import new modules into original file
- Wire correctly

Rules:
- Keep original code intact (temporary duplication allowed)
- Ensure new code is callable

---

## 6. GRADUAL REPLACEMENT

- Replace usage step-by-step

Rules:
- Replace ONE logical unit at a time
- Validate after each replacement
- DO NOT delete old code yet

---

## 7. MULTI-LEVEL SELF-VERIFICATION (CRITICAL)

### 7.1 Syntax & Structure
- No missing functions
- No broken imports
- No undefined references
- No circular dependencies

---

### 7.2 Execution Integrity
- All execution paths preserved
- Function call order unchanged
- Entry → output mapping identical

---

### 7.3 Flow-Level Validation
- All feature flows preserved
- All function chains remain connected
- No disconnected logic

---

### 7.4 Side Effects Protection
- No lost state mutations
- No lost global/shared variables
- No lost logging / caching / context
- Closure behavior preserved

---

### 7.5 Async Integrity
- Await chains preserved
- Lifecycle/order unchanged
- No race condition introduced

---

### 7.6 Dependency Integrity
- No implicit dependencies broken
- All required imports exist
- No hidden coupling lost

---

### 7.7 Duplication Safety
- No conflicting duplicate logic
- Old and new code behave identically

---

### 7.8 Reachability & Circular Dependency Check (CRITICAL)
- All moved functions are still used; no dead code introduced.
- **Zero Circular Dependencies:** Ensure extracted files do not import each other (`A -> B -> A`). If mutual dependency exists, extract shared interfaces/types into a dedicated `types.ts` or `shared/` module.
- **Barrel Exports & Public Contracts:** Update or maintain existing barrel files (`index.ts`) so external callers and test imports do not break.
- **Server/Client Boundary & Secret Leak Safety:** In fullstack frameworks (Next.js, Remix), never import server-only logic, database clients, or environment secrets into client-rendered (`"use client"`) components.
- **CSS Cascade & DOM Hierarchy:** Ensure extracted sub-components retain CSS specificity, flex/grid parent-child relationships, and Tailwind class priority without visual regressions.

---

IF ANY doubt:
→ STOP and ask user

---

## 8. CLEANUP (STRICTLY GUARDED)

Cleanup ONLY if ALL conditions met:

- All replacements completed
- No references to old code remain
- Full verification passed AFTER replacement
- New structure fully functional

Steps:
- Remove duplicated old code
- Remove unused imports
- Keep structure clean and minimal

---

## 8.1 ROLLBACK SAFETY

Before cleanup:
- Snapshot working state
- Ensure ability to revert

---

## 9. FINAL OUTPUT (CONCISE)

- Files created
- Functions/classes moved
- Final structure
- Any risks/assumptions

Final confirmation MUST include:
"No behavior change detected"

---

## DECISION LOGIC (MANDATORY)

- JSX/UI → component
- API call → service
- Reusable logic → hook
- State → store
- DB query → repository
- Business rules → service
- Pure logic → utils

---

## ANTI-PATTERNS (STRICTLY FORBIDDEN)

### FRONTEND:
- UI + API + state + logic in one file
- Component calling API directly
- Hook returning JSX
- Mixed responsibilities

### BACKEND:
- Controller contains business logic
- Service accesses DB directly
- Single file handles everything
- Cross-layer coupling

---

## PRIORITY ORDER

### FRONTEND:
1. features/
2. components/
3. hooks/
4. services/
5. store/
6. utils/
7. ...

### BACKEND:
1. controller/router
2. service
3. repository
4. model/entity
5. DTO/schema
6. utils/
7. ...

---

## FINAL RULE

- Prefer many small, clear files
- DO NOT over-split if cohesion is strong
- Each file MUST have one clear responsibility
- Readability > cleverness
- Stability > optimization
- NEVER sacrifice correctness for structure
