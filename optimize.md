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
> 6. **ZERO-DEVIATION SNAPSHOT & INSTANT ROLLBACK:** Always create a snapshot (`cp <target> <target>.bak`) before modifying code. If optimization causes data divergence, race conditions, or test regressions, instantly rollback from `.bak` (`mv <target>.bak <target>`). Clean up `.bak` upon success.

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
│    Sanitization        │     │    Intervention (.bak) │
└────────────────────────┘     └────────────────────────┘
```

### Phase 1: Baseline Measurement & Bottleneck Profiling
- **Pre-Flight Snapshot:** Create an atomic backup of target files before editing:
  ```bash
  cp path/to/file.ts path/to/file.ts.bak
  ```
- Record exact baseline performance metrics before touching code:
  - **Latency:** P50, P95, and P99 response times in ms.
  - **Database:** Total query count per request, scan type (Sequential Scan vs Index Scan), execution duration via `EXPLAIN ANALYZE`.
  - **Frontend:** Component render count, unneeded prop re-renders, bundle chunk size in KB, TTI / LCP / INP.
  - **System Resources:** Peak RSS memory in MB, CPU saturation percentage, GC pause frequency.

---

### Phase 2: Bottleneck Categorization

| Category | Typical Hotspots | Evidence-Driven Solutions |
| :--- | :--- | :--- |
| **1. Database & Queries** | N+1 queries, unindexed filters, `SELECT *`, long-running transactions holding locks | Eager loading (`join`/`include`), composite indexes, selective projections, connection pool tuning; **keep 3rd-party API calls and heavy CPU compute outside DB transaction blocks**. |
| **2. Backend & APIs** | Serial async calls, unconstrained parallel tasks, heavy JSON transforms | **Bounded concurrency** (`p-limit` or batch chunks) instead of raw `Promise.all()`; streaming response bodies; lean DTO mappings. |
| **3. Caching Layer** | Repeated identical DB queries, cache penetration, in-memory cache OOM | Redis with TTL, cache-stampede distributed locks; **LRU eviction with explicit `maxSize`** for in-memory caches; cache null/empty results with short TTL against penetration. |
| **4. Memory & Leaks** | Unbounded arrays, retained closures, dangling subscriptions | `WeakMap`/`WeakSet`, streaming streams for large exports, explicit unmount cleanup. |
| **5. Frontend & UI** | Heavy re-renders, oversized bundles, unmemoized context, premature memoization overhead | Virtualized lists, dynamic `import()` code splitting, selective memoization (avoid `useMemo` when dependency references change every render); track INP / LCP. |
| **6. Cloud & External APIs**| Excessive 3rd-party calls, burst API billing spikes | Batching API calls, Exponential Backoff + Jitter, local request deduplication. |

---

### Phase 3: Targeted Intervention & Safety Guards

Apply the minimal structural modification addressing the bottleneck:

#### 🛡️ Optimization Safety Checklist:
- [ ] **Data & Contract Parity:** API request/response format and database schemas remain 100% stable; numerical and aggregation results match baseline 1:1.
- [ ] **Serialization Parity:** Cached objects rehydrate with original prototypes (`Date`, `BigInt`) without string corruption.
- [ ] **Cache Invalidation & Eviction Bounds:** Cache keys invalidated on mutations; in-memory caches enforce LRU max-size/TTL bounds to prevent OOM; null values cached with short TTL against penetration attacks.
- [ ] **Stampede Protection:** Mutex locks or stale-while-revalidate applied to high-traffic cached endpoints.
- [ ] **Bounded Concurrency:** Concurrency capped (e.g. `p-limit(10)` or batch chunks) instead of unbounded `Promise.all()` to prevent connection pool exhaustion or rate limit bans.
- [ ] **Transaction Duration Minimization:** Zero network I/O, email dispatch, or external HTTP calls inside active DB transaction blocks (`BEGIN...COMMIT`).
- [ ] **Connection Pool Safety:** Connections released immediately after query completion; idle timeouts and pool sizing configured to prevent starvation under burst traffic.
- [ ] **Retry Storm Protection:** Outbound retries use exponential backoff with randomized jitter; max retries capped <= 3.
- [ ] **Frontend Memoization & Unmount Cleanup:** `useMemo` justified by measurable profiling; all event listeners, timers, and WebSocket subscriptions explicitly cleaned up.

---

### Phase 4: Post-Optimization Verification & Sanitization

1. **Re-run benchmark** under identical baseline conditions.
2. **Verify that:**
   - The target metric measurably improved (e.g. latency decreased by >= 40% or DB queries reduced from 25 to 2).
   - All automated unit and integration tests continue to pass with zero regressions.
   - Typecheck is completely clean (`tsc --noEmit`).
3. **Rollback / Sanitization Protocol:**
   - **IF VERIFICATION PASSES:** Remove temporary backup files (`rm path/to/file.ts.bak`).
   - **IF VERIFICATION FAILS (Data drift, race conditions, or test regressions):** Instantly rollback from `.bak` (`mv path/to/file.ts.bak path/to/file.ts`), abort changes, and report the exact regression.

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
- [x] Pre-flight `.bak` created prior to mutations; sanitized after test run.
- [x] Serialization parity preserved (No Date/BigInt corruption).
- [x] Cache invalidation added to update mutation handlers; LRU bounds enforced.
- [x] Concurrency bounded via `p-limit` (no unbounded Promise.all pool exhaustion).
- [x] Zero external network I/O inside active database transaction blocks.
- [x] Outbound API retries bounded with Exponential Backoff + Jitter.

---

### 4. Verification Gate
- **Functional Test Suite:** **PASS (Zero regressions)**
- **Type Check:** `tsc --noEmit` -> **CLEAN (0 errors)**
- **Sanitization:** All `.bak` files cleaned. Workspace spotless.
```
