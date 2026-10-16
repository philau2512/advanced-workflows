---
description: ♻️ Behavior-Preserving Refactoring & Safe File Splitting Engine with Plan-First Review, Persistent .bak Snapshot & Parity Reconciliation.
argument-hint: "[target-path] [--split] [--changed] [--strict] [--tdd]"
---

# /refactor - Behavior-Preserving Refactoring & Safe File-Splitting Engine

$ARGUMENTS

---

## 🎯 Purpose & Core Principles

Activates **REFACTOR & EXECUTION MODE** to safely improve code readability, cohesion, architecture boundaries, and maintainability without altering external behavior.

> **IRON INVARIANTS:**
> 1. **STRICTLY BEHAVIOR-PRESERVING:** Improve code structure, preserve external behavior contracts. Never add features, modify public APIs, alter error payloads, or mutate database persistence semantics.
> 2. **PLAN-FIRST & USER REVIEW GATE (MANDATORY):** NEVER begin modifying code immediately upon invocation. Always inspect targets, assess baseline state, formulate a comprehensive Refactoring Plan, and present it to the user. **STOP and wait for explicit user approval before touching any code.**
> 3. **APPROVED SNAPSHOT BACKUP (`.bak`):** ONLY after the user explicitly approves ("ok", "proceed", "tiến hành", etc.), create an exact file snapshot (`cp <target> <target>.bak`) before making any modifications.
> 4. **POST-REFACTOR PARITY RECONCILIATION:** Once refactoring completes, NEVER automatically delete the `.bak` file. Systematically cross-audit the refactored code against `<target>.bak` to verify that zero logic, branch conditions, error handlers, or important comments were dropped. If any discrepancy or missing code is detected, immediately patch and report it.
> 5. **PERSISTENT BACKUP GUARANTEE:** Retain `<target>.bak` in the working directory. Under NO circumstances should the agent delete `.bak` files on its own. Only delete `.bak` files when the user explicitly commands it (e.g. "xóa file bak", "delete bak files").
> 6. **NO TYPE REGRESSION & BOUNDARY INTEGRITY:** Never replace strong types with `any`/`unknown`. Preserve ORM schemas, `@Transactional` boundaries, auth guards, and secret containment.
> 7. **COPY-FIRST & GRADUAL REPLACEMENT:** When splitting files, NEVER delete original code prematurely. Copy to new files -> import into original -> replace unit-by-unit -> verify -> re-export.
> 8. **BARREL RE-EXPORT GUARANTEE:** The original file path must remain active and re-export all extracted public symbols to guarantee zero breaking changes for external consumers.

---

## ⚙️ Argument Parsing & Usage Modes

```text
# Standard Refactor (Extract functions, reduce nesting, consolidate logic, improve naming):
/refactor <path/to/file or module> [--tdd] [--changed]

# Safe File-Splitting (Splitting oversized >500 LOC monolithic files):
/refactor --split <path/to/large-file> [--out-dir <dir>]
```

### Flags:
- `--split`: Engages the Staged File-Splitting protocol to decompose monolithic files into layered architectures.
- `--changed`: Restricts refactoring scope strictly to git-modified files and direct callers.
- `--strict`: Enforces strict clean-code cleanup, including minor naming debt and micro-optimizations.
- `--tdd`: Enforces characterization tests before modifying logic and runs targeted tests after each step.

---

## 🛡️ The 5-Stage Defensive Refactoring Pipeline

Every refactoring operation must strictly follow these 5 stages:

```
┌─────────────────────────────────────────────────────────────────┐
│ Stage 1: Discovery & Refactoring Plan (User Review Gate)        │
│ - Inspect target code, code smells, LOC, cognitive complexity   │
│ - Check baseline build / typecheck / tests                      │
│ - Formulate detailed Refactoring Plan                           │
│ - 🛑 HALT EXECUTION & WAIT FOR USER APPROVAL                     │
└────────────────────────────────┬────────────────────────────────┘
                                 │ User confirms: "OK / Proceed / Làm đi"
┌────────────────────────────────▼────────────────────────────────┐
│ Stage 2: Approved Snapshot Creation                             │
│ - Create atomic backup: cp <target> <target>.bak                │
│ - Lock in baseline test / typecheck status                      │
└────────────────────────────────┬────────────────────────────────┘
                                 │
┌────────────────────────────────▼────────────────────────────────┐
│ Stage 3: Phased Refactoring Execution                           │
│ - Mode A: Targeted Logic Refactoring OR                         │
│ - Mode B: Staged File-Splitting Protocol (--split)              │
│ - Maintain barrel re-export guarantee                           │
└────────────────────────────────┬────────────────────────────────┘
                                 │
┌────────────────────────────────▼────────────────────────────────┐
│ Stage 4: Deep Reconciliation vs .bak & Gap Remediation          │
│ - Line-by-line / semantic diff against <target>.bak             │
│ - Audit 5 Semantic Traps & missing code/logic                   │
│ - 🛠️ IMMEDIATELY FIX any detected gaps or dropped logic         │
│ - Run targeted typecheck & tests                                │
└────────────────────────────────┬────────────────────────────────┘
                                 │
┌────────────────────────────────▼────────────────────────────────┐
│ Stage 5: Final Report & .bak Preservation                       │
│ - Present Refactoring & Reconciliation Report                   │
│ - 🛡️ RETAIN <target>.bak FILES (DO NOT DELETE)                  │
│ - Only delete .bak when user explicitly requests: "xóa file bak"│
└─────────────────────────────────────────────────────────────────┘
```

---

## 📝 Stage 1: Discovery & Refactoring Plan (User Review Gate)

### Protocol:
1. **Analyze Target Code:**
   - Read target file(s) and inspect structure, exported API surface, internal dependencies, cyclomatic complexity, and smells.
   - Run baseline typecheck (`tsc --noEmit` or language linter) and targeted tests to confirm current green/clean status.
2. **Draft Refactoring Plan:**
   - Present a structured plan before making ANY changes:
     - **Current State & Problems:** Smells, deeply nested conditionals, oversized functions, mixed responsibilities.
     - **Proposed Architecture / Structure:** New files/functions to create, naming, parameter DTOs, pure helpers.
     - **Behavior Preservation Strategy:** How public APIs, return types, error cases, and side-effects remain identical.
     - **Risk Assessment & Mitigation:** Potential traps (closures, async sequences, `this` context).
3. **🛑 Mandatory Halt for User Approval:**
   - Output the plan using the **Plan Review Template** below.
   - **DO NOT modify code or create `.bak` files at this stage.**
   - End response with an explicit call to the user:
     > *"Kế hoạch refactor chi tiết đã sẵn sàng. Vui lòng xem xét kế hoạch trên và phản hồi 'OK' hoặc góp ý điều chỉnh để tiến hành thực hiện."*

---

## 💾 Stage 2: Approved Snapshot Creation

Execute this stage **ONLY AFTER** receiving user confirmation (e.g., "OK", "đồng ý", "tiến hành", "làm đi"):

1. **Atomic Backup Creation:**
   - Create a duplicate snapshot for every file scheduled for modification:
     ```bash
     cp path/to/file.ts path/to/file.ts.bak
     ```
   - Verify that `<target>.bak` exists and contains identical byte content to the original.
2. **Baseline Re-Confirmation:**
   - Ensure the working tree is clean and baseline tests pass before touching code.

---

## 🔬 Stage 3: Phased Refactoring Execution

Execute refactoring using either **Mode A** or **Mode B**:

### Mode A: Targeted Logic Refactoring

#### Core Transformation Playbook:
1. **Reduce Nesting & Cognitive Complexity:**
   - Invert `if/else` checks into guard clauses with early `return`, `continue`, or `throw`.
   - Flatten nested ternary expressions and nested callbacks into linear pipelines or `async/await`.
2. **Parameter & DTO Encapsulation:**
   - Replace parameter clusters (>3 parameters) with strongly-typed parameter objects or DTOs.
   - Separate command inputs from context dependencies via dependency injection.
3. **Duplication Consolidation (DRY vs AHA):**
   - *Rule of Three:* Consolidate repeated logic only when identical code appears **>= 3 times** and shares the **same domain owner**.
   - *AHA Invariant:* Keep coincidental similarities intact if they belong to different business subdomains to avoid premature coupling.
4. **Immutability & Pure Separation:**
   - Eliminate direct mutations of input parameters or global caches.
   - Separate pure domain calculations from impure I/O operations.

#### Anti-Patterns to Avoid:
- Mechanical over-abstraction (creating 1-to-1 interfaces or repositories for everything).
- Fragmenting readable code into dozens of disconnected 2-line micro-helpers.
- Deduplicating logic that looks identical but belongs to distinct business domains (premature abstraction).
- Module top-level runtime side-effects (e.g., reading `process.env` or initializing network clients at module import time).
- Weakening existing test assertions to force refactored code to pass.

---

### Mode B: Staged File-Splitting Protocol (`--split`)

#### Architecture Layer Matrix:

##### Frontend Architecture (React / Next.js / Vue):
- `features/` -> Domain-specific modules & orchestration (**Primary**)
- `components/` -> UI presentation only (**STRICT: No direct API calls, no DB access**)
- `hooks/` -> Reusable stateful & lifecycle logic (**STRICT: No JSX return**)
- `services/` -> API clients & remote data fetching
- `store/` -> Global / client state management
- `utils/` -> Pure helper functions & stateless formatters

##### Backend Architecture (Node / Express / NestJS / FastAPI / Spring):
- `controller/router` -> Request parsing, validation, HTTP response (**STRICT: No business logic**)
- `service/` -> Core business logic & orchestration (**Sits between controller and repository**)
- `repository/dao` -> Database queries & persistence (**STRICT: No business rules**)
- `model/entity` -> Database schemas & domain entities
- `dto/schema` -> Request/response contracts & transport types
- `utils/` -> Pure calculation helpers & formatters

#### Staged Splitting Workflow:
1. **Pre-Scan & Chunking:** Group functions by domain feature / lifecycle before extracting.
2. **Target File Planning:** Define target files following the Architecture Layer Matrix above. Enforce unidirectional top-down imports; strictly forbid circular dependencies.
3. **Copy-First Extraction:** Copy types, helpers, and services into new files. **DO NOT delete original code yet.** Extract in sequence: Types (`.types.ts`) -> Pure Utils (`.utils.ts`) -> Services/Hooks -> Subcomponents.
4. **Wiring & Gradual Replacement:** Import new modules back into the original file. Replace old internal references ONE logical unit at a time.
5. **Barrel Re-Export Guarantee:** Keep the original file path active. Re-export all extracted public symbols from it so existing consumers never break.

---

## 🔍 Stage 4: Deep Reconciliation vs `.bak` & Gap Remediation

Once code edits are made, perform an exhaustive cross-comparison between the refactored code and `<target>.bak`:

### 1. Parity Audit vs `.bak`:
- **Line-by-Line / Block Diff:** Compare `<target>.bak` against refactored files.
- **Lost Code Detection:** Check whether any branches, error validations, logging statements, analytics triggers, or important business comments were accidentally omitted.
- **Behavioral Semantic Traps:** Systematically verify against the 5 Semantic Traps:

| # | Semantic Trap | Risk Scenario | Safe Refactoring Rule |
| :--- | :--- | :--- | :--- |
| **1** | **Falsy vs Nullish** | Converting `if (!x)` to `if (x == null)` or `??` to `\|\|` accidentally swallows valid `0`, `""`, or `false`. | Distinguish explicitly between `undefined`/`null` checks and general falsiness checks. |
| **2** | **Short-Circuiting & Side-Effects** | Extracting conditional sub-expressions into local variables prematurely executes expressions that previously short-circuited. | Keep expressions containing I/O, DB calls, or function invocations inside their conditional branches. |
| **3** | **Default Params & Destructuring** | Consolidating parameter lists into an options object drops parameter defaults or breaks callers passing `undefined`. | Ensure parameter object types specify defaults and preserve optionality contracts identically. |
| **4** | **Closures, Scope & `this` Context** | Extracting methods into standalone functions or changing function declarations to arrows strips `this` binding or breaks closed-over variables. | Preserve closure state scope; bind or pass context explicitly when decoupling instance methods. |
| **5** | **Error Handling & Exception Propagation** | Simplifying `try/catch` blocks converts typed exceptions into generic errors or alters error payload shapes. | Ensure re-thrown errors preserve the original stack trace, error code, and HTTP status mappings. |

### 2. Immediate Remediation:
- If ANY dropped logic, missing validation, or semantic deviation is detected: **FIX IT IMMEDIATELY** before running validation.
- Document every gap discovered and the exact remediation applied in the final report.

### 3. The 10-Point Self-Verification Checklist:
- [ ] **1. Syntax & Circular Dependencies:** Zero undefined symbols; no circular imports (`A -> B -> A`). Shared types in dedicated `types.ts`.
- [ ] **2. Execution Order & Flow:** Function call sequence, middleware pipeline, and argument mappings remain 100% identical.
- [ ] **3. State & Closure Integrity:** No lost state mutations, closures retain access to required variables, logging/caching calls preserved.
- [ ] **4. Async & Concurrency:** `await` chains intact; no race conditions or unhandled rejections introduced.
- [ ] **5. Server/Client Boundary Safety:** In fullstack frameworks (Next.js/Remix), NEVER import server-only logic, DB clients, or environment secrets into `"use client"` components.
- [ ] **6. CSS Cascade & DOM Hierarchy:** Subcomponents preserve CSS specificity, flex/grid relationships, and style priorities.
- [ ] **7. Barrel Re-Export Integrity:** All external consumers import seamlessly from original path with zero regressions.
- [ ] **8. Compiler & Test Gate:** `tsc --noEmit` (or language linter) is clean (0 errors) and characterization tests pass 100%.
- [ ] **9. Transaction & Context Propagation:** DB transactions (`tx`), Tenant ID, and request contexts propagate without escaping ACID boundaries.
- [ ] **10. Test Mocking Surface & TDZ:** Existing test mocks (`jest.spyOn`, `vi.mock`) remain functional; no Temporal Dead Zone runtime import errors.

---

## 🛡️ Stage 5: Final Report & `.bak` Retention Protocol

### STRICT .bak RETENTION POLICY:
> [!IMPORTANT]
> **NEVER AUTOMATICALLY DELETE `.bak` FILES UPON COMPLETION.**
> - Keep all `<target>.bak` files intact next to modified files so the user can inspect the exact diff or run manual comparisons.
> - **Deletion Rule:** Only delete `.bak` files when the user explicitly gives a follow-up command (e.g., *"xóa file bak"*, *"đã kiểm tra xong, hãy xóa file .bak"*, *"cleanup bak files"*).
> - **Rollback Rule:** If the user rejects the changes or requests a rollback, restore from `.bak`:
>   ```bash
>   cp path/to/file.ts.bak path/to/file.ts
>   ```

---

## 📋 Standard Output Templates

### Template A: Refactoring Plan (Output at Stage 1)

```markdown
## 📋 Refactoring Plan: [Target Module / File]

### 1. Current State & Code Assessment
- **Target File:** `[path/to/file]` (~[LOC] lines)
- **Baseline Status:** [Typecheck: Clean | Tests: PASS]
- **Identified Smells / Debt:**
  - High cognitive complexity in `[functionA]` (deeply nested conditionals)
  - Mixed concerns (database queries inside presentation layer)
  - Parameter clump in `[functionB]` (>4 arguments)

### 2. Proposed Architecture & Structural Plan
| Step | Target Action | New File / Module | Rationale |
| :--- | :--- | :--- | :--- |
| 1 | Extract types & DTOs | `[path/to/target.types.ts]` | Decouple interfaces from runtime |
| 2 | Extract pure calculations | `[path/to/target.utils.ts]` | Isolate domain logic for easy testing |
| 3 | Encapsulate service logic | `[path/to/target.service.ts]` | Centralize business orchestration |
| 4 | Wire original file & barrel | `[path/to/target.ts]` | Maintain full backward compatibility |

### 3. Behavior Preservation & Risk Mitigation
- **Public API Contract:** Re-export all symbols from `[path/to/target.ts]`; zero external import breaks.
- **Edge Cases & Side-Effects:** Preserving exact short-circuit evaluations and `try/catch` error payload structures.
- **Rollback Safety:** Snapshot `[target].bak` will be created immediately upon your approval.

---
> 🛑 **Kế hoạch refactor chi tiết đã sẵn sàng. Vui lòng xem xét kế hoạch trên và phản hồi 'OK' hoặc góp ý điều chỉnh để tiến hành thực hiện.**
```

---

### Template B: Execution & Reconciliation Report (Output at Stage 5)

```markdown
## ♻️ Refactoring & Reconciliation Report: [Target Module / File]

### 1. Scope & Execution Mode
- **Target:** `[path/to/file]`
- **Mode:** [Targeted Refactor / Staged File Split]
- **Backup Status:** `[path/to/file].bak` created and **PRESERVED** for user inspection.
- **Verification Baseline:** [Pre-refactor test/typecheck: PASS]

---

### 2. Summary of Structural Changes
| File / Component | Layer / Change Type | Improvements Applied |
| :--- | :--- | :--- |
| `src/order.types.ts` | Types Layer | Extracted shared DTOs & Interfaces |
| `src/order.utils.ts` | Utils (Pure) | Extracted `calculateDiscount()` & formatting helpers |
| `src/order.service.ts` | Domain Service | Encapsulated DB persistence & payment orchestration |
| `src/order.ts` | Barrel Entrypoint | Re-exports all public symbols; delegates to service |

---

### 3. Deep Reconciliation vs `.bak` (Rà Soát & Đối Chiếu Logic)
- **Diff & Code Audit Status:**
  - [x] All original functions, methods, and branches accounted for in refactored code.
  - [x] Error handling, custom exceptions, and error payload structures matched 1-to-1.
  - [x] Side-effects, logging, caching, and async await chains intact.
  - [x] 5 Semantic Traps checked (Falsy/Nullish, Short-circuits, Defaults, Closures, Exceptions).
- **Discrepancies Discovered & Remediated:**
  - *Gaps found during audit:* [None / e.g., "Phát hiện hàm calculateTax thiếu trường hợp null -> đã bổ sung và đối chiếu khớp 100% với file .bak"]
  - *Remediation applied:* [Chi tiết fix nếu có]

---

### 4. Verification Gate Status
- [x] Zero circular dependencies detected.
- [x] Server/Client boundary intact (No secrets leaked to UI).
- [x] Public contracts & barrel re-exports verified with caller test suite.
- [x] Database transaction (`tx`) and request context propagated without leakage.
- [x] Existing test mock surfaces and module loading order verified.
- **Type Check:** `tsc --noEmit` -> **CLEAN (0 errors)**
- **Test Suite:** `npm test` -> **PASS (All green)**

---

### 5. 🛡️ Backup File Status & Next Actions
> [!NOTE]
> File backup `[path/to/file].bak` **VẪN ĐANG ĐƯỢC LƯU GIỮ NGUYÊN VẸN** tại thư mục để bạn tự đối chiếu và kiểm tra độc lập.
> - Nếu bạn hài lòng và muốn dọn dẹp file backup, vui lòng phản hồi: **"xóa file bak"** (Agent sẽ tiến hành xóa).
> - Nếu muốn khôi phục lại trạng thái cũ: **"rollback"** (Agent sẽ khôi phục từ file .bak).
```
