# Reference Data Engine Database Specification

Version: 1.0  
Status: Draft  
Module: Reference Data Engine

---

# 1. Purpose

This document defines the database structure for the Reference Data Engine.

The Reference Data Engine stores reusable lookup data used across Business Suite.

It supports:

- Reference Sets
- Reference Groups
- Reference Values
- Global reference data
- Tenant-specific reference data
- Hierarchical values
- Default values
- Active/inactive values
- Audit logging

---

# 2. Design Principles

The database must be:

- Simple
- Configurable
- Tenant-aware
- Easy to manage
- Reusable across modules
- Free from unnecessary complexity

The Version 1 design should include only must-have fields.

---

# 3. Core Tables

The Reference Data Engine uses three main tables:

````text
reference_sets
reference_groups
reference_values


---

# 5. Reference Data Tables

## 5.1 reference_sets

Stores high-level containers for reference data.

Examples:

- Platform
- Finance
- HR
- Inventory
- Workflow
- CRM
- Sales
- Procurement

```text
reference_sets


---

---

# 6. Constraints

The following uniqueness constraints should be enforced.

## Reference Sets

```sql
UNIQUE (scope, tenant_id, code)
````

Rules:

- The `code` must be unique within the same scope.
- Global reference sets use `tenant_id = NULL`.
- Tenant reference sets use their respective `tenant_id`.

---

## Reference Groups

```sql
UNIQUE (reference_set_id, scope, tenant_id, code)
```

Rules:

- Group codes must be unique within the same Reference Set.
- Multiple tenants may have groups with the same code, provided they belong to different tenants.

---

## Reference Values

```sql
UNIQUE (reference_group_id, scope, tenant_id, code)
```

Rules:

- Value codes must be unique within the same Reference Group.
- Different Reference Groups may reuse the same value code.

---

# 7. Database Indexing

To ensure good performance, the following indexes should be created.

## Single Column Indexes

- tenant_id
- reference_set_id
- reference_group_id
- parent_id
- code
- status
- scope
- sort_order

---

## Composite Indexes

```text
(scope, tenant_id)

(reference_set_id, status)

(reference_group_id, status)

(reference_group_id, sort_order)
```

These indexes optimize:

- Dropdown loading
- Searching
- Filtering
- Hierarchical queries
- Sorting

---

# 8. Row Level Security (RLS)

The Reference Data Engine must enforce complete tenant isolation.

## Global Reference Data

Global reference data:

- Can be viewed by all authenticated users.
- Can only be modified by the Super Administrator.

Examples:

- Countries
- Currencies
- Languages
- Time Zones

---

## Tenant Reference Data

Tenant reference data:

- Can only be viewed by members of that tenant.
- Can only be managed by authorized Tenant Administrators.

Examples:

- Customer Categories
- Supplier Categories
- Departments
- Positions
- Expense Categories

---

# 9. Default Seed Data

The system should automatically create the following Reference Sets.

## Platform

Contains:

- Countries
- Currencies
- Languages
- Time Zones

---

## Workflow

Contains:

- Workflow Status
- Approval Modes
- Notification Channels

---

## Finance

Contains:

- Payment Methods
- Tax Types
- Expense Categories

---

## CRM

Contains:

- Customer Categories
- Lead Sources

---

## Sales

Contains:

- Sales Channels
- Order Status

---

## Inventory

Contains:

- Item Categories
- Item Types
- Units of Measure

---

## Procurement

Contains:

- Supplier Categories
- Procurement Methods

---

## HR

Contains:

- Employment Types
- Leave Types
- Education Levels

---

## POS

Contains:

- Payment Types
- Receipt Status

These seed records should be created during the initial platform installation.

---

# 10. Soft Deletes

Reference data should use soft deletes.

Use:

```text
deleted_at
```

Rules:

- System reference values must not be permanently deleted.
- Reference values already used by business records must not be permanently deleted.
- Deleted records should remain available for historical reporting and auditing.
- Inactive values should no longer appear in selection lists but remain visible on existing records.

---

# 11. Implementation Rules

The Reference Data Engine must follow these implementation rules.

- Use UUID primary keys.
- Keep the Version 1 database structure simple.
- Avoid unnecessary complexity.
- Do not hardcode dropdown values.
- Business modules must access reference data through the Service Layer.
- UI components must never query database tables directly.
- Enforce Row Level Security.
- Maintain complete audit logs for administrative changes.
- Use soft deletes for all configurable reference data.

---

# 12. Conclusion

The Reference Data Engine database provides a centralized, reusable, and tenant-aware structure for managing lookup and configuration data across Business Suite.

The Version 1 design intentionally focuses on the essential capabilities:

- Reference Sets
- Reference Groups
- Reference Values
- Global and Tenant scopes
- Hierarchical values
- Default values
- Soft deletes
- Audit support
- Row Level Security

This design provides a solid foundation while remaining simple, maintainable, and easy to extend in future releases.
