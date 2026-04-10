---
description: ⚡Performance and efficiency optimization workflow. Improve speed, throughput, resource usage, and scalability using evidence-driven changes.
---

# /optimize - Evidence-Driven Optimization Mode

$ARGUMENTS

---

## Purpose

This command activates OPTIMIZE mode for improving performance, efficiency, and scalability using measured evidence instead of guesswork.

Use it when the goal is to make the system faster, lighter, more stable under load, or less expensive to run.

---

## What Optimize Means

Optimization is:

- reducing latency
- improving throughput
- lowering CPU, memory, or network overhead
- reducing unnecessary rendering or recomputation
- improving database/query efficiency
- improving bundle or asset efficiency
- improving cache effectiveness
- improving concurrency or workload handling
- reducing infrastructure or runtime cost

Optimization is **not**:

- random micro-tuning without evidence
- rewriting code just because it "looks slow"
- trading readability for negligible gains
- hiding behavioral changes under a performance label
- introducing caching or parallelism without correctness checks

If the main issue is structure, use `/refactor`.
If the main issue is correctness, use `/debug`.
If the system is unfamiliar, use `/analyze` first.

---

## Core Rule

> **OPTIMIZE = MEASURE FIRST, CHANGE SECOND, RE-MEASURE LAST**

Default expectation:

- performance claims should be tied to evidence
- bottlenecks should be identified before edits
- optimizations should be scoped to the limiting factor
- improvements should be validated after changes

---

## Why This Exists

Without a dedicated optimization workflow, teams often:

- optimize what is visible instead of what is expensive
- spend time on tiny gains while real bottlenecks remain untouched
- make code harder to maintain for no meaningful payoff
- add caches, memoization, or concurrency that create correctness bugs
- celebrate "optimized" code without before/after proof

`/optimize` exists to make performance work disciplined, measurable, and safe.

---

## When To Use

Use `/optimize` when:

- pages render slowly
- API response time is too high
- build or startup is slow
- database queries are inefficient
- memory usage is too high
- CPU spikes under load
- network payloads are larger than necessary
- repeated work is wasting time or cost
- scaling problems appear under concurrency
- performance is blocking release or user experience

Do **not** use `/optimize` by default for every task.

Only optimize when there is:

- a measured problem
- a visible bottleneck
- a clear scalability concern
- or a strong reason to improve efficiency before growth

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

## Relationship To Other Workflows

| Need | Best Workflow |
|------|---------------|
| Understand system behavior first | `/analyze` |
| Improve structure safely | `/refactor` |
| Fix correctness issue | `/debug` |
| Add capability | `/enhance` |
| Improve performance or efficiency | `/optimize` |
| Validate behavior after changes | `/test` |
| Review risk and code quality | `/review` |

Optimization often works best after `/analyze`, and often pairs with `/test`.

---

## Optimization Goals

When `/optimize` is triggered, the assistant should improve one or more of these:

1. Response time
2. Render time
3. Throughput
4. Startup time
5. Build time
6. Database efficiency
7. Memory usage
8. CPU usage
9. Network efficiency
10. Cache hit rate
11. Scalability under concurrency
12. Runtime or infrastructure cost

Each optimization should explicitly state which metric or experience it targets.

---

## Optimization Safety Contract

Performance improvements must not silently break correctness.

### Preserve By Default

- business logic correctness
- data integrity
- user-visible behavior
- API contract
- security guarantees
- ordering guarantees users rely on
- idempotency assumptions
- validation semantics
- error handling semantics

### High-Risk Changes Requiring Extra Care

- caching mutable data
- changing concurrency behavior
- batching writes
- reordering side effects
- lazy loading or deferred execution
- reducing precision or fidelity
- skipping validation
- changing pagination/windowing semantics

If an optimization affects these, verify correctness explicitly.

---

## Optimization Categories

### 1. Frontend Rendering

Use when:

- UI feels sluggish
- unnecessary rerenders happen
- heavy components block interaction
- large lists or charts are slow

Typical moves:

- reduce repeated computation
- split heavy rendering paths
- defer non-critical work
- virtualize large lists
- improve state granularity
- reduce blocking effects

### 2. Network And Payload

Use when:

- responses are too large
- too many requests are sent
- waterfall loading hurts UX

Typical moves:

- batch requests where safe
- reduce payload fields
- compress or resize assets
- prefetch selectively
- remove redundant fetching

### 3. Database And Persistence

Use when:

- queries are slow
- N+1 behavior appears
- indexes are missing
- unnecessary writes happen

Typical moves:

- optimize query shape
- reduce round trips
- add or tune indexes
- batch or cache reads safely
- avoid repeated serialization/deserialization

### 4. CPU And Memory

Use when:

- processes spike CPU
- memory grows unexpectedly
- workers become unstable

Typical moves:

- reduce repeated work
- avoid large in-memory duplication
- stream instead of buffering
- free retained references
- isolate hot loops

### 5. Build, Startup, And Tooling

Use when:

- local dev startup is slow
- builds take too long
- CI time is high

Typical moves:

- reduce heavy imports
- split build steps
- remove unused dependencies
- improve cache usage
- narrow watched or compiled scope

### 6. Cost And Capacity

Use when:

- infrastructure cost is rising
- services waste compute
- scaling concerns are predictable

Typical moves:

- reduce expensive calls
- improve cache hit rate
- lower payload size
- reduce duplicate processing
- tune worker concurrency safely

---

## Optimization Protocol

Follow this sequence unless evidence requires a narrower path.

### Phase 1: Define The Performance Problem

State clearly:

- what is slow, heavy, or inefficient
- where users or systems feel the impact
- what metric matters
- what "good enough" looks like

Good examples:

- "Checkout API p95 should drop below 400ms"
- "Dashboard initial render should feel responsive on large datasets"
- "Order query should avoid N+1 and reduce database round trips"

Bad examples:

- "Make app faster"
- "Improve performance everywhere"
- "Optimize codebase"

### Phase 2: Establish A Baseline

Capture the current state before changing code.

Possible baselines:

- response time
- render time
- query duration
- bundle size
- build duration
- memory footprint
- CPU utilization
- request count
- cache hit/miss behavior

If exact measurement is unavailable, document the best available evidence and say it is partial.

### Phase 3: Identify The Bottleneck

Find the limiting factor, not just symptoms.

Look for:

- slow query or missing index
- expensive render path
- repeated serialization
- large payload
- synchronous blocking work
- unnecessary recomputation
- too many network hops
- excessive logging or tracing overhead
- cache miss patterns
- queue backlog or lock contention

Do not start optimizing until there is a plausible bottleneck hypothesis.

### Phase 4: Choose The Smallest High-Impact Change

Prefer the smallest change with meaningful gain.

Order of preference:

1. Remove wasted work
2. Reduce redundant I/O
3. Narrow recomputation
4. Improve query shape or indexing
5. Improve cache strategy
6. Defer or parallelize safe work
7. Larger architectural optimization

Avoid large rewrites unless smaller changes cannot address the bottleneck.

### Phase 5: Implement Targeted Optimization

Each change should clearly map to the identified bottleneck.

Examples:

- deduplicate repeated fetches
- memoize only proven hot paths
- virtualize large lists
- add missing indexes
- move non-critical work off the critical path
- reduce object churn in hot loops
- stream large results instead of buffering

Do not mix unrelated cleanup into the same optimization pass.

### Phase 6: Re-Measure And Compare

After changes, compare against baseline:

- before
- after
- what improved
- what did not improve
- whether tradeoffs were worth it

If performance did not improve meaningfully, reconsider the hypothesis.

### Phase 7: Validate Correctness And Stability

Confirm that optimization did not break:

- correctness
- ordering
- consistency
- cache freshness
- state synchronization
- user flows
- tests

Optimization is incomplete if it is faster but no longer reliable.

---

## Required Optimization Mindset

Always optimize for:

- measurable gains
- minimal blast radius
- correctness under load
- maintainable improvements
- stable user experience

Do not optimize for:

- cleverness
- benchmark theater
- micro-optimizations with no user or system value
- premature abstraction
- replacing readable code with opaque tricks for tiny gains

---

## Reading Strategy Before Optimization

Read enough to connect symptoms to causes.

### Read Order

1. User-facing entry point or request path
2. Main implementation on the hot path
3. Data access / network boundaries
4. State management or render boundaries
5. Related tests
6. Config or infrastructure settings affecting performance

### Things To Capture While Reading

- hot path ownership
- repeated work
- unnecessary allocations
- expensive dependencies
- blocking synchronous sections
- over-fetching or over-rendering
- cache boundaries
- concurrency assumptions

### For Large Files

If a file is large:

- trace only the hot path first
- expand around expensive operations
- avoid full-file rewrites
- optimize the bottleneck, not the entire file

---

## Measurement Checklist

Before claiming success, try to capture some of these:

| Area | Example Metrics |
|------|-----------------|
| API | p50/p95 latency, throughput, request count |
| UI | initial render, interaction delay, rerender count |
| DB | query duration, rows scanned, round trips |
| Build | total time, step time, cache hit rate |
| Memory | peak usage, retained objects, leak trend |
| CPU | hot functions, utilization spikes |
| Network | payload size, waterfall depth, request duplication |
| Cache | hit rate, miss cost, staleness risk |
| Cost | expensive operations per request/job |

---

## Heuristics By Problem Type

### Slow API

Focus on:

- query count
- payload size
- serial I/O
- unnecessary downstream calls
- repeated parsing or transformation

### Slow Frontend

Focus on:

- rerender frequency
- heavy component trees
- large lists/charts
- blocking effects
- sequential data loading

### Slow Database

Focus on:

- N+1 patterns
- missing indexes
- full scans
- unnecessary joins
- repeated reads for identical data

### High Memory Usage

Focus on:

- retained references
- buffering large results
- duplicated structures
- cache growth without eviction
- long-lived closures or subscriptions

### High Infrastructure Cost

Focus on:

- duplicate work per request
- oversized payloads
- low-value expensive jobs
- low cache efficiency
- over-provisioned concurrency

---

## Anti-Patterns To Avoid

Do not:

- optimize without baseline evidence
- optimize the loudest symptom before finding the bottleneck
- add memoization everywhere by default
- add caching without invalidation strategy
- parallelize work that depends on strict ordering
- trade correctness for speed without approval
- increase complexity for gains too small to matter
- treat benchmark improvement as enough without validating real flows

---

## Output Format

```markdown
## ⚡ Optimization Report: [Target]

### Objective
- Target: [feature/module/path]
- Problem: [what is slow or inefficient]
- Metric: [latency/render time/query time/bundle size/etc.]
- Goal: [desired outcome]

### Baseline
- Current measurement: [before]
- Evidence source: [profiling/logs/test/manual trace]
- Confidence: High | Medium | Low

### Bottleneck Analysis
1. [observed expensive area]
2. [why it is likely the bottleneck]
3. [what was ruled out]

### Optimization Plan
1. [targeted step]
2. [targeted step]
3. [targeted step]

### Changes Made
| Area | Change | Expected Effect |
|------|--------|-----------------|
| `src/...` | Reduced repeated fetches | lower latency |
| `src/...` | Added virtualization | lower render cost |
| `src/...` | Tuned query/index | faster DB read |

### Results
- Before: [value]
- After: [value]
- Improvement: [delta or percentage]
- Tradeoff: [memory/readability/complexity/none]

### Validation
- Behavior check: [what remained correct]
- Test status: [passed/not run/not available]
- Residual risk: Low | Medium | High

### Follow-Up
- Use `/test` to add regression coverage around [area]
- Use `/review` to assess complexity introduced by optimization
- Use `/analyze` if another hotspot remains unclear
```

---

## Short Output Format

For smaller optimization tasks:

```markdown
## ⚡ Optimization: [Target]

- Problem: [one line]
- Bottleneck: [one line]
- Change: [one line]
- Result: [before -> after]
- Risk: [one line]
```

---

## Escalation Rules

Stop and ask for approval if optimization would likely:

- change behavior users depend on
- weaken data consistency or correctness
- alter API contract
- rely on aggressive caching with unclear invalidation
- change concurrency model significantly
- drop validation or safety checks
- remove observability needed for debugging or compliance

If confidence is low, narrow the optimization scope and report what is still unknown.

---

## Handoff Matrix

After `/optimize`, choose next steps intentionally:

| Next Need | Workflow |
|-----------|----------|
| Validate unchanged behavior | `/test` |
| Audit complexity or risk | `/review` |
| Understand another hotspot | `/analyze` |
| Clean up resulting structure | `/refactor` |
| Fix a correctness bug revealed by optimization | `/debug` |

---

## Example Prompts

```text
/optimize dashboard page render with large datasets
/optimize product search API latency
/optimize order query performance
/optimize bundle size before deploy
/optimize image loading on landing page
/optimize worker memory usage during imports
/optimize cache strategy for repeated reads
```

---

## Definition Of Done

`/optimize` is complete only when:

- the performance problem is clearly defined
- a baseline exists or best-available evidence is documented
- the bottleneck hypothesis is explicit
- changes map directly to the bottleneck
- before/after comparison is reported
- correctness and stability were checked
- residual risks or follow-up work are documented

If those are missing, optimization is not complete.

---

## Final Rule

Optimize what matters, not what is easy to tweak.

A small measured gain on the real bottleneck is better than a flashy rewrite with no proof.
