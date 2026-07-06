# Finance Engine

## API_SPECIFICATION.md

---

# 1. Overview

The Finance Engine exposes a comprehensive set of secure, versioned APIs that provide financial services to Business Suite modules.

The Finance Engine is the only platform component permitted to create, validate, post, reverse, and query accounting transactions.

Business modules never access Finance database tables directly.

All communication occurs through Finance APIs, Platform Events, or Integration Contracts.

---

# 2. API Principles

The Finance Engine APIs follow the Business Suite API standards.

Every API is:

- RESTful
- JSON Based
- Versioned
- Stateless
- Secure
- Multi-Tenant Aware
- Multi-Company Aware
- Multi-Branch Aware
- Idempotent where applicable
- Fully Auditable

---

# 3. Base URL

```text
/api/v1/finance
```

Future versions will be exposed as:

```text
/api/v2/finance
```

---

# 4. Authentication

Authentication is handled by Platform Core using Supabase Auth.

Every request must include a valid access token.

Example:

```http
Authorization: Bearer <access_token>
```

Unauthenticated requests must return:

```http
401 Unauthorized
```

---

# 5. Request Context

Every Finance request executes within an active platform context.

The following values are resolved automatically by Platform Core.

```text
Tenant ID

Company ID

Branch ID

User ID

Workspace ID

Correlation ID
```

Business modules must never manually override these values.

---

# 6. API Architecture

```text
Business Module

        │

Finance API

        │

Finance Service Layer

        │

Validation Layer

        │

Posting Engine

        │

General Ledger

        │

Response
```

---

# 7. API Domains

The Finance Engine exposes APIs organized into functional domains.

```text
Finance

│
├── General Ledger
├── Journals
├── Chart of Accounts
├── Fiscal Years
├── Accounting Periods
├── Accounts Receivable
├── Accounts Payable
├── Cash Management
├── Banking
├── Financial Configuration
├── Reconciliation
├── Reports
└── Posting Services
```

---

# 8. General API Standards

All APIs follow consistent conventions.

## Success Response

```json
{
  "success": true,
  "message": "Operation completed successfully.",
  "data": {}
}
```

---

## Error Response

```json
{
  "success": false,
  "message": "Validation failed.",
  "errors": []
}
```

---

## Pagination

Collection endpoints support:

```text
page

pageSize

sort

direction

search

filters
```

---

## Filtering

Common filters include:

- Company
- Branch
- Status
- Currency
- Accounting Period
- Date Range

---

# 9. Posting Service APIs

The Posting Service is the primary integration point for Business Modules.

Business modules submit financial posting requests.

The Finance Engine determines the accounting treatment.

---

## Supported Operations

- Validate Posting
- Preview Posting
- Post Transaction
- Reverse Transaction
- Repost Transaction (Authorized Only)

---

## Posting Flow

```text
Business Module

↓

Posting Request

↓

Validation

↓

Posting Rules

↓

Journal Creation

↓

General Ledger

↓

Posting Event

↓

Response
```

---

# 10. API Design Rules

The following rules apply to every Finance API.

- APIs never expose database tables.
- APIs never expose internal Posting Engine logic.
- APIs validate authorization before processing.
- APIs validate Tenant, Company, and Branch context.
- APIs return standardized responses.
- APIs generate audit events.
- APIs generate correlation IDs for traceability.
- APIs never bypass financial validation.

---
