# Reference Data Engine Specification

Version: 1.0  
Status: Draft  
Module: Reference Data Engine

---

# 1. Purpose

The Reference Data Engine is a shared platform service used to manage configurable lookup data across the entire Business Suite platform.

It replaces hardcoded dropdowns, status values, category lists, and reusable system values with dynamic, tenant-aware, database-managed reference data.

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
- Future Modules

---

# 2. Core Principle

Business Suite must avoid hardcoded dropdown values and status values.

Instead, reusable values should be managed through the Reference Data Engine.

Examples:

- Tenant Status
- Subscription Status
- User Status
- Workflow Status
- Approval Modes
- Industry Types
- Customer Categories
- Supplier Categories
- Item Types
- Units of Measure
- Payment Methods
- Tax Types
- Leave Types
- Employment Types
- Asset Categories
- Expense Categories

---

# 3. Objectives

The Reference Data Engine aims to:

- Centralize reusable lookup values.
- Reduce hardcoded values.
- Support tenant-specific configuration.
- Support platform-wide reference data.
- Support consistent dropdowns across modules.
- Support reporting consistency.
- Support localization in future versions.
- Allow administrators to manage configurable lists without code changes.

---

# 4. Scope

The Reference Data Engine includes:

- Reference Groups
- Reference Values
- Global Reference Data
- Tenant-Specific Reference Data
- Status Lists
- Dropdown Lists
- Sort Order
- Active/Inactive Values
- Metadata
- Default Values
- Import/Export
- Audit Logging

---

# 5. Out of Scope

The Reference Data Engine does not manage:

- Business transactions
- Financial postings
- Inventory movements
- Customer records
- Supplier records
- Employee records
- Approval execution

It only manages reusable reference and lookup data consumed by other modules.

---

# 6. Core Concepts

## Reference Group

A Reference Group represents a collection of related reference values.

Examples:

- Customer Categories
- Supplier Categories
- Workflow Status
- Approval Modes
- Payment Methods
- Units of Measure
- Tax Types
- Leave Types

A Reference Group defines the structure for its reference values.

---

## Reference Value

A Reference Value is an individual item within a Reference Group.

Examples:

Reference Group

Payment Methods

Values

- Cash
- Mobile Money
- Bank Transfer
- Visa
- Mastercard

Each value may contain additional configuration beyond its display name.

---

## Global Reference Data

Global Reference Data is maintained by the Platform Administrator.

Global reference data is shared across all tenants.

Examples:

- Countries
- Currencies
- Languages
- Time Zones
- ISO Codes

Global reference data cannot be modified by tenant administrators unless explicitly allowed.

---

## Tenant Reference Data

Tenant Reference Data belongs to a specific tenant.

Examples:

- Customer Categories
- Supplier Categories
- Expense Categories
- Departments
- Positions
- Asset Categories

Tenant administrators may manage their own reference data.

---

## System Reference Data

System Reference Data is required for correct platform operation.

Examples:

- Workflow Status
- Subscription Status
- User Status
- Approval Modes

System reference data is managed by the Platform Administrator.

---

## Reference Metadata

Every reference value may contain metadata.

Examples:

- Color
- Icon
- Description
- Sort Order
- Parent Reference
- Display Order
- Additional Configuration

Metadata allows reference values to provide richer functionality without requiring schema changes.

---

## Default Values

Reference Groups may define one or more default values.

Example:

Payment Method

Default

Cash

Default values may be used automatically by business modules during record creation.

---

## Reference Set

A Reference Set is a high-level container used to organize related Reference Groups.

Examples:

- Platform
- Finance
- HR
- CRM
- Inventory
- Procurement
- Workflow
- Sales
- POS

A Reference Set helps organize reference data by platform service or business module.

Example:

````text
Reference Set: Finance
    ↓
Reference Group: Payment Methods
    ↓
Reference Values:
        - Cash
        - Bank Transfer
        - Mobile Money



# 7. Reference Categories

The Reference Data Engine supports multiple categories.

## Platform References

Shared across the platform.

Examples:

- Countries
- Currencies
- Languages
- Time Zones

---

## System References

Required for platform functionality.

Examples:

- Workflow Status
- Approval Modes
- Subscription Status
- Notification Channels

---

## Tenant References

Defined by individual tenants.

Examples:

- Customer Categories
- Supplier Categories
- Expense Categories
- Departments
- Positions

---

## Module References

Specific to a business module.

Examples:

CRM

- Lead Sources
- Customer Types

Inventory

- Item Categories
- Stock Adjustment Reasons

Finance

- Account Categories
- Payment Methods
- Tax Types

HR

- Leave Types
- Employment Types
- Education Levels

Each module should consume reference data from the Reference Data Engine instead of maintaining its own lookup tables.

---

# 8. Ownership

Reference data ownership is determined by its category.

Platform Administrator

Owns:

- Global References
- System References

Tenant Administrator

Owns:

- Tenant References

Business Modules

Consume reference data but do not own or manage it directly.

---

# 9. Reference Data Architecture

The Reference Data Engine serves as the single source of truth for reusable lookup data.

Business modules must retrieve reference values from the Reference Data Engine instead of maintaining duplicate lookup tables.

Example:

```text
Inventory

↓

Request Item Categories

↓

Reference Data Engine

↓

Return Active Categories
````

The same approach applies to every business module.

---

# 10. Reference Data Lifecycle

````markdown
Every reference structure follows the lifecycle below.

````text
Create Reference Set

↓

Create Reference Group

↓

Configure Reference Values

↓

Review

↓

Publish

↓

Used by Platform Services and Business Modules

↓

Update When Required

↓

Archive

# 11. Reference Data Consumers

The following platform services and modules consume reference data.

## Platform Core

Examples:

- User Status
- Tenant Status
- Subscription Status
- Business Types
- Time Zones

---

## Workflow Engine

Examples:

- Workflow Status
- Approval Modes
- Notification Channels
- Escalation Types

---

## CRM

Examples:

- Customer Categories
- Lead Sources
- Contact Types
- Opportunity Status

---

## Sales

Examples:

- Order Status
- Invoice Status
- Discount Types
- Sales Channels

---

## Inventory

Examples:

- Item Categories
- Item Types
- Units of Measure
- Adjustment Reasons
- Warehouse Types

---

## Procurement

Examples:

- Purchase Request Status
- Purchase Order Status
- Supplier Categories
- Procurement Methods

---

## Finance

Examples:

- Payment Methods
- Tax Types
- Tax Categories
- Expense Categories
- Account Categories

---

## HR

Examples:

- Employment Types
- Leave Types
- Education Levels
- Job Grades
- Marital Status

---

## POS

Examples:

- Payment Types
- Receipt Status
- Till Status

---

# 12. Reference Data Rules

Reference data should be managed centrally.

Business modules must:

- Read reference values.
- Validate selected values.
- Store only the selected reference identifier.

Business modules must not:

- Create duplicate lookup tables.
- Hardcode dropdown values.
- Hardcode status values.
- Hardcode category lists.

---

# 13. Reference Data Versioning

Reference groups should support controlled evolution.

Rules:

- Existing values should not be deleted if referenced by business data.
- Inactive values remain available for historical records.
- Renaming a reference value should not affect existing records.
- Reference identifiers remain permanent.

---

# 14. Reference Data Status

Reference Groups

- Draft
- Active
- Archived

Reference Values

- Active
- Inactive
- Archived

Inactive values:

- Cannot be selected for new records.
- Remain visible in historical records.

Archived values:

- Cannot be edited.
- Remain available for audit and reporting.

---

# 15. Reference Value Attributes

Each reference value may contain additional attributes.

Examples:

- Code
- Name
- Description
- Icon
- Color
- Sort Order
- Parent Reference
- Default Value
- Display Order
- Metadata (JSON)

Modules may consume these attributes to enhance the user experience without requiring additional configuration.

---

# 9. Reference Data Architecture

The Reference Data Engine serves as the single source of truth for reusable lookup data.

Business modules must retrieve reference values from the Reference Data Engine instead of maintaining duplicate lookup tables.

Example:

```text
Inventory

↓

Request Item Categories

↓

Reference Data Engine

↓

Return Active Categories
````
````

The same approach applies to every business module.

---

# 10. Reference Data Lifecycle

Every reference group follows the lifecycle below.

```text
Create Reference Group

↓

Configure Reference Values

↓

Review

↓

Publish

↓

Used by Business Modules

↓

Update When Required

↓

Archive
```

Reference values should remain available for historical records even if archived.

---

# 11. Reference Data Consumers

The following platform services and modules consume reference data.

## Platform Core

Examples:

- User Status
- Tenant Status
- Subscription Status
- Business Types
- Time Zones

---

## Workflow Engine

Examples:

- Workflow Status
- Approval Modes
- Notification Channels
- Escalation Types

---

## CRM

Examples:

- Customer Categories
- Lead Sources
- Contact Types
- Opportunity Status

---

## Sales

Examples:

- Order Status
- Invoice Status
- Discount Types
- Sales Channels

---

## Inventory

Examples:

- Item Categories
- Item Types
- Units of Measure
- Adjustment Reasons
- Warehouse Types

---

## Procurement

Examples:

- Purchase Request Status
- Purchase Order Status
- Supplier Categories
- Procurement Methods

---

## Finance

Examples:

- Payment Methods
- Tax Types
- Tax Categories
- Expense Categories
- Account Categories

---

## HR

Examples:

- Employment Types
- Leave Types
- Education Levels
- Job Grades
- Marital Status

---

## POS

Examples:

- Payment Types
- Receipt Status
- Till Status

---

# 12. Reference Data Rules

Reference data should be managed centrally.

Business modules must:

- Read reference values.
- Validate selected values.
- Store only the selected reference identifier.

Business modules must not:

- Create duplicate lookup tables.
- Hardcode dropdown values.
- Hardcode status values.
- Hardcode category lists.

---

# 13. Reference Data Versioning

Reference groups should support controlled evolution.

Rules:

- Existing values should not be deleted if referenced by business data.
- Inactive values remain available for historical records.
- Renaming a reference value should not affect existing records.
- Reference identifiers remain permanent.

---

# 14. Reference Data Status

Reference Groups

- Draft
- Active
- Archived

Reference Values

- Active
- Inactive
- Archived

Inactive values:

- Cannot be selected for new records.
- Remain visible in historical records.

Archived values:

- Cannot be edited.
- Remain available for audit and reporting.

---

# 15. Reference Value Attributes

Each reference value may contain additional attributes.

Examples:

- Code
- Name
- Description
- Icon
- Color
- Sort Order
- Parent Reference
- Default Value
- Display Order
- Metadata (JSON)

Modules may consume these attributes to enhance the user experience without requiring additional configuration.

---

# 16. Multi-Tenant Architecture

The Reference Data Engine supports both global and tenant-specific reference data.

Reference data ownership is determined by its scope.

## Global

Managed by the Platform Administrator.

Shared across all tenants.

Examples:

- Countries
- Currencies
- Languages
- Time Zones
- ISO Codes

---

## Tenant

Managed by Tenant Administrators.

Available only within the owning tenant.

Examples:

- Customer Categories
- Supplier Categories
- Expense Categories
- Departments
- Positions
- Asset Categories

The Reference Data Engine must enforce complete tenant isolation.

---

# 17. Integration

Business modules consume reference data through the Service Layer.

Modules must never query reference tables directly.

Example

```text
Finance

↓

ReferenceDataService

↓

Reference Data Engine

↓

Payment Methods
```

The same service should be used by:

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

# 18. Search & Filtering

The Reference Data Engine should support efficient searching.

Users should be able to search by:

- Code
- Name
- Description
- Category
- Status

Filtering should support:

- Scope
- Module
- Active Status
- Parent Reference
- Date Created

---

# 19. Import & Export

Reference data should support bulk operations.

Supported formats:

- Excel
- CSV

Import features:

- Validation
- Duplicate Detection
- Preview
- Error Reporting

Export features:

- Active Values
- Inactive Values
- Filtered Results

Imports should never overwrite existing data without confirmation.

---

# 20. Audit & History

Every change to reference data must be auditable.

Examples:

- Group Created
- Group Updated
- Value Added
- Value Updated
- Value Archived
- Value Activated
- Import Completed

Audit records must include:

- User
- Tenant
- Date & Time
- Previous Value
- New Value

Audit history must never be deleted.

---

# 21. Future Enhancements

Future versions of the Reference Data Engine may include:

- Multi-language Reference Values
- Effective Date Ranges
- Reference Value Versioning
- Reference Templates
- AI-Assisted Classification
- Cross-Tenant Templates
- External Reference Synchronization
- REST API for External Systems
- Dynamic Business Rules
- Formula-Based Reference Values

These enhancements should integrate without requiring architectural redesign.

---

# 22. Implementation Rules

The Reference Data Engine implementation must follow:

- docs/architecture/Architecture.md
- docs/architecture/CodingStandards.md
- docs/architecture/DesignLanguage.md
- docs/architecture/FolderStructure.md

Implementation requirements:

- React + TypeScript
- Tailwind CSS
- shadcn/ui
- Supabase
- PostgreSQL
- Service Layer Architecture
- UUID Primary Keys
- Row Level Security
- API-First Design

Reference data must remain configurable and must never be hardcoded into business modules.

---

# 23. Success Criteria

The Reference Data Engine is considered complete when:

- Reference Groups can be created.
- Reference Values can be managed.
- Global and Tenant scopes function correctly.
- Hierarchical references are supported.
- Default values are supported.
- Active and inactive values behave correctly.
- Import and export function correctly.
- Audit history is maintained.
- Business modules consume reference data successfully.
- Tenant isolation is enforced.

---

# 24. Conclusion

The Reference Data Engine provides a centralized, configurable, and reusable platform service for managing lookup and configuration data across the Business Suite.

By eliminating hardcoded values and duplicate lookup tables, the platform achieves greater consistency, maintainability, flexibility, and scalability.

Every platform service and business module should rely on the Reference Data Engine as the authoritative source for reusable reference data.
