# Document Numbering Engine Process Specification

Version: 1.0

Status: Approved

Module: Document Numbering Engine

---

# 1. Purpose

This document defines the operational processes of the Document Numbering Engine.

It describes how Numbering Series are configured, how document numbers are generated, reserved, consumed, reset, and audited throughout their lifecycle.

---

# 2. Process Principles

The Document Numbering Engine shall follow these principles:

- Centralized numbering
- Tenant-aware
- Configuration-driven
- Transaction-safe
- Auditable
- Consistent across all modules

Business modules consume numbering services but never generate numbers directly.

---

# 3. Numbering Lifecycle

Every Numbering Series follows the lifecycle below.

```text
Create

↓

Configure

↓

Activate

↓

Generate Numbers

↓

Reserve Numbers

↓

Consume Numbers

↓

Audit

↓

Deactivate

↓

Archive
```

A Numbering Series must be active before it can generate document numbers.

---

# 4. Numbering Series Management

## Purpose

Allow administrators to configure document numbering for business documents.

Examples:

- Invoice Numbers
- Purchase Order Numbers
- Employee Numbers
- Workflow References

---

## Process

```text
Create Numbering Series

↓

Configure Format

↓

Configure Scope

↓

Configure Reset Rule

↓

Activate

↓

Available for Use
```

---

## Business Rules

- Series Code must be unique within a tenant.
- A Numbering Series must belong to one tenant unless it is platform-managed.
- Only active Numbering Series may generate numbers.
- Archived Numbering Series cannot be used for new documents.

---

# 5. Number Generation Process

## Purpose

Generate the next available document number.

---

## Process

```text
Business Module

↓

Request Number

↓

Validate Numbering Series

↓

Load Current Sequence

↓

Generate Next Sequence

↓

Apply Format

↓

Reserve Number

↓

Return Generated Number
```

---

## Business Rules

- Every generated number must be unique.
- Number generation must be transaction-safe.
- Number generation must be atomic.
- Business modules must never calculate document numbers.
- Every generated number must be recorded in history.

---

# 6. Reservation Process

## Purpose

Temporarily reserve a generated number until the business transaction is completed.

---

## Process

```text
Generate Number

↓

Reserve Number

↓

Business Document Saved

↓

Mark Reservation as Used
```

---

## Business Rules

- Reserved numbers cannot be issued to another request.
- Used numbers become permanent.
- Released numbers remain available for audit.
- Reservation expiry should be configurable.

---

# 7. Number Consumption Process

## Purpose

Convert a reserved number into a permanent document number.

---

## Process

```text
Reserved Number

↓

Business Document Saved Successfully

↓

Reservation Updated

↓

Status = Used

↓

Document Number Becomes Permanent
```

---

## Business Rules

- A reserved number may only be used once.
- Used numbers become permanent.
- Used numbers must never be reused.
- Every successful consumption must be recorded in history.

---

# 8. Reservation Release Process

## Purpose

Release a reserved number when a business transaction is cancelled before completion.

---

## Process

```text
Reserved Number

↓

Transaction Cancelled

↓

Reservation Released

↓

Recorded in History
```

---

## Business Rules

- Released numbers remain in the audit history.
- Released numbers are not automatically reused.
- Reservation release must be auditable.

---

# 9. Reservation Expiry Process

## Purpose

Automatically expire abandoned reservations.

---

## Process

```text
Reserved Number

↓

Reservation Timeout Reached

↓

Status = Expired

↓

Recorded in History
```

---

## Business Rules

- Reservation timeout is configurable per Numbering Series.
- Expired reservations remain visible for audit purposes.
- Expired numbers are not automatically reused.

---

# 10. Sequence Reset Process

## Purpose

Automatically reset numbering sequences according to the configured reset rule.

Supported reset rules:

- Never
- Daily
- Monthly
- Quarterly
- Calendar Year
- Financial Year

---

## Process

```text
Current Reset Period Ends

↓

Create New Sequence Period

↓

Sequence Starts at 1

↓

Continue Number Generation
```

---

## Business Rules

- Reset processing must occur automatically.
- Previous sequence periods remain available for reporting.
- Reset operations must be recorded in history.

---

# 11. Number Preview Process

## Purpose

Allow administrators to preview generated numbers before saving configuration.

---

## Process

```text
Configure Numbering Series

↓

Generate Preview

↓

Display Preview

↓

No Sequence Consumed
```

---

## Business Rules

- Preview does not reserve a number.
- Preview does not increment the sequence.
- Preview uses the current configuration.

---

# 12. Numbering Series Activation

## Purpose

Enable a Numbering Series for use.

---

## Process

```text
Inactive Series

↓

Activate

↓

Available to Business Modules
```

---

## Business Rules

- Only active Numbering Series may generate numbers.
- Activation must be recorded in history.

---

# 13. Numbering Series Deactivation

## Purpose

Prevent future use of a Numbering Series.

---

## Process

```text
Active Series

↓

Deactivate

↓

Unavailable for New Requests
```

---

## Business Rules

- Existing document numbers remain valid.
- Existing reservations remain valid until completed or expired.
- Deactivation does not affect historical records.
- Deactivation must be recorded in history.

---

# 14. Numbering Series Modification

## Purpose

Allow administrators to modify an existing Numbering Series.

---

## Process

```text
Select Numbering Series

↓

Edit Configuration

↓

Validate Changes

↓

Save

↓

Audit Change
```

---

## Business Rules

- Prefix may be changed.
- Format Template may be changed.
- Reset Rule may be changed.
- Reservation Timeout may be changed.
- Scope changes should be validated before saving.
- Changes must not invalidate existing document numbers.
- Every modification must be recorded in history.

---

# 15. Manual Sequence Adjustment

## Purpose

Allow authorized administrators to adjust the current sequence when required.

Typical scenarios include:

- Data migration
- Legacy system import
- Initial implementation
- Administrative correction

---

## Process

```text
Open Numbering Series

↓

Adjust Current Sequence

↓

Validate New Value

↓

Save

↓

Audit Change
```

---

## Business Rules

- Only authorized users may adjust sequences.
- The new sequence must not be less than the highest number already used.
- Every adjustment must be audited.
- Sequence adjustments should require a reason.

---

# 16. Validation Process

Before generating a document number, the system shall validate:

- Numbering Series exists.
- Numbering Series is active.
- User has permission.
- Tenant context is valid.
- Branch context is valid, where applicable.
- Format Template is valid.
- Sequence is available.
- Reset period is valid.

Generation must stop if validation fails.

---

# 17. Audit Process

Every important action must create an audit record.

Examples:

- Numbering Series Created
- Numbering Series Updated
- Number Generated
- Number Reserved
- Number Used
- Reservation Released
- Reservation Expired
- Sequence Reset
- Manual Sequence Adjustment
- Numbering Series Activated
- Numbering Series Deactivated

Audit records should include:

- User
- Tenant
- Numbering Series
- Generated Number, where applicable
- Action
- Date & Time

Audit history must be immutable.

---

# 18. Business Module Integration

Business modules consume numbering through the Document Numbering Service.

Integration flow:

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

Business modules must never:

- Generate document numbers.
- Maintain their own sequences.
- Reuse released or expired numbers.
- Bypass the Document Numbering Engine.

---

# 19. Error Handling

The Document Numbering Engine shall handle errors safely.

Examples:

- Numbering Series not found.
- Numbering Series inactive.
- Invalid format template.
- Duplicate number detected.
- Failed sequence lock.
- Invalid scope.
- Missing tenant context.
- Missing branch context.

Rules:

- Errors must be logged.
- Users should receive clear, actionable messages.
- Transactions must be rolled back on failure.
- Partial number generation must never occur.

---

# 20. Future Enhancements

Future versions of the Document Numbering Engine may support:

- Conditional numbering rules.
- Multiple active series selection rules.
- Environment-specific numbering.
- Barcode generation.
- QR Code generation.
- External numbering services.
- AI-assisted numbering recommendations.
- Automatic archival of inactive series.

These enhancements should integrate without requiring redesign of the engine.

---

# 21. Implementation Rules

The Document Numbering Engine shall comply with:

- docs/architecture/Architecture.md
- docs/architecture/CodingStandards.md
- docs/architecture/DesignLanguage.md
- specs/platform-core/security.md

Implementation requirements:

- React + TypeScript
- PostgreSQL
- Supabase
- UUID Primary Keys
- Service Layer Architecture
- API-First Design
- Transaction-safe sequence generation
- Row Level Security

---

# 22. Success Criteria

The Document Numbering Engine workflows are considered complete when:

- Numbering Series can be configured.
- Document numbers are generated correctly.
- Reservations function correctly.
- Used numbers are permanent.
- Released and expired reservations are handled correctly.
- Reset rules function correctly.
- Manual sequence adjustment is controlled and audited.
- Validation rules are enforced.
- Audit history is complete.
- Business modules successfully consume the service.

---

# 23. Conclusion

The Document Numbering Engine provides a reliable, configurable, and centralized process for generating document numbers throughout Business Suite.

By separating numbering from business modules and enforcing consistent lifecycle management, the platform guarantees uniqueness, auditability, scalability, and maintainability across all tenants and business processes.
