---
description: ⚡ Evidence-Driven Performance & Resource Optimization Engine. Optimize database queries, reduce API latency, fix memory leaks, and tune rendering performance.
argument-hint: "[bottleneck or target] [--profile] [--out]"
---

# /optimize - Evidence-Driven Performance Optimization Engine

$ARGUMENTS

---

## 🎯 Purpose & Core Principles

Activates **OPTIMIZATION MODE** to systematically improve latency, throughput, resource efficiency, and scalability based on measured profiling data.

> **IRON INVARIANTS:**
> 1. **MEASURE FIRST, CHANGE SECOND, RE-MEASURE LAST:** Base all optimizations on empirical profiling or reproducible metrics, not speculative guesswork.
> 2. **CORRECTNESS OVER SPEED:** Performance gains must never compromise data integrity, transaction boundaries, or API contracts.
> 3. **TARGETED BLAST RADIUS:** Isolate interventions directly to the identified bottleneck. Avoid broad, premature micro-optimizations.
> 4. **CACHE SAFETY & PARITY:** Caching must implement explicit invalidation, TTLs, and serialization parity (e.g. preserving Dates, Maps, BigInts without distortion).

---

## ⚙️ Argument Parsing & Usage

```text
/optimize <bottleneck description, endpoint, or component> [--profile] [--out]
```

### Examples:
- `/optimize slow checkout API endpoint`
- `/optimize database query in order repository`
- `/optimize memory leak in background worker`
- `/optimize frontend bundle size and dashboard re-renders`

---

## 🔬 4-Phase Optimization Protocol

```
┌────────────────────────┐     ┌────────────────────────┐
│ 1. Baseline Benchmark  │ ──► │ 2. Root Bottleneck     │
│    (Measure & Quantify)│     │    Identification      │
└────────────────────────┘     └───────────┬────────────┘
                                           │
┌────────────────────────┐     ┌───────────▼────────────┐
│ 4. Verification &      │ ◄── │ 3. Targeted            │
│    Regression Guard    │     │    Intervention        │
└────────────────────────┘     └────────────────────────┘
```

### Phase 1: Baseline Measurement & Bottleneck Profiling
- Record baseline performance metric:
  - **Latency:** P95/P99 response time in ms.
  - **Database:** Query count, scan type (Sequential Scan vs Index Scan), execution duration.
  - **Frontend:** Component render count, bundle chunk size in KB, TTI / LCP.
  - **Resources:** Peak RSS memory in MB, CPU saturation percentage.

### Phase 2: Bottleneck Categorization

| Category | Typical Hotspots | Evidence-Driven Solutions |
| :--- | :--- | :--- |
| **Database & Queries** | N+1 queries, unindexed filters, large `SELECT *` | Eager loading (`join`/`include`), composite indexes, selective column projection. |
| **Backend & API** | Serial independent async calls, heavy JSON serialization | `Promise.all()` parallelization, streaming responses, lean DTO transforms. |
| **Caching Layer** | Repeated identical DB queries, expensive calculations | Redis / Memory cache with LRU, cache-stampede protection, stale-while-revalidate. |
| **Memory & Leaks** | Unbounded arrays, dangling event listeners, closure retention | WeakMap/WeakSet, stream processing for large datasets, explicit cleanup in lifecycles. |
| **Frontend & UI** | Heavy re-renders, oversized bundles, unmemoized context | Virtualized lists, dynamic `import()` code splitting, `useMemo`/`useCallback` at boundaries. |

### Phase 3: Targeted Intervention & Safety Guards
- Apply the minimal structural change addressing the bottleneck.
- **Safety Checklist:**
  - [ ] Idempotency maintained for mutated endpoints.
  - [ ] No race conditions introduced by parallel execution.
  - [ ] Cache invalidation occurs on every write/mutation path.
  - [ ] External API retries have exponential backoff and jitter to prevent thundering herds.

### Phase 4: Post-Optimization Verification
- Re-run benchmark under identical conditions.
- Verify that:
  1. The target metric measurably improved (e.g. latency decreased by >= 40%).
  2. All automated functional tests continue to pass with zero regressions.

---

## 📋 Standard Optimization Report Template

```markdown
## ⚡ Performance Optimization Report: [Target / Bottleneck]

### 1. Executive Summary & Measured Impact

| Metric Dimension | Before Optimization | After Optimization | Net Improvement |
| :--- | :--- | :--- | :--- |
| **P95 Latency** | 420 ms | 65 ms | **84% reduction** |
| **DB Queries per Request** | 24 (N+1 issue) | 2 queries | **91% query reduction** |
| **Memory Allocation** | 180 MB | 45 MB | **75% lower footprint** |

---

### 2. Root Cause of Inefficiency
- **Identified Bottleneck:** `[file.ts:line]`
- **Mechanics:** [Explanation of why the previous implementation was inefficient]

---

### 3. Applied Intervention
- **Modified File:** `[path/to/file.ts:line]`
- **Technique:** [e.g. Added composite index on `(user_id, status)` and eager-loaded relations]

---

### 4. Safety & Regression Verification
- Functional test suite: **PASS** (Zero behavioral changes)
- Typecheck: **CLEAN**
```
