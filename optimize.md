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
> 2. **CORRECTNESS OVER SPEED:** Performance gains must never compromise data integrity, transaction boundaries, or public API contracts.
> 3. **TARGETED BLAST RADIUS:** Isolate interventions directly to the identified bottleneck. Avoid premature micro-optimizations across unaffected areas.
> 4. **CACHE SAFETY & SERIALIZATION PARITY:** Caching must implement explicit invalidation on writes, stampede locks, and serialization parity (e.g. preserving `Date`, `Map`, `Set`, `BigInt` types upon deserialization).
> 5. **RETRY STORM & COST PROTECTION:** Any automated retry logic must strictly enforce Exponential Backoff, Jitter, and hard max caps to prevent third-party API quota exhaustion or cascading outages.

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
- Record exact baseline performance metrics before touching code:
  - **Latency:** P50, P95, and P99 response times in ms.
  - **Database:** Total query count per request, scan type (Sequential Scan vs Index Scan), execution duration via `EXPLAIN ANALYZE`.
  - **Frontend:** Component render count, unneeded prop re-renders, bundle chunk size in KB, TTI / LCP.
  - **System Resources:** Peak RSS memory in MB, CPU saturation percentage, GC pause frequency.

---

### Phase 2: Bottleneck Categorization

| Category | Typical Hotspots | Evidence-Driven Solutions |
| :--- | :--- | :--- |
| **1. Database & Queries** | N+1 queries, unindexed filters, `SELECT *` over large tables | Eager loading (`join`/`include`), composite indexes, selective column projection. |
| **2. Backend & APIs** | Serial independent async calls, heavy JSON payload transforms | `Promise.all()` concurrency, streaming response bodies, lean DTO mappings. |
| **3. Caching Layer** | Repeated identical DB queries, expensive compute cycles | Redis with TTL, cache-stampede distributed locks, probabilistic early expiration. |
| **4. Memory & Leaks** | Unbounded arrays, retained closures, dangling subscriptions | `WeakMap`/`WeakSet`, streaming streams for large exports, explicit unmount cleanup. |
| **5. Frontend & UI** | Heavy re-renders, oversized bundles, unmemoized context | Virtualized lists, dynamic `import()` code splitting, selective memoization. |
| **6. Cloud & External APIs**| Excessive 3rd-party calls, burst API billing spikes | Batching API calls, Exponential Backoff + Jitter, local request deduplication. |

---

### Phase 3: Targeted Intervention & Safety Guards

Apply the minimal structural modification addressing the bottleneck:

#### 🛡️ Optimization Safety Checklist:
- [ ] **Data & Contract Parity:** API request/response format and database schemas remain 100% stable.
- [ ] **Serialization Parity:** Cached objects rehydrate with original prototypes (`Date`, `BigInt`) without string corruption.
- [ ] **Cache Invalidation:** Cache keys are explicitly invalidated or updated on every mutation path (create, update, delete).
- [ ] **Stampede Protection:** Mutex locks or stale-while-revalidate applied to high-traffic cached endpoints.
- [ ] **Retry Storm Protection:** Outbound retries use exponential backoff with randomized jitter; max retries capped $\le 3$.
- [ ] **Unmount Cleanup:** All frontend event listeners, timers, and WebSocket subscriptions are explicitly cleaned up.

---

### Phase 4: Post-Optimization Verification

1. Re-run benchmark under identical baseline conditions.
2. Verify that:
   - The target metric measurably improved (e.g. latency decreased by $\ge 40\%$ or DB queries reduced from 25 to 2).
   - All automated unit and integration tests continue to pass with zero regressions.
   - Typecheck is completely clean (`tsc --noEmit`).

---

## 📋 Standard Optimization Report Template

```markdown
## ⚡ Performance Optimization Report: [Target / Bottleneck]

### 1. Executive Summary & Measured Impact

| Metric Dimension | Before Optimization | After Optimization | Net Improvement |
| :--- | :--- | :--- | :--- |
| **P95 Latency** | 420 ms | 65 ms | **84% reduction** |
| **DB Queries per Request** | 24 queries (N+1) | 2 queries | **91% query reduction** |
| **Peak Memory Allocation** | 180 MB | 45 MB | **75% lower footprint** |

---

### 2. Root Cause Analysis
- **Identified Bottleneck:** `[file.ts:line]`
- **Mechanics:** [Technical explanation of why the previous implementation was inefficient]

---

### 3. Applied Intervention & Safety Verification
- **Modified File:** `[path/to/file.ts:line]`
- **Technique Applied:** [e.g. Added composite index on (tenant_id, status) and converted to eager join]
- [x] Serialization parity preserved (No Date/BigInt corruption).
- [x] Cache invalidation added to update mutation handlers.
- [x] Outbound API retries bounded with Exponential Backoff + Jitter.

---

### 4. Verification Gate
- **Functional Test Suite:** **PASS (Zero regressions)**
- **Type Check:** `tsc --noEmit` -> **CLEAN (0 errors)**
```
