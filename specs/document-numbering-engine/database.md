# Document Numbering Engine Database Specification

Version: 1.0

Status: Draft

Module: Document Numbering Engine

---

# 1. Purpose

This document defines the database structure for the Document Numbering Engine.

The Document Numbering Engine provides centralized generation, reservation, tracking, and auditing of document numbers across the Business Suite platform.

It ensures every generated number is unique, traceable, configurable, and tenant-aware.

---

# 2. Design Principles

The database shall be:

- Simple
- Configurable
- Multi-tenant
- Transaction-safe
- Highly performant
- Auditable
- Scalable

The Version 1 implementation should include only the structures necessary to support reliable numbering.

---

# 3. Core Tables

The Document Numbering Engine consists of four primary tables.

```text
numbering_series

↓

numbering_sequences

↓

numbering_reservations

↓

numbering_history
```

Each table has a distinct responsibility.

---

# 4. Table Responsibilities

## numbering_series

Stores numbering configuration.

Examples:

- Sales Invoice
- Purchase Order
- Employee
- Customer
- Workflow

---

## numbering_sequences

Stores the current running sequence for each numbering series.

This table is responsible for generating the next available sequence safely.

---

## numbering_reservations

Stores reserved numbers.

Reservations prevent duplicate numbers during concurrent processing.

---

## numbering_history

Stores a complete audit trail of numbering activity.

Examples:

- Number Generated
- Number Reserved
- Number Released
- Number Used
- Configuration Changed

---

# 5. Ownership

Numbering Series may belong to:

## Platform

Managed by the Platform Administrator.

Examples:

- Workflow References
- Tenant Numbers

---

## Tenant

Managed by the Tenant Administrator.

Examples:

- Sales Invoices
- Purchase Orders
- Employee Numbers

Business modules consume numbering services but do not own numbering configuration.

---

# 6. Numbering Tables

## 6.1 numbering_series

Stores document numbering configuration.

```text
numbering_series
```

| Column              | Type      | Notes                                                           |
| ------------------- | --------- | --------------------------------------------------------------- |
| id                  | uuid      | Primary key                                                     |
| tenant_id           | uuid      | Optional. Null for platform-level series                        |
| module_code         | text      | Example: sales, finance, hr, workflow                           |
| document_type       | text      | Example: invoice, purchase_order, employee                      |
| series_code         | text      | Required                                                        |
| name                | text      | Required                                                        |
| description         | text      | Optional                                                        |
| prefix              | text      | Example: INV, PO, EMP                                           |
| format_template     | text      | Example: {PREFIX}-{FY}-{NUMBER}                                 |
| scope               | text      | platform, tenant, branch                                        |
| reset_rule          | text      | never, daily, monthly, quarterly, calendar_year, financial_year |
| number_length       | integer   | Example: 6                                                      |
| reservation_minutes | integer   | Optional                                                        |
| status              | text      | active, inactive                                                |
| created_at          | timestamp | Required                                                        |
| updated_at          | timestamp | Required                                                        |
| deleted_at          | timestamp | Optional                                                        |

Rules:

- Series code must be unique within a tenant.
- Platform series have `tenant_id = null`.
- Tenant series must have `tenant_id`.
- Inactive series cannot generate new numbers.
- Business modules must request numbers using `series_code`.

Recommended constraint:

```sql
unique (tenant_id, series_code)
```

---

## 6.2 numbering_sequences

Stores the current sequence state for each numbering series and scope.

```text
numbering_sequences
```

| Column              | Type      | Notes                                         |
| ------------------- | --------- | --------------------------------------------- |
| id                  | uuid      | Primary key                                   |
| tenant_id           | uuid      | Optional                                      |
| numbering_series_id | uuid      | References numbering_series.id                |
| scope_key           | text      | Example: tenant id, branch id, financial year |
| current_number      | bigint    | Current sequence value                        |
| reset_period        | text      | Example: 2026, 2026-07, 2026-07-02            |
| last_generated_at   | timestamp | Optional                                      |
| created_at          | timestamp | Required                                      |
| updated_at          | timestamp | Required                                      |

Rules:

- Each series may have many sequence rows depending on scope and reset period.
- Sequence updates must be transaction-safe.
- Sequence numbers must never be duplicated.
- Sequence rows must be locked during number generation.

Recommended constraint:

```sql
unique (numbering_series_id, scope_key, reset_period)
```

---

## 6.3 numbering_reservations

Stores reserved or generated document numbers.

```text
numbering_reservations
```

| Column              | Type      | Notes                             |
| ------------------- | --------- | --------------------------------- |
| id                  | uuid      | Primary key                       |
| tenant_id           | uuid      | Optional                          |
| numbering_series_id | uuid      | References numbering_series.id    |
| sequence_id         | uuid      | References numbering_sequences.id |
| generated_number    | text      | Required                          |
| sequence_number     | bigint    | Required                          |
| entity_type         | text      | Optional                          |
| entity_id           | uuid      | Optional                          |
| status              | text      | reserved, used, released, expired |
| reserved_by         | uuid      | References platform_users.id      |
| reserved_at         | timestamp | Required                          |
| used_at             | timestamp | Optional                          |
| expires_at          | timestamp | Optional                          |
| created_at          | timestamp | Required                          |
| updated_at          | timestamp | Required                          |

Rules:

- Generated number must be unique within the tenant.
- Used numbers must never be reused.
- Released and expired numbers remain for audit purposes.
- Reservations help prevent duplicate numbers during concurrent transactions.

Recommended constraint:

```sql
unique (tenant_id, generated_number)
```

---

## 6.4 numbering_history

Stores audit history for numbering activity.

```text
numbering_history
```

| Column              | Type      | Notes                                                      |
| ------------------- | --------- | ---------------------------------------------------------- |
| id                  | uuid      | Primary key                                                |
| tenant_id           | uuid      | Optional                                                   |
| numbering_series_id | uuid      | References numbering_series.id                             |
| reservation_id      | uuid      | Optional, references numbering_reservations.id             |
| action              | text      | created, updated, reserved, used, released, expired, reset |
| generated_number    | text      | Optional                                                   |
| performed_by        | uuid      | Optional, references platform_users.id                     |
| metadata            | jsonb     | Optional                                                   |
| created_at          | timestamp | Required                                                   |

Rules:

- History is append-only.
- History must never be edited or deleted.
- Every generated number must have history.
- Configuration changes must be recorded.

---

# 7. Database Constraints

The following constraints shall be enforced.

## Numbering Series

```sql
UNIQUE (tenant_id, series_code)
```

Rules:

- Each tenant may only have one Numbering Series with the same `series_code`.
- Platform-managed series use `tenant_id = NULL`.

---

## Numbering Sequences

```sql
UNIQUE (numbering_series_id, scope_key, reset_period)
```

Rules:

- Only one active sequence exists for a specific Series, Scope, and Reset Period.
- Duplicate sequence records are not permitted.

---

## Number Reservations

```sql
UNIQUE (tenant_id, generated_number)
```

Rules:

- Every generated document number must be unique.
- Generated numbers must never be reused.

---

# 8. Database Indexes

The following indexes should be created.

## Single Column Indexes

- tenant_id
- numbering_series_id
- sequence_id
- series_code
- module_code
- document_type
- scope
- status
- generated_number
- reserved_at
- created_at

---

## Composite Indexes

```text
(tenant_id, series_code)

(module_code, document_type)

(numbering_series_id, scope_key)

(numbering_series_id, reset_period)

(tenant_id, generated_number)

(status, expires_at)
```

These indexes optimize:

- Number generation
- Reservation lookup
- Expired reservation cleanup
- Searching
- Reporting

---

# 9. Concurrency Control

The Document Numbering Engine must support multiple concurrent users.

## Requirements

- Only one sequence update may occur at a time.
- Duplicate numbers must never be generated.
- Number generation must be atomic.
- Failed transactions must not corrupt the sequence.

## Implementation

Sequence generation should execute within a database transaction.

The sequence row should be locked before incrementing the current number.

Only after the transaction succeeds should the generated number be returned.

---

# 10. Reservation Management

Reservations help prevent duplicate numbers during long-running transactions.

Reservation states include:

- Reserved
- Used
- Released
- Expired

## Business Rules

- Reserved numbers cannot be issued to another request.
- Used numbers become permanent.
- Released numbers remain in history.
- Expired reservations remain available for audit.
- Reservation expiry should be configurable.

---

# 11. Reset Processing

The engine shall automatically create a new sequence period when a reset occurs.

Examples:

## Financial Year

```text
2026

↓

2027
```

## Monthly

```text
2026-07

↓

2026-08
```

## Daily

```text
2026-07-01

↓

2026-07-02
```

## Business Rules

- Reset processing should occur automatically.
- Previous sequence periods remain available for reporting.
- Reset events must be recorded in numbering history.

---

# 12. Row Level Security (RLS)

The Document Numbering Engine must enforce tenant isolation.

Rules:

- Platform-managed series may be read where appropriate.
- Tenant numbering configuration is visible only to that tenant.
- Tenant numbering history is isolated.
- Reservation records are tenant-specific.

Row Level Security policies must be applied to all tenant-owned tables.

---

# 13. Soft Deletes

The following tables should support soft deletes:

- numbering_series

Use:

```text
deleted_at
```

Rules:

- Deleted numbering series remain available for historical reporting.
- Series already used by business documents should not be permanently deleted.

Execution tables such as reservations and history should not use soft deletes because they form part of the permanent audit trail.

---

# 14. Audit Requirements

Every important operation must generate a history record.

Examples:

- Series Created
- Series Updated
- Number Generated
- Number Reserved
- Number Released
- Number Used
- Sequence Reset
- Series Activated
- Series Deactivated

Audit records must include:

- User
- Tenant
- Numbering Series
- Generated Number, where applicable
- Action
- Date & Time

History records are immutable.

---

# 15. Seed Data

The platform should seed default Numbering Series for core platform services.

## Platform Core

Examples:

- Tenant Number
- User Number

## Workflow Engine

Examples:

- Workflow Reference Number

## Reference Data Engine

Examples:

- Reference Import Batch Number

Future business modules should seed their own default numbering series during module installation.

---

# 16. Example Numbering Series

## Invoice Number

| Field           | Value                  |
| --------------- | ---------------------- |
| module_code     | sales                  |
| document_type   | invoice                |
| series_code     | STANDARD-INVOICE       |
| prefix          | INV                    |
| format_template | {PREFIX}-{FY}-{NUMBER} |
| scope           | tenant                 |
| reset_rule      | financial_year         |
| number_length   | 6                      |

Example output:

```text
INV-2026-000001
```

---

## Purchase Order Number

| Field           | Value                           |
| --------------- | ------------------------------- |
| module_code     | procurement                     |
| document_type   | purchase_order                  |
| series_code     | LOCAL-PO                        |
| prefix          | PO                              |
| format_template | {PREFIX}/{BRANCH}/{FY}/{NUMBER} |
| scope           | branch                          |
| reset_rule      | financial_year                  |
| number_length   | 6                               |

Example output:

```text
PO/KLA/2026/000001
```

---

## Employee Number

| Field           | Value             |
| --------------- | ----------------- |
| module_code     | hr                |
| document_type   | employee          |
| series_code     | EMPLOYEE-NUMBER   |
| prefix          | EMP               |
| format_template | {PREFIX}-{NUMBER} |
| scope           | tenant            |
| reset_rule      | never             |
| number_length   | 5                 |

Example output:

```text
EMP-00001
```

---

# 17. Business Module Integration Rules

Business modules must request numbers from the Document Numbering Engine.

Modules must provide:

- Tenant ID
- Series Code
- Business Date
- Branch ID, where applicable
- Entity Type
- Entity ID, where applicable

Example request:

```text
Generate Number

tenant_id: active tenant
series_code: STANDARD-INVOICE
branch_id: active branch
business_date: invoice date
entity_type: Invoice
```

The engine returns:

```text
INV-2026-000001
```

Modules must not calculate or increment document numbers themselves.

---

# 18. Failure Handling

The engine must handle failures safely.

Examples:

- Missing Numbering Series
- Inactive Numbering Series
- Invalid Format Template
- Missing Branch for Branch-Scoped Series
- Failed Sequence Lock
- Duplicate Number Conflict
- Expired Reservation

Rules:

- Errors must be logged.
- Users should receive clear messages.
- Transactions must be rolled back on failure.
- Partial number generation must not occur.

---

# 19. Implementation Rules

The database implementation must follow:

- UUID primary keys
- Foreign key constraints
- Transaction-safe sequence generation
- Tenant isolation
- Row Level Security
- Immutable history
- Soft deletes for configuration tables
- No soft deletes for audit/execution tables

Business modules must consume the numbering service through the Service Layer.

---

# 20. Conclusion

The Document Numbering Engine database provides a simple, reliable, and scalable structure for generating document numbers across Business Suite.

The design separates configuration, sequence tracking, reservations, and history to ensure consistency, concurrency safety, and auditability.

Every platform service and business module must rely on this engine for document numbering instead of implementing independent numbering logic.
