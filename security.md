---
description: 🔐 Practical security audit and hardening workflow for apps, web frontends, APIs, and services. Covers code-level and config-level risks with evidence-driven findings and prioritized remediation.
---

# /security - Practical Security Audit Mode

$ARGUMENTS

---

## Purpose

This command activates SECURITY mode for practical application security review and hardening.

Use it to find real risks in code, configuration, dependencies, authentication, authorization, secrets handling, data exposure, and deployment posture.

---

## What Security Means Here

This workflow is designed for real software teams shipping web apps, APIs, services, workers, and modern application stacks.

It focuses on:

- secrets exposure
- authentication weaknesses
- authorization gaps
- input validation failures
- injection risk
- unsafe file handling
- insecure session/token handling
- dependency and supply-chain exposure
- insecure environment/configuration
- excessive data exposure
- logging/privacy leaks
- missing operational protections

This workflow is **not** intended to be:

- purely academic
- OWASP keyword dumping without proof
- fear-driven reporting with no prioritization
- vulnerability theater without actionable remediation

The goal is to produce findings that a real team can act on immediately.

---

## Core Rule

> **SECURITY = FIND REAL ATTACK SURFACES, PROVE RISK WITH EVIDENCE, PRIORITIZE FIXES THAT MATTER**

Default expectation:

- findings should be tied to code, config, or observed behavior
- severity should reflect realistic impact
- remediation should be practical
- uncertainty should be labeled clearly

---

## Why This Exists

Without a dedicated security workflow, teams often:

- focus only on syntax errors and miss dangerous trust boundaries
- confuse authentication with authorization
- leak secrets through config, logs, or frontend exposure
- ship unsafe defaults because no one checked environment posture
- over-report theoretical issues and under-report exploitable ones
- fix visible symptoms while leaving the actual security gap open

`/security` exists to make security review concrete, practical, and usable during development.

---

## When To Use

Use `/security` when:

- reviewing an app before release
- auditing auth or permission logic
- checking API endpoints or admin features
- validating config/environment safety
- reviewing file upload, webhooks, tokens, or secrets
- investigating possible data exposure
- checking third-party dependency risk
- hardening an existing codebase
- reviewing a sensitive feature like billing, admin, identity, or exports

Use it both:

- proactively before deploy
- reactively after a risky change or suspected issue

---

## Supported Intents

```text
/security
/security auth and session flow
/security admin permissions
/security api endpoints
/security file upload pipeline
/security environment and secrets handling
/security before production deploy
/security payment and webhook handling
/security user data exposure risks
```

---

## Relationship To Other Workflows

| Need | Best Workflow |
|------|---------------|
| Understand current behavior first | `/analyze` |
| Fix a confirmed defect | `/debug` |
| Improve structure safely | `/refactor` |
| Improve performance | `/optimize` |
| Audit practical security risks | `/security` |
| Validate protections with tests | `/test` |
| Ship after checks pass | `/deploy` |

Security reviews often work best after `/analyze`, and often lead into `/debug`, `/test`, or `/deploy`.

---

## Security Review Goals

When `/security` is triggered, the assistant should try to answer:

1. Where are the trust boundaries?
2. What input is attacker-controlled?
3. What secrets exist and where can they leak?
4. How is identity established?
5. How is access control enforced?
6. What sensitive actions exist?
7. What data could be exposed or over-returned?
8. Which dependencies or configs expand attack surface?
9. Which findings are actually exploitable or high-value?
10. What should be fixed first?

---

## Review Scope Levels

Choose the smallest scope that fully answers the request.

### Level 1: Focused Security Check

Use for:

- one feature
- one endpoint group
- one auth flow
- one upload flow
- one admin page

Deliverables:

- attack surface summary
- concrete findings
- prioritized fixes

### Level 2: Subsystem Security Audit

Use for:

- auth/session system
- billing
- admin/backoffice
- webhook processing
- file storage pipeline
- user data export/import

Deliverables:

- trust boundary map
- code-level and config-level risks
- severity-ranked remediation plan

### Level 3: Release Security Review

Use for:

- production readiness
- broad app/API audit
- release gate before deploy

Deliverables:

- high-priority risks
- unsafe defaults
- dependency/config posture
- deployment hardening checklist

---

## Security Protocol

Follow this sequence unless evidence requires a narrower path.

### Phase 1: Clarify Scope And Sensitivity

Identify:

- target feature, route, subsystem, or release scope
- data sensitivity level
- privileged roles involved
- external integrations involved

Examples of high-sensitivity areas:

- admin access
- payments
- PII
- authentication
- password reset
- webhooks
- file upload
- impersonation tools
- exports/imports

### Phase 2: Map Trust Boundaries

Find where untrusted input enters the system:

- HTTP requests
- query params
- headers
- cookies
- request body
- uploaded files
- webhooks
- background job payloads
- environment variables
- CLI input
- third-party callbacks

Also identify boundaries between:

- client and server
- controller and service
- service and DB
- app and external API
- app and storage
- user role levels

### Phase 3: Identify Sensitive Assets

Look for:

- secrets
- tokens
- passwords
- API keys
- sessions
- user PII
- financial data
- internal admin operations
- audit logs
- export files
- privileged config

Ask:

- what would an attacker want here?
- what would be damaging if leaked, forged, or escalated?

### Phase 4: Review Code-Level Controls

Check whether code properly handles:

- authentication
- authorization
- input validation
- output encoding/sanitization
- query safety
- path safety
- file safety
- crypto usage
- token/session handling
- webhook verification
- rate limit or abuse controls
- error exposure

### Phase 5: Review Config-Level Controls

Check whether environment and deployment posture properly handles:

- secret storage
- `.env` exposure risk
- client-exposed env vars
- default credentials
- debug mode in production
- CORS policy
- security headers
- cookie flags
- HTTPS assumptions
- dependency audit posture
- build-time secret injection
- storage bucket exposure
- log redaction
- admin tooling exposure

### Phase 6: Validate Findings By Evidence

For each issue, capture:

- where it lives
- why it matters
- what attacker-controlled input reaches it
- what control is missing or weak
- practical impact
- confidence level

Do not present pure speculation as a confirmed vulnerability.

### Phase 7: Prioritize Remediation

Order fixes by:

1. exploitability
2. impact
3. blast radius
4. ease of abuse
5. ease of remediation

Focus first on:

- auth/authz gaps
- secret exposure
- injection risk
- file upload abuse
- admin privilege escalation
- unsafe production config
- sensitive data leakage

---

## Code-Level Security Checklist

Review these areas when relevant.

### 1. Authentication

Check for:

- missing auth guard on protected routes
- inconsistent auth enforcement across endpoints
- trusting client-only auth state
- weak password reset flow
- insecure token generation or verification
- accepting unsigned or weakly validated identity input

Ask:

- who proves identity?
- where is identity checked?
- can a caller bypass that check?

### 2. Authorization

Check for:

- role checks missing on sensitive actions
- ownership checks missing for object access
- admin actions callable by standard users
- frontend-only permission enforcement
- indirect privilege escalation via ID swapping

Ask:

- who is allowed?
- where is that enforced?
- is authorization checked server-side every time it matters?

### 3. Input Validation

Check for:

- missing schema validation
- trusting raw request input
- inconsistent validation across create/update paths
- unsafe coercion
- hidden assumptions around nullable/optional fields

### 4. Injection Risk

Check for:

- SQL injection
- NoSQL injection
- command injection
- template injection
- unsafe eval/dynamic code
- unsafe regex use where attacker input controls complexity

### 5. XSS And Output Safety

Check for:

- unsanitized HTML rendering
- dangerous DOM insertion
- markdown/html rendering without sanitization
- reflected user input in templates or UI
- stored content rendered without output safety

### 6. File Upload And File Access

Check for:

- trusting extension only
- trusting MIME type from client
- unbounded upload size
- path traversal
- executable upload risk
- public bucket exposure
- unsafe file serving

### 7. Session, Cookie, And Token Handling

Check for:

- missing `HttpOnly`, `Secure`, `SameSite`
- long-lived tokens without rotation
- tokens in local storage where risk is high
- session fixation risk
- logout not invalidating sensitive session state
- refresh flow weaknesses

### 8. Webhooks And External Callbacks

Check for:

- missing signature verification
- replay protection missing
- weak source trust assumptions
- processing external events before verification

### 9. Logging And Error Exposure

Check for:

- secrets in logs
- tokens in logs
- PII in logs
- stack traces exposed to clients
- verbose internal error detail returned publicly

### 10. Sensitive Data Exposure

Check for:

- over-broad API responses
- returning fields the client does not need
- leaking internal IDs, secrets, hashes, or metadata
- export endpoints without filtering or authorization

---

## Config-Level Security Checklist

Review these areas when relevant.

### 1. Secrets And Environment

Check for:

- hardcoded secrets
- secrets committed to repo
- secrets in sample files that look real
- secrets exposed to frontend bundle
- missing separation between server-only and public env vars
- unsafe secret fallback defaults

### 2. Dependency And Supply Chain

Check for:

- outdated vulnerable dependencies
- unpinned risky dependencies where policy requires pinning
- direct use of abandoned packages in sensitive paths
- install scripts or build scripts with unusual privilege

### 3. Deployment Posture

Check for:

- debug mode enabled in production
- dev-only endpoints left exposed
- health/admin endpoints insufficiently protected
- permissive CORS
- open storage buckets
- missing transport security assumptions

### 4. Security Headers And Browser Protections

Check for:

- missing CSP where relevant
- missing `X-Frame-Options` or `frame-ancestors`
- missing `X-Content-Type-Options`
- weak referrer policy
- inconsistent CSRF posture

### 5. Rate Limiting And Abuse Resistance

Check for:

- auth brute-force protection missing
- password reset abuse possible
- email/webhook/send endpoints unthrottled
- search/export endpoints easily abused at scale

### 6. Data Storage And Retention

Check for:

- plaintext secrets or tokens stored unnecessarily
- sensitive data cached too broadly
- unencrypted sensitive artifacts where policy expects protection
- logs or exports retained too openly

---

## Severity Guide

Use severity based on practical impact, not drama.

### Critical

Use when the issue likely enables:

- auth bypass
- privilege escalation to sensitive actions
- secret compromise with major impact
- direct injection into sensitive systems
- broad sensitive data exfiltration

### High

Use when the issue likely enables:

- strong unauthorized access path
- meaningful data leakage
- unsafe production posture on exposed surface
- exploitable file handling or token weakness

### Medium

Use when the issue:

- weakens security materially
- needs other conditions to exploit
- expands attack surface but not immediate full compromise

### Low

Use when the issue:

- is mostly hygiene
- is low-impact alone
- should still be fixed to reduce future risk

---

## Confidence Labels

When useful, label findings with confidence:

- **High confidence**: directly confirmed in code/config/behavior
- **Medium confidence**: strongly supported but missing one proof step
- **Low confidence**: plausible but unconfirmed

If confidence is low, say what evidence is missing.

---

## Practical Heuristics By System Type

### Web App / SPA

Focus on:

- client/server trust boundary
- exposed env vars
- route protection vs server enforcement
- XSS risk
- token storage
- admin UI assumptions

### API / Backend

Focus on:

- authn/authz on every sensitive endpoint
- input validation
- injection risk
- data filtering
- rate limiting
- error exposure

### Admin / Internal Tools

Focus on:

- role enforcement
- impersonation controls
- auditability
- bulk action abuse
- export/PII exposure

### File And Media Pipelines

Focus on:

- type validation
- storage permissions
- malware/executable exposure paths
- signed URL lifetime
- path traversal

### Webhooks / Integrations

Focus on:

- signature verification
- replay protection
- idempotency
- safe retry handling
- source validation assumptions

---

## Anti-Patterns To Avoid

Do not:

- call something secure because auth exists somewhere in the stack
- trust frontend role checks as true authorization
- assume framework defaults are always safe
- report every theoretical issue with the same severity
- ignore config posture because the code looks clean
- ignore code posture because the infra looks clean
- hide uncertainty
- recommend unrealistic fixes with no regard for the existing system

---

## Output Format

```markdown
## 🔐 Security Report: [Target]

### Scope
- Target: [feature/module/release area]
- Depth: Focused Security Check | Subsystem Security Audit | Release Security Review
- Sensitivity: Low | Medium | High

### Attack Surface Summary
- Entry points: [routes/endpoints/forms/webhooks/uploads/etc.]
- Sensitive assets: [tokens/PII/admin actions/secrets/etc.]
- Trust boundaries: [client->server, server->db, webhook->app, etc.]

### Findings
| Severity | Area | Finding | Evidence | Impact |
|----------|------|---------|----------|--------|
| High | Authorization | Missing ownership check on export endpoint | `src/...` | user can access another user's data |
| Medium | Config | CORS too permissive for admin API | `config/...` | expands abuse surface |
| Low | Logging | Error handler may log token payloads | `src/...` | sensitive log leakage risk |

### Priority Fixes
1. [highest-value remediation]
2. [next remediation]
3. [next remediation]

### Hardening Notes
- [practical improvement]
- [practical improvement]

### Verification
- Code/config reviewed: [what was checked]
- Dependency/config posture: [checked/not checked/partial]
- Confidence: High | Medium | Low

### Follow-Up
- Use `/debug` to patch [specific issue]
- Use `/test` to add security regression coverage around [area]
- Use `/deploy` only after [critical/high] findings are resolved
```

---

## Short Output Format

For smaller requests:

```markdown
## 🔐 Security: [Target]

- Main risk: [one line]
- Evidence: [one line]
- Severity: Critical | High | Medium | Low
- Fix first: [one line]
- Residual risk: [one line]
```

---

## Escalation Rules

Stop and ask for approval if remediation would likely:

- change user-facing behavior significantly
- revoke or reshape public API contract
- rotate or invalidate active credentials broadly
- require schema/data migration with operational risk
- disable live integrations or admin functionality

If a finding is severe but evidence is partial, report it clearly and recommend confirmation steps.

---

## Handoff Matrix

After `/security`, choose next steps intentionally:

| Next Need | Workflow |
|-----------|----------|
| Understand unclear trust flow | `/analyze` |
| Fix concrete vulnerability | `/debug` |
| Improve structure to support safer controls | `/refactor` |
| Add regression checks for auth/permissions | `/test` |
| Re-check release readiness | `/deploy` |

---

## Example Prompts

```text
/security auth flow and session handling
/security admin API permissions
/security check for secret leaks and unsafe env usage
/security review upload pipeline
/security audit payment webhook handling
/security review before production deploy
/security check user data exposure in API responses
```

---

## Definition Of Done

`/security` is complete only when:

- trust boundaries were identified
- sensitive assets were identified
- both code-level and config-level risks were considered
- findings are evidence-backed or clearly labeled as partial-confidence
- severity is prioritized practically
- top remediation steps are actionable
- follow-up verification is identified

If those are missing, the security review is not complete.

---

## Final Rule

Find the risks that actually matter in the real system.

Prioritize exploitability, impact, and practical remediation over theoretical completeness.
