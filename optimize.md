---
description: ⚡ Performance and efficiency optimization workflow. Improve speed, throughput, resource usage, and scalability using evidence-driven changes.
---

# /optimize - Evidence-Driven Optimization Mode

$ARGUMENTS

## Purpose & Core Rule

Activates OPTIMIZE mode for improving latency, throughput, resource efficiency, and scalability based on measured evidence.

> **OPTIMIZE = MEASURE FIRST, CHANGE SECOND, RE-MEASURE LAST**

- Base optimizations on empirical profiling, logs, or metrics rather than guesswork.
- Scope changes strictly to the identified bottleneck.
- **Safety guarantee:** Performance gains must never silently compromise correctness, data integrity, or public contracts.

---

## When to Use

- **Use when:** High API latency, sluggish UI rendering, database N+1/slow queries, excessive memory/CPU consumption, slow builds, or scaling bottlenecks.
- **Do not use:** General structural cleanups (use `/refactor`), fixing functional bugs (use `/debug`), or micro-tuning without evidence.

---

## Supported Intents

```text
/optimize dashboard render performance
/optimize slow checkout API
/optimize database queries for orders
/optimize bundle size
/optimize upload pipeline
/optimize memory usage in background worker
/optimize caching for product listing
/optimize before high traffic launch
```

---

## Optimization Safety Contract

### Must Preserve
- Business logic, data integrity, user-visible behavior, API contracts, security guarantees, idempotency, and error handling.
- **Serialization Parity:** Caching complex objects (Date, Map, Set, BigInt, RegExp) must preserve exact data types across serialization/deserialization cycles without string/null mutation.

### High-Risk Actions (Verify Thoroughly)
- Caching mutable state (requires cache invalidation & stampede locks).
- **Retry Storms & API Cost Loops:** Retries on failed downstream/3rd-party services must strictly enforce Exponential Backoff, Jitter, and hard max-retry caps to avoid billing spikes or cascading outages.
- Parallelizing sequential/ordered side effects or batching writes.
- Lazy-loading critical paths or skipping validation checks.

---

## Optimization Categories

| Category | Typical Hotspots | Target Interventions |
| :--- | :--- | :--- |
| **1. Frontend Rendering** | UI lag, unnecessary rerenders, large lists, SPA memory leaks | List virtualization, state granularity, memoizing heavy paths, cleanup unmounted event listeners/subscriptions |
| **2. Network & Payload** | Large responses, waterfall requests | Request batching, payload trimming, compression, selective prefetching |
| **3. Database & Queries** | Slow queries, N+1 loading, missing indexes, cache stampede | Query restructuring, adding/tuning indexes, batching reads/writes, cache locks/probabilistic early expiration |
| **4. CPU & Memory** | CPU spikes, memory leaks, high GC pressure | Stream vs buffer, hot loop optimization, clearing retained closures/event listeners |
| **5. Build & Tooling** | Slow startup, bloated bundle, long CI, serverless cold starts | Tree shaking, dynamic imports, build caching, pruning heavy dependencies, optimizing cold start init |
| **6. Cost & Capacity** | Excessive cloud compute/API calls | Efficient caching, concurrency tuning, eliminating redundant operations |

---

## Optimization Protocol

### Phase 1: Define the Problem & Target Metric
- Explicitly state what is slow/heavy, the affected user/system path, and the target metric (e.g., "p95 latency < 300ms", "reduce render time on 10k rows").

### Phase 2: Establish a Baseline
- Record current measurements (latency, query duration, bundle size, CPU/RAM) before touching code.

### Phase 3: Identify the Bottleneck
- Isolate the limiting factor (I/O, CPU, DB, Network, Lock contention) rather than treating superficial symptoms.

### Phase 4: Select Smallest High-Impact Strategy
- Priority: Remove wasted work → Reduce redundant I/O → Narrow recomputation → Optimize queries/indexes → Cache safely → Parallelize.

### Phase 5: Implement Targeted Changes
- Apply changes scoped strictly to the bottleneck. Avoid unrelated refactoring in the optimization pass.

### Phase 6: Re-Measure & Compare
- Verify measurable delta (Before vs After, improvement %, resource impact vs tradeoffs).

### Phase 7: Validate Correctness & Parity
- Verify business logic, data consistency, cache freshness, and existing test suites remain 100% intact.

---

## Measurement Checklist

| Domain | Key Metrics |
| :--- | :--- |
| **API / Backend** | p50/p95/p99 latency, throughput (RPS), connection pool saturation |
| **Frontend** | LCP, INP, CLS, component render duration, rerender count |
| **Database** | Query execution time, rows examined vs returned, index hit rate |
| **System** | Peak memory (RSS), CPU utilization %, garbage collection pauses |

---

## Anti-Patterns to Avoid

- Optimizing without baseline measurements or profiling evidence.
- Indiscriminately adding memoization/caching everywhere without invalidation logic.
- Trading correctness, safety, or readability for negligible micro-gains.
- Parallelizing operations that rely on strict execution order.
- Declaring victory based on synthetic benchmarks without verifying end-to-end user flows.

---

## Output Formats

### Standard Format

```markdown
## ⚡ Optimization Report: [Target]

### Objective & Baseline
- **Target:** [Feature/API/Component]
- **Problem & Metric:** [Latency / Memory / Render time / Query count]
- **Baseline:** [Measured before value] (Source: Logs / Profiler / Trace)
- **Goal:** [Target after value]

### Bottleneck Analysis & Plan
- **Bottleneck:** [Exact root cause limiting performance]
- **Strategy:** [Specific targeted intervention]

### Changes Made
| Area | Modification | Expected Impact |
| :--- | :--- | :--- |
| `src/...` | [Query tuning / Virtualization / Caching] | [Latency/Memory reduction] |

### Results & Verification
- **Before vs After:** [Before] → [After] ([Delta / % Improvement])
- **Tradeoffs:** [None / Memory / Added complexity]
- **Correctness Check:** [Tests passed, contracts preserved]
- **Next Follow-up:** `/test` | `/review`
```

### Short Format

```markdown
## ⚡ Optimization: [Target]
- **Problem & Metric:** [One line]
- **Bottleneck:** [Root cause]
- **Change:** [Key modification]
- **Result:** [Before] → [After] ([% improvement])
- **Tradeoff & Risk:** [None / Low]
```

---

## Handoff Matrix

| Next Need | Workflow |
| :--- | :--- |
| Verify correctness with regression tests | `/test` or `/test-audit` |
| Audit introduced complexity or caching logic | `/review` |
| Deep dive into an unclear downstream bottleneck | `/analyze` |
| Clean up architecture after optimization | `/refactor` |

---

## Definition of Done

1. Concrete performance bottleneck identified and evidenced with a baseline.
2. Targeted optimization applied directly to the bottleneck.
3. Before/after comparative measurement reported.
4. Correctness, edge cases, and test suites verified.
