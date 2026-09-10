---
description: 📡 Production observability, structured logging, OpenTelemetry distributed tracing, and Sentry error tracking integration workflow.
---

# /telemetry - Observability & Distributed Tracing Mode

$ARGUMENTS

## Purpose & Core Rule

Activates TELEMETRY mode for establishing production-grade structured logging, error monitoring (Sentry), and distributed tracing (OpenTelemetry).

> **TELEMETRY = STRUCTURED LOGS, CORRELATION IDS, ZERO UNCAUGHT DRIFT**

- Enforce JSON-structured logging with standard contextual fields (timestamp, level, service, traceId).
- Propagate correlation/request IDs across HTTP gateways, background queues, and database spans.
- Sanitize logs automatically to ensure passwords, API keys, tokens, and PII are never recorded.

---

## When to Use

- **Use when:** Setting up production logging, integrating Sentry/Datadog, instrumenting OpenTelemetry spans, diagnosing distributed latency, or auditing sensitive log leaks.
- **Do not use:** Using raw `console.log` for quick local debugging without structured context.

---

## Supported Intents

```text
/telemetry setup pino structured logger with request id
/telemetry integrate sentry error tracking for nextjs
/telemetry add opentelemetry tracing to express api
/telemetry audit and mask pii/secrets in log output
/telemetry configure correlation id middleware across microservices
```

---

## Observability Dimensions & Standards

| Dimension | Standard & Tooling |
| :--- | :--- |
| **1. Structured Logging** | High-performance JSON logger (Pino in Node, Winston, Zap in Go, Loguru in Python). |
| **2. Correlation Tracing** | Generate or forward `x-request-id` / `traceparent` headers through all outbound HTTP/gRPC calls. |
| **3. PII & Secret Redaction** | Built-in redaction keys: `password`, `token`, `authorization`, `creditCard`, `secret`. |
| **4. Error Context** | Capture unhandled rejections with full stack trace, user ID, environment, and breadcrumbs. |
| **5. Health & Metrics** | Standardized `/healthz`, `/readyz`, and Prometheus `/metrics` endpoints. |

---

## Execution Protocol

### Phase 1: Audit Current Logging & Monitoring
- Scan codebase for unformatted `console.log`/`print` calls, missing error handlers, and unmasked secrets.

### Phase 2: Configure Central Logger
- Instantiate central logger with log levels (`debug`, `info`, `warn`, `error`) and JSON formatters.

### Phase 3: Add Correlation ID Middleware
- Attach unique Request ID (`crypto.randomUUID()`) to incoming requests and inject into AsyncLocalStorage / Context.

### Phase 4: Configure Error Tracking (Sentry / SDK)
- Initialize error monitoring with release tags, environment names, and sample rates.

### Phase 5: OpenTelemetry Auto-Instrumentation (Optional)
- Wrap HTTP handlers, database queries (Prisma/pg), and external fetches with tracing spans.

### Phase 6: Verify Log Output & PII Redaction
- Test sample requests (happy path and error paths). Verify JSON format, traceId inclusion, and secret masking.

---

## Output Format

```markdown
## 📡 Observability & Telemetry Report: [Scope]

### 1. Instrumentation Summary
- **Logger:** [Pino / Structured JSON configured]
- **Error Tracking:** [Sentry initialized with environment tagging]
- **Tracing:** [OpenTelemetry HTTP/DB spans enabled]

### 2. Standardized Log Schema Sample
```json
{
  "level": "error",
  "time": 1723900000000,
  "service": "order-api",
  "traceId": "c8f9b2-48a1",
  "userId": "usr_123",
  "msg": "Payment processing failed",
  "err": { "message": "Card declined", "code": "CARD_ERROR" }
}
```

### 3. Verification & PII Check
- Redaction verified on sensitive headers: [Passed]
- Trace context propagated across async boundaries: [Verified]
```

---

## Definition of Done

1. Central structured logger configured with automatic PII redaction.
2. Correlation ID middleware active across all API entry points.
3. Unhandled errors properly captured with stack traces and trace context.
