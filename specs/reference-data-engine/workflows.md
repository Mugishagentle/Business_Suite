# Reference Data Engine Process Specification

Version: 1.0

Status: Approved

Module: Reference Data Engine

---

# 1. Purpose

This document defines the operational processes of the Reference Data Engine.

It describes how Reference Sets, Reference Groups, and Reference Values are created, reviewed, published, maintained, and archived throughout their lifecycle.

---

# 2. Process Principles

The Reference Data Engine shall follow these principles:

- Centralized management
- Tenant-aware
- Configuration-driven
- Auditable
- Simple to maintain
- Consistent across all modules

Business modules consume reference data but do not manage it directly.

---

# 3. Reference Data Lifecycle

Reference data follows the lifecycle below.

```text
Create

↓

Draft

↓

Review (Optional)

↓

Publish

↓

Active

↓

Inactive

↓

Archived
```

The review stage may be enabled or disabled by tenant policy.

---

# 4. Reference Set Management

## Purpose

Organize related Reference Groups.

Examples:

- Platform
- Finance
- Workflow
- Inventory
- CRM
- HR

---

## Process

```text
Create Reference Set

↓

Enter Details

↓

Save Draft

↓

Review

↓

Publish

↓

Available for Use
```

---

## Business Rules

- Code must be unique within scope.
- Draft Reference Sets are not available to business modules.
- Published Reference Sets become available immediately.
- Archived Reference Sets cannot be assigned to new Reference Groups.

---

# 5. Reference Group Management

## Purpose

Organize related Reference Values.

Examples:

- Payment Methods
- Workflow Status
- Tax Types
- Customer Categories

---

## Process

```text
Create Reference Group

↓

Assign Reference Set

↓

Configure

↓

Publish

↓

Available for Use
```

---

## Business Rules

- Every Reference Group belongs to one Reference Set.
- Draft groups are hidden from business modules.
- Archived groups remain available for historical reporting.

---

# 6. Reference Value Management

## Purpose

Manage selectable values used throughout Business Suite.

Examples:

- Cash
- Mobile Money
- Approved
- Pending
- Annual Leave

---

## Process

```text
Create Value

↓

Assign Reference Group

↓

Configure

↓

Publish

↓

Available for Selection
```

---

## Business Rules

- Code must be unique within the Reference Group.
- Only active values appear in dropdowns.
- Inactive values remain visible on historical records.
- Archived values cannot be edited.

---

# 7. Reference Value Activation

## Purpose

Allow a Reference Value to become available for use by business modules.

---

## Process

```text
Draft

↓

Publish

↓

Active

↓

Available in Dropdowns
```

---

## Business Rules

- Only published and active values may be selected in business modules.
- Activation should be recorded in the audit history.
- Activation should not affect historical records.

---

# 8. Reference Value Deactivation

## Purpose

Prevent future use of a Reference Value without affecting historical data.

---

## Process

```text
Active

↓

Deactivate

↓

Inactive

↓

Hidden from New Selections
```

---

## Business Rules

- Inactive values must not appear in new dropdown selections.
- Existing records using inactive values remain valid.
- Deactivation should not delete the value.
- Deactivation must be audited.

---

# 9. Default Value Management

## Purpose

Allow a Reference Group to define a default value.

---

## Process

```text
Reference Group

↓

Select Default Value

↓

Save

↓

Default Updated
```

---

## Business Rules

- Only one default value may exist per Reference Group.
- Default values must be active.
- Changing the default should automatically remove the previous default.
- Business modules may use the default value during record creation.

---

# 10. Hierarchical Reference Values

## Purpose

Support parent-child relationships where required.

Examples:

```text
Item Categories

Electronics
    ├── Laptops
    ├── Monitors
    └── Accessories

Furniture
    ├── Chairs
    ├── Tables
    └── Cabinets
```

---

## Business Rules

- Parent values may have multiple child values.
- Child values belong to only one parent.
- Parent values may exist without children.
- Circular parent relationships are not allowed.
- Deleting or archiving a parent must be validated if child values exist.

---

# 11. Import Process

## Purpose

Bulk create or update reference data.

---

## Process

```text
Upload File

↓

Validate

↓

Preview

↓

Confirm

↓

Import

↓

Summary
```

---

## Business Rules

- Validate required fields.
- Detect duplicate codes.
- Report invalid rows.
- Do not overwrite existing records without confirmation.
- Record the import in audit history.

---

# 12. Export Process

## Purpose

Export reference data for reporting or migration.

Supported formats:

- Excel
- CSV

---

## Business Rules

- Export respects tenant scope.
- Export respects user permissions.
- Filters should apply to exported data.
- Export activity should be logged.

---

# 13. Search Process

Reference data should support searching by:

- Code
- Name
- Description

Filters should support:

- Reference Set
- Reference Group
- Scope
- Status
- Parent Value

Search results should update efficiently and support pagination where required.

---

# 14. Audit Process

## Purpose

Maintain a complete history of administrative changes.

The following actions must create audit records:

- Reference Set Created
- Reference Set Updated
- Reference Group Created
- Reference Group Updated
- Reference Value Created
- Reference Value Updated
- Activated
- Deactivated
- Archived
- Imported
- Exported

Audit records should include:

- User
- Tenant
- Action
- Date & Time
- Previous Value
- New Value

Audit history must be immutable.

---

# 15. Business Module Integration

Business modules must consume reference data through the Reference Data Service.

Typical process:

```text
Business Module

↓

ReferenceDataService

↓

Reference Data Engine

↓

Return Active Values

↓

Display to User
```

Business modules must never:

- Query reference tables directly.
- Hardcode lookup values.
- Duplicate reference data.

---

# 16. Validation Process

Before a Reference Value is saved, the system must validate:

- Required fields.
- Unique code within the Reference Group.
- Valid parent reference.
- Valid tenant scope.
- Valid status.
- Default value rules.

Validation failures must return user-friendly messages.

---

# 17. Archive Process

Reference data should be archived instead of deleted whenever possible.

## Process

```text
Active

↓

Archive

↓

Archived

↓

Read-Only

↓

Available for Historical Records
```

---

## Business Rules

- Archived values cannot be selected for new records.
- Archived values remain visible on historical records.
- Archived values cannot be edited.
- System values should not be archived unless performed by the Super Administrator.

---

# 18. Error Handling

The Reference Data Engine should handle errors gracefully.

Examples:

- Duplicate Code
- Invalid Parent
- Missing Reference Group
- Invalid Tenant
- Missing Permissions
- Import Validation Errors

Rules:

- Errors must be logged.
- Users should receive clear, actionable messages.
- Partial updates should not occur.
- Transactions should be rolled back on failure.

---

# 19. Future Enhancements

Future versions of the Reference Data Engine may support:

- Multi-language reference values.
- Effective date ranges.
- Bulk editing.
- Drag-and-drop hierarchy management.
- AI-assisted categorization.
- External synchronization.
- REST API for third-party systems.
- Reference templates.
- Formula-based reference values.

These enhancements should integrate without requiring redesign of the core engine.

---

# 20. Implementation Rules

The Reference Data Engine must comply with:

- `docs/architecture/Architecture.md`
- `docs/architecture/CodingStandards.md`
- `docs/architecture/DesignLanguage.md`
- `specs/platform-core/security.md`

Implementation requirements:

- React + TypeScript
- Supabase
- PostgreSQL
- UUID Primary Keys
- Service Layer Architecture
- Row Level Security
- API-First Design

Reference data must remain configurable and reusable across the entire platform.

---

# 21. Success Criteria

The Reference Data Engine workflows are considered complete when:

- Reference Sets can be created and managed.
- Reference Groups can be created and managed.
- Reference Values can be created and managed.
- Draft, Active, Inactive, and Archived states function correctly.
- Default values are enforced.
- Hierarchical values function correctly.
- Import and export processes work correctly.
- Validation rules are enforced.
- Audit history is maintained.
- Business modules consume reference data through the service layer.

---

# 22. Conclusion

The Reference Data Engine provides a consistent and controlled process for managing reusable lookup and configuration data throughout Business Suite.

By centralizing reference data management, the platform reduces duplication, improves consistency, simplifies maintenance, and ensures that all modules rely on a single, authoritative source of configurable values.
