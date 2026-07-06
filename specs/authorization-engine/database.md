# Authorization Engine Database Specification

Version: 1.0

Status: Approved

Module: Authorization Engine

---

# 1. Purpose

This document defines the database structure for the Authorization Engine.

The Authorization Engine provides centralized authorization across Business Suite by managing roles, permissions, resources, policies, assignments, authorization decisions, and authorization history.

The design supports tenant isolation, policy-based authorization, resource protection, and future authorization models such as ABAC and PBAC.

---

# 2. Design Principles

The database shall be:

- Centralized
- Tenant-aware
- Policy-driven
- Resource-aware
- Highly scalable
- Auditable
- Extensible

Business modules shall never implement independent authorization stores.

All authorization data shall be managed through the Authorization Engine.

---

# 3. Architecture

The Authorization Engine database consists of two logical layers.

## Configuration Layer

```text
Roles

↓

Permissions

↓

Resources

↓

Policies

↓

Assignments
```

Defines who may perform what actions.

---

## Runtime Layer

```text
Authorization Request

↓

Policy Evaluation

↓

Authorization Decision

↓

Authorization History
```

Records actual authorization evaluations.

---

# 4. Core Tables

Version 1 consists of the following primary tables.

```text
roles
        │
        ├──────────────┐
        ▼              ▼
role_permissions   user_roles

permissions
        │
        ▼
resources

policies

authorization_history
```

Future versions may introduce:

- permission_cache
- delegated_permissions
- temporary_permissions
- policy_conditions
- authorization_sessions
- resource_attributes

---

# 5. Table Responsibilities

## roles

Defines business and platform roles.

Examples:

- Super Administrator
- Tenant Administrator
- Finance Manager
- HR Officer
- Branch Manager

---

## permissions

Defines available permissions.

Examples:

- invoice.view
- invoice.create
- invoice.approve
- report.export

---

## resources

Defines protected resources.

Examples:

- Customer
- Invoice
- Document
- Workflow
- Report

---

## role_permissions

Maps permissions to roles.

---

## user_roles

Assigns roles to users.

---

## policies

Defines policy rules.

Examples:

- Approval limits
- Branch restrictions
- Department restrictions
- Workflow restrictions

---

## authorization_history

Stores authorization decisions.

Supports:

- Audit
- Troubleshooting
- Compliance

---

# 6. Database Tables

## 6.1 roles

Defines authorization roles.

```text
roles
```

| Column      | Type      | Notes                       |
| ----------- | --------- | --------------------------- |
| id          | uuid      | Primary Key                 |
| tenant_id   | uuid      | Nullable for platform roles |
| role_code   | text      | Unique role code            |
| role_name   | text      | Display name                |
| description | text      | Optional                    |
| is_system   | boolean   | Default false               |
| is_active   | boolean   | Default true                |
| created_at  | timestamp | Required                    |
| updated_at  | timestamp | Required                    |

### Business Rules

- Role codes must be unique within a tenant.
- Platform roles may have `tenant_id = null`.
- System roles cannot be deleted by tenant users.
- Inactive roles cannot be assigned to users.

---

## 6.2 actions

Defines reusable authorization actions.

```text
actions
```

| Column      | Type      | Notes                                 |
| ----------- | --------- | ------------------------------------- |
| id          | uuid      | Primary Key                           |
| action_code | text      | Example: view, create, update, delete |
| action_name | text      | Display name                          |
| description | text      | Optional                              |
| is_active   | boolean   | Default true                          |
| created_at  | timestamp | Required                              |

### Business Rules

- Actions are reusable across modules.
- Action codes must be unique.
- Actions should be standardized across Business Suite.

Examples:

- view
- create
- update
- delete
- approve
- reject
- export
- import
- download
- configure

---

## 6.3 resources

Defines protected resources.

```text
resources
```

| Column        | Type      | Notes                                |
| ------------- | --------- | ------------------------------------ |
| id            | uuid      | Primary Key                          |
| resource_code | text      | Example: invoice, customer, document |
| resource_name | text      | Display name                         |
| module_code   | text      | Owning module                        |
| description   | text      | Optional                             |
| is_active     | boolean   | Default true                         |
| created_at    | timestamp | Required                             |

### Business Rules

- Resource codes must be unique.
- Every protected entity should be registered as a resource.
- Resources belong to platform services or business modules.

---

## 6.4 permissions

Defines allowed resource-action combinations.

```text
permissions
```

| Column          | Type      | Notes                    |
| --------------- | --------- | ------------------------ |
| id              | uuid      | Primary Key              |
| resource_id     | uuid      | References resources.id  |
| action_id       | uuid      | References actions.id    |
| permission_code | text      | Example: invoice.approve |
| permission_name | text      | Display name             |
| description     | text      | Optional                 |
| is_active       | boolean   | Default true             |
| created_at      | timestamp | Required                 |

### Business Rules

- Permissions are generated from Resource + Action.
- Permission codes must be unique.
- Business modules must use permission codes consistently.
- Inactive permissions cannot be assigned.

Recommended format:

```text
resource.action
```

Example:

```text
invoice.approve
```

---

## 6.5 user_roles

Assigns roles to users.

```text
user_roles
```

| Column      | Type      | Notes               |
| ----------- | --------- | ------------------- |
| id          | uuid      | Primary Key         |
| tenant_id   | uuid      | Required            |
| user_id     | uuid      | Platform User       |
| role_id     | uuid      | References roles.id |
| assigned_by | uuid      | Platform User       |
| assigned_at | timestamp | Required            |
| expires_at  | timestamp | Optional            |

### Business Rules

- Users may have multiple roles.
- Role assignments are tenant-specific.
- Expired role assignments are ignored.
- Role assignments must be audited.

---

## 6.6 role_permissions

Assigns permissions to roles.

```text
role_permissions
```

| Column        | Type      | Notes                     |
| ------------- | --------- | ------------------------- |
| id            | uuid      | Primary Key               |
| role_id       | uuid      | References roles.id       |
| permission_id | uuid      | References permissions.id |
| assigned_by   | uuid      | Platform User             |
| assigned_at   | timestamp | Required                  |

### Business Rules

- A role may have many permissions.
- A permission may belong to many roles.
- Duplicate assignments are not allowed.
- Permission assignments must be audited.

---

# 7. Reference Data Usage

The Authorization Engine should use the Reference Data Engine for configurable values.

Examples include:

- Policy Types
- Decision Types
- Subject Types
- Assignment Types
- Resource Levels

Examples:

| Reference Group  | Example Values                                                |
| ---------------- | ------------------------------------------------------------- |
| Decision Types   | Allow, Deny                                                   |
| Subject Types    | User, Role, Service Account, Platform Service                 |
| Policy Types     | Approval Limit, Branch Restriction, Ownership, Classification |
| Assignment Types | User Role, Role Permission, Permission Policy                 |
| Resource Levels  | Module, Entity, Record, Field                                 |

This prevents hardcoded authorization values and keeps the engine configurable.

---

# 8. Constraints

The following constraints should be enforced.

## Roles

```sql
UNIQUE (tenant_id, role_code)
```

## Actions

```sql
UNIQUE (action_code)
```

## Resources

```sql
UNIQUE (resource_code)
```

## Permissions

```sql
UNIQUE (permission_code)
```

## User Roles

```sql
UNIQUE (user_id, role_id, tenant_id)
```

## Role Permissions

```sql
UNIQUE (role_id, permission_id)
```

## Permission Policies

```sql
UNIQUE (permission_id, policy_id)
```

These constraints prevent duplicate authorization configuration and ensure consistent permission evaluation.

---

# 9. Indexing

Recommended indexes:

- tenant_id
- role_code
- permission_code
- resource_code
- action_code
- user_id
- decision
- evaluated_at
- correlation_id

Composite indexes:

```text
(tenant_id, user_id)

(role_id, permission_id)

(permission_id, policy_id)

(resource_id, action_id)

(decision, evaluated_at)

(correlation_id)
```

These indexes optimize:

- Permission evaluation
- Role resolution
- Policy evaluation
- Authorization history
- Correlation tracing

---

# 10. Row Level Security

The Authorization Engine must enforce tenant isolation.

Rules:

- Roles remain tenant-specific unless designated as platform roles.
- Permissions are shared platform resources.
- Resources are shared platform resources.
- Policies may be platform-wide or tenant-specific.
- Authorization history remains tenant-specific.

RLS must apply to:

- roles
- user_roles
- policies
- authorization_history

Shared configuration tables such as actions, resources, and permissions may be excluded from tenant filtering.

---

# 11. Platform Integration

The Authorization Engine provides authorization decisions to all platform services.

Consumers include:

Platform Services:

- Platform Core
- Platform Event Bus
- Workflow Engine
- Search & Indexing Engine
- Activity & Audit Engine
- Reporting Engine
- Notification Engine
- Document Management Engine

Business Modules:

- CRM
- Sales
- Finance
- Procurement
- Inventory
- HR
- POS

Business Rules

- Authorization decisions originate from the Authorization Engine.
- Business modules must never bypass authorization checks.
- Authorization decisions should preserve Correlation IDs where applicable.

---

# 12. Authorization Processing

Every authorization request follows the same evaluation sequence.

```text
Receive Request

↓

Load User Roles

↓

Resolve Permissions

↓

Evaluate Policies

↓

Return Decision

↓

Record Authorization History
```

Business Rules

- Authorization evaluation should be deterministic.
- Evaluation should be fast.
- Decisions should be auditable.
- Denied decisions should include a reason.

---

# 13. Seed Data

Default Actions:

- View
- Create
- Update
- Delete
- Approve
- Reject
- Export
- Import
- Download
- Configure

Default Platform Roles:

- Super Administrator
- Platform Administrator
- Tenant Administrator

Default Decision Types:

- Allow
- Deny

---

# 14. Implementation Rules

The database implementation shall follow:

- UUID Primary Keys
- Foreign Key Constraints
- Tenant Isolation
- Row Level Security
- Service Layer Architecture
- API-First Design
- Auditable Assignments
- Append-Only Authorization History

Business modules must never modify authorization tables directly.

Authorization changes shall occur through the Authorization Engine only.

---

# 15. Conclusion

The Authorization Engine database provides a centralized and scalable authorization foundation for Business Suite.

By separating configuration from runtime evaluation, supporting reusable roles, actions, resources, permissions, policies, assignments, and authorization history, the engine delivers consistent, tenant-aware, and auditable authorization across all platform services and business modules while providing a strong foundation for future authorization models.
