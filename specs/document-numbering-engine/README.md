# Document Numbering Engine Specification

Version: 1.0  
Status: Draft  
Module: Document Numbering Engine

---

# 1. Purpose

The Document Numbering Engine is a shared platform service responsible for generating unique, configurable, tenant-aware document numbers across Business Suite.

It ensures that every module uses a consistent and reliable numbering system.

The engine is used by:

- Platform Core
- Workflow Engine
- CRM
- Sales
- Inventory
- Procurement
- Finance
- HR
- POS
- Reporting

---

# 2. Core Principle

No module should generate document numbers independently.

All document numbers must be requested from the Document Numbering Engine.

This prevents:

- Duplicate numbers
- Inconsistent formats
- Hardcoded numbering rules
- Module-specific numbering logic
- Difficult reporting and auditing

---

# 3. Examples

The engine may generate numbers such as:

````text
INV-2026-000001
PO-2026-000045
PR-KLA-2026-000009
EMP-000123
WF-2026-001245
CUS-2026-000004

---

# 7. Core Concepts

## Numbering Series

A Numbering Series defines how numbers are generated for a specific document type.

Examples:

- Invoice Numbers
- Purchase Order Numbers
- Customer Numbers
- Employee Numbers
- Workflow Reference Numbers

Each series belongs to a tenant unless defined as a platform-level series.

---

## Document Type

A Document Type identifies the kind of document requiring a number.

Examples:

- Invoice
- Purchase Order
- Purchase Request
- Receipt
- Employee
- Customer
- Workflow Instance

Each document type may have its own numbering series.

---

## Number Format

The Number Format defines how the final number appears.

Examples:

```text
{PREFIX}-{YEAR}-{NUMBER}

{PREFIX}/{BRANCH}/{YEAR}/{NUMBER}

{PREFIX}-{NUMBER}

---

# 3. Core Concepts

The Document Numbering Engine is built around the following concepts:

- Numbering Series
- Document Types
- Number Formats
- Number Sequences
- Number Scopes
- Reset Rules
- Reservations

Each concept works together to generate unique and configurable document numbers.

---

# 4. Numbering Series

A Numbering Series defines how document numbers are generated.

Examples:

- Standard Sales Invoice
- Proforma Invoice
- Credit Note
- Local Purchase Order
- International Purchase Order
- Employee Numbers

Each Numbering Series contains:

- Prefix
- Format
- Scope
- Reset Rule
- Current Sequence
- Status

Business modules request numbers from a specific Numbering Series.

---

# 5. Document Types

A Document Type represents the business document requesting a number.

Examples:

- Customer
- Supplier
- Employee
- Sales Invoice
- Purchase Order
- Payment Voucher
- Journal
- Workflow Instance

A Document Type may have one or many Numbering Series.

Example:

```text
Sales Invoice

↓

Standard Invoice

↓

INV-2026-000001
````

---

# 6. Number Format

The Number Format determines the appearance of generated numbers.

Supported placeholders include:

- `{PREFIX}`
- `{YEAR}`
- `{FY}`
- `{MONTH}`
- `{DAY}`
- `{BRANCH}`
- `{NUMBER}`

Examples:

```text
{PREFIX}-{YEAR}-{NUMBER}
```

Produces:

```text
INV-2026-000001
```

Another example:

```text
{PREFIX}/{BRANCH}/{YEAR}/{NUMBER}
```

Produces:

```text
PO/KLA/2026/000023
```

Number formats should be fully configurable.

---

# 7. Number Scopes

The scope determines where document numbers must be unique.

Supported scopes:

- Platform
- Tenant
- Branch

Future versions may support:

- Department
- Warehouse
- Business Unit

Example:

Tenant Scope

```text
INV-2026-000001
```

Branch Scope

```text
INV-KLA-2026-000001

INV-MBR-2026-000001
```

Each scope maintains its own independent sequence.

---

# 8. Reset Rules

Reset Rules determine when numbering sequences restart.

Supported rules:

- Never
- Daily
- Monthly
- Quarterly
- Calendar Year
- Financial Year

Example:

```text
INV-2026-000001
INV-2026-000002
```

New Financial Year

```text
INV-2027-000001
```

Reset execution must be automatic.

---

# 9. Number Reservation

Document numbers may be reserved before a business transaction is completed.

Reservation helps prevent conflicts during long-running operations.

A reserved number may have one of the following states:

- Reserved
- Used
- Released
- Expired

Business Rules

- Reserved numbers are unique.
- Used numbers cannot be reused.
- Released numbers are retained for audit purposes.
- Reservation expiry should be configurable.
- Every reservation must be audited.

---

# 10. Number Generation Lifecycle

Every document number follows the lifecycle below.

```text
Business Module

↓

Request Number

↓

Validate Numbering Series

↓

Determine Scope

↓

Determine Reset Rule

↓

Generate Next Sequence

↓

Format Number

↓

Reserve Number

↓

Return Number

↓

Business Module Saves Document

↓

Mark Number as Used
```

If the document is not successfully created, the reserved number should be released or marked as expired according to the configured reservation policy.

---

# 11. Business Module Integration

Every platform service and business module must obtain document numbers through the Document Numbering Engine.

The integration flow is:

```text
Business Module

↓

DocumentNumberService

↓

Document Numbering Engine

↓

Generate Number

↓

Return Number

↓

Save Business Document
```

The following modules consume the service:

## Platform Core

Examples:

- Tenant Numbers
- User Numbers

---

## Workflow Engine

Examples:

- Workflow Reference Number

---

## CRM

Examples:

- Customer Number
- Lead Number
- Opportunity Number

---

## Sales

Examples:

- Quotation Number
- Sales Order Number
- Invoice Number
- Receipt Number

---

## Inventory

Examples:

- Item Number
- Stock Adjustment Number
- Stock Transfer Number
- Stock Count Number

---

## Procurement

Examples:

- Purchase Request Number
- RFQ Number
- Purchase Order Number
- Goods Received Note Number

---

## Finance

Examples:

- Payment Voucher Number
- Journal Number
- Receipt Number
- Budget Number

---

## HR

Examples:

- Employee Number
- Leave Request Number
- Recruitment Number

---

## POS

Examples:

- POS Receipt Number
- Till Session Number

---

# 12. Number Preview

Administrators should be able to preview the next generated number before saving configuration.

Example

Configuration

```text
Prefix

INV

Financial Year

2026

Digits

6
```

Preview

```text
INV-2026-000001
```

Previewing must never consume a sequence number.

---

# 13. Audit

Every numbering operation must be auditable.

Examples:

- Numbering Series Created
- Numbering Series Updated
- Number Reserved
- Number Released
- Number Used
- Reset Executed
- Configuration Changed

Audit records should include:

- User
- Tenant
- Numbering Series
- Generated Number
- Action
- Date & Time

---

# 14. Future Enhancements

Future versions of the Document Numbering Engine may support:

- Multiple numbering series per document type.
- Conditional numbering rules.
- Dynamic placeholders.
- AI-assisted numbering recommendations.
- External numbering integration.
- Barcode generation.
- QR code generation.
- Number recycling policies, where legally permitted.
- Environment-aware numbering for Development, Test, and Production.

These enhancements should integrate without requiring architectural redesign.

---

# 15. Implementation Rules

The Document Numbering Engine must comply with:

- docs/architecture/Architecture.md
- docs/architecture/CodingStandards.md
- docs/architecture/DesignLanguage.md
- docs/architecture/FolderStructure.md

Implementation requirements:

- React + TypeScript
- Supabase
- PostgreSQL
- UUID Primary Keys
- Service Layer Architecture
- API-First Design
- Transaction-safe sequence generation

Business modules must never generate document numbers directly.

---

# 16. Success Criteria

The Document Numbering Engine is considered complete when:

- Numbering Series can be created and managed.
- Configurable formats are supported.
- Placeholder replacement works correctly.
- Scope rules function correctly.
- Reset rules function correctly.
- Concurrent number generation is safe.
- Reservation and release are supported.
- Preview works without consuming sequence numbers.
- Business modules successfully consume the service.
- Audit history is maintained.

---

# 17. Conclusion

The Document Numbering Engine provides a centralized, configurable, and reliable numbering service for the Business Suite platform.

By separating numbering logic from business modules, the platform achieves consistency, scalability, auditability, and maintainability while ensuring that every generated document number is unique, traceable, and governed by configurable business rules.
