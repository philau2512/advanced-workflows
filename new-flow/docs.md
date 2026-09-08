---
description: 📖 Comprehensive Technical Documentation & Release Notes Engine. Generate OpenAPI/Swagger schemas, API references, module guides, and Keep-a-Changelog release notes.
argument-hint: "[target] [--api] [--openapi] [--changelog] [--unreleased] [--flow]"
---

# /docs - Technical Documentation & Changelog Engine

$ARGUMENTS

---

## 🎯 Purpose & Core Principles

Activates **DOCUMENTATION & RELEASE NOTES MODE** to extract accurate API contracts, generate OpenAPI/Swagger schemas, document module architectures, and compile release changelogs from git history.

> **IRON INVARIANTS:**
> 1. **EMPIRICAL FIDELITY:** All API routes, parameters, types, and error codes must be verified against actual handler code. Zero hallucinated parameters.
> 2. **STANDARDIZED FORMATS:** Output valid OpenAPI 3.0/3.1 YAML/JSON, clean Markdown API references, or "Keep a Changelog" formatted release notes.
> 3. **CONVENTIONAL COMMITS PARSING:** Automatically categorize commits into Breaking Changes, Added, Fixed, Changed, and Security.

---

## ⚙️ Argument Parsing & Usage Modes

```text
# 1. API Documentation & OpenAPI Specification:
/docs --api <path/to/routes or controllers> [--openapi] [--out <file>]

# 2. Changelog & Release Notes Generation:
/docs --changelog [<git-range>, e.g. v1.0.0...v1.1.0 or --unreleased]

# 3. Module & Feature Flow Documentation:
/docs <module or feature path> [--flow]
```

### Flags:
- `--api`: Extracts endpoint methods, request bodies, query params, headers, and responses from backend handlers.
- `--openapi`: Formats API documentation as a valid OpenAPI 3.0 YAML/JSON specification.
- `--changelog`: Analyzes git commit history to compile structured release notes.
- `--unreleased`: Compiles changelog from the latest git tag up to `HEAD`.
- `--flow`: Documents end-to-end data flow and component relationships for a specific module.

---

## 🔬 Mode A: API Contract & OpenAPI Generator (`--api`)

Inspects route files and controllers (Express, NestJS, FastAPI, Go Gin, Spring Boot) to document contracts:

```
┌────────────────────────┐     ┌────────────────────────┐     ┌────────────────────────┐
│ 1. Scan Route & Method │ ──► │ 2. Inspect Body & DTO  │ ──► │ 3. Extract Responses   │
│    (HTTP verb, path)   │     │    (Zod, Pydantic, TS) │     │    (200, 400, 401, 500)│
└────────────────────────┘     └────────────────────────┘     └───────────┬────────────┘
                                                                          │
                                                                          ▼
                                                              ┌────────────────────────┐
                                                              │ 4. Output OpenAPI/Doc  │
                                                              │    (JSON/YAML/Markdown)│
                                                              └────────────────────────┘
```

### Extracted Dimensions:
1. **Route & Method:** `POST /api/v1/orders`, `@UseGuards(JwtAuthGuard)`.
2. **Parameters:** Path params (`:id`), Query strings (`?page=1&limit=20`).
3. **Request Body:** Typed JSON payload with required vs optional attributes.
4. **Responses:** Success schema with realistic sample data, plus standardized error codes.

---

## 📜 Mode B: Changelog & Release Notes Generator (`--changelog`)

Parses git log commits according to **Keep a Changelog** and **Conventional Commits**:

### Classification Rules:
- **🚨 Breaking Changes:** Commits with `BREAKING CHANGE` footer or `type!:`. Highlighted prominently at top.
- **✨ Added (`feat`):** New endpoints, UI features, or user capabilities.
- **🐛 Fixed (`fix`):** Bug fixes, error handling patches, regression resolutions.
- **⚡ Performance (`perf`):** Measurable latency reductions, query optimizations.
- **♻️ Changed (`refactor`):** Internal architectural changes preserving external behavior.
- **🔒 Security (`sec`):** Patches for authentication, authorization, or CVE vulnerabilities.

---

## 📋 Standard Documentation Output Templates

### API Documentation Template (Markdown or OpenAPI):

```markdown
## 📖 API Reference: [Module Name]

### `POST /api/v1/checkout`
Initiates order payment and creates transactional record.

- **Authentication:** Bearer JWT (`Authorization: Bearer <token>`)
- **Headers:** `Content-Type: application/json`

#### Request Body
```json
{
  "cartId": "cart_987",
  "paymentMethod": "stripe",
  "couponCode": "SUMMER20" // optional
}
```

#### Responses
- **`201 Created`**: Returns confirmed order details.
- **`400 Bad Request`**: Validation error (e.g. cart empty).
- **`401 Unauthorized`**: Missing or expired session token.
```

### Changelog Template:

```markdown
## [1.2.0] - 2026-09-03

### 🚨 Breaking Changes
- `POST /api/v1/orders` now requires `shippingAddressId` parameter.

### ✨ Added
- Added support for VnPAY QR code payments in checkout flow.
- Added dark mode toggle in user preference settings.

### 🐛 Fixed
- Fixed race condition causing double charge on rapid button clicks.
- Resolved memory leak in background websocket connection pool.
```
