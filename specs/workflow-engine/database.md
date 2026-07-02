# Workflow Engine Database Specification

Version: 1.0

Status: Approved

Module: Workflow Engine

---

# 1. Purpose

This document defines the database structure for the Workflow Engine.

The Workflow Engine database is designed to support dynamic, tenant-specific workflows that can be used by every module within Business Suite.

The design is generic and configurable.

No table should be tied to a specific business module.

---

# 2. Design Principles

The Workflow Engine database follows these principles:

- Multi-tenant
- Configuration-driven
- Version-controlled
- Module-independent
- Scalable
- Auditable
- Extensible

Every tenant can configure different workflows without affecting other tenants.

---

# 3. Core Entities

The Workflow Engine consists of the following entities:

- Workflow
- Workflow Version
- Workflow Process
- Workflow Level
- Workflow Level Approver
- Workflow Condition
- Workflow Notification
- Workflow Instance
- Workflow Instance Level
- Workflow Action
- Workflow Comment
- Workflow Attachment
- Workflow Delegation
- Workflow History

Future entities may include:

- Workflow Templates
- Workflow Rules
- Workflow SLA
- Workflow Analytics

---

# 4. Entity Relationships

```text
Workflow
    │
    ├─────────────┐
    ▼             ▼
Workflow Version  Workflow Process
        │
        ▼
Workflow Level
        │
        ▼
Workflow Level Approver
        │
        ▼
Workflow Condition
        │
        ▼
Workflow Notification

Business Record
        │
        ▼
Workflow Instance
        │
        ▼
Workflow Instance Level
        │
        ▼
Workflow Action
        │
        ▼
Workflow History
```

---

# 5. Common Fields

Every tenant-owned table should contain:

- id (UUID)
- tenant_id
- created_at
- updated_at
- created_by
- updated_by
- status
- remarks (optional)

Every table should support:

- Soft Delete
- Audit Logging
- Row Level Security

---

# 6. Workflow Definition Tables

## 6.1 workflows

Stores the main workflow definition.

A workflow belongs to one tenant and represents one configurable business workflow.

Examples:

- Purchase Request Approval
- Expense Approval
- Leave Approval
- Stock Adjustment Approval
- Invoice Approval

```text
workflows
```

| Column        | Type      | Notes                                           |
| ------------- | --------- | ----------------------------------------------- |
| id            | uuid      | Primary key                                     |
| tenant_id     | uuid      | References tenants.id                           |
| name          | text      | Required                                        |
| code          | text      | Required                                        |
| description   | text      | Optional                                        |
| workflow_type | text      | approval, review, verification, acknowledgement |
| module_code   | text      | Optional. Example: finance, procurement, hr     |
| entity_type   | text      | Example: ExpenseClaim, PurchaseRequest          |
| status        | text      | draft, published, archived, inactive            |
| is_default    | boolean   | Default false                                   |
| created_by    | uuid      | References platform_users.id                    |
| updated_by    | uuid      | References platform_users.id                    |
| created_at    | timestamp | Required                                        |
| updated_at    | timestamp | Required                                        |
| deleted_at    | timestamp | Optional                                        |

Rules:

- Workflows are tenant-specific.
- Workflow code must be unique per tenant.
- A tenant may have multiple workflows for the same entity type.
- Only one workflow should be default for a specific entity type unless conditions determine otherwise.

Recommended constraint:

```sql
unique (tenant_id, code)
```

---

## 6.2 workflow_versions

Stores workflow versions.

A workflow instance must always run against the workflow version that was active when the instance started.

```text
workflow_versions
```

| Column         | Type      | Notes                        |
| -------------- | --------- | ---------------------------- |
| id             | uuid      | Primary key                  |
| tenant_id      | uuid      | References tenants.id        |
| workflow_id    | uuid      | References workflows.id      |
| version_number | integer   | Required                     |
| status         | text      | draft, published, archived   |
| published_at   | timestamp | Optional                     |
| published_by   | uuid      | References platform_users.id |
| created_by     | uuid      | References platform_users.id |
| updated_by     | uuid      | References platform_users.id |
| created_at     | timestamp | Required                     |
| updated_at     | timestamp | Required                     |
| deleted_at     | timestamp | Optional                     |

Rules:

- Workflow versions preserve historical workflow structure.
- Existing workflow instances must not be affected when a workflow is edited.
- New workflow instances should use the latest published version.
- Published versions should not be edited directly.
- Editing a published workflow should create a new draft version.

Recommended constraint:

```sql
unique (workflow_id, version_number)
```

---

## 6.3 workflow_processes

Maps a workflow to a business process or entity.

This allows modules to connect their records to the Workflow Engine without hardcoding workflow logic.

```text
workflow_processes
```

| Column       | Type      | Notes                             |
| ------------ | --------- | --------------------------------- |
| id           | uuid      | Primary key                       |
| tenant_id    | uuid      | References tenants.id             |
| workflow_id  | uuid      | References workflows.id           |
| module_code  | text      | Example: finance, procurement, hr |
| entity_type  | text      | Example: ExpenseClaim, Invoice    |
| process_code | text      | Example: EXPENSE_APPROVAL         |
| name         | text      | Required                          |
| description  | text      | Optional                          |
| status       | text      | active, inactive                  |
| created_by   | uuid      | References platform_users.id      |
| updated_by   | uuid      | References platform_users.id      |
| created_at   | timestamp | Required                          |
| updated_at   | timestamp | Required                          |
| deleted_at   | timestamp | Optional                          |

Rules:

- Processes are tenant-specific.
- One entity type may have multiple workflow processes.
- Process selection may depend on conditions such as amount, branch, department, or category.
- Business modules should reference workflow processes instead of embedding approval logic.

Recommended constraint:

```sql
unique (tenant_id, process_code)
```

---

# 7. Workflow Level Configuration Tables

## 7.1 workflow_levels

Stores the levels or steps within a workflow version.

```text
workflow_levels
```

| Column              | Type      | Notes                                  |
| ------------------- | --------- | -------------------------------------- |
| id                  | uuid      | Primary key                            |
| tenant_id           | uuid      | References tenants.id                  |
| workflow_id         | uuid      | References workflows.id                |
| workflow_version_id | uuid      | References workflow_versions.id        |
| level_number        | integer   | Required                               |
| name                | text      | Required                               |
| description         | text      | Optional                               |
| approval_mode       | text      | any_one, all, majority, first_response |
| is_required         | boolean   | Default true                           |
| allow_return        | boolean   | Default true                           |
| allow_delegation    | boolean   | Default true                           |
| auto_approve        | boolean   | Default false                          |
| escalation_hours    | integer   | Optional                               |
| status              | text      | active, inactive                       |
| created_by          | uuid      | References platform_users.id           |
| updated_by          | uuid      | References platform_users.id           |
| created_at          | timestamp | Required                               |
| updated_at          | timestamp | Required                               |
| deleted_at          | timestamp | Optional                               |

Rules:

- Levels belong to a workflow version.
- Level numbers determine execution order.
- Levels must be configurable per tenant.
- A workflow version may have one or many levels.
- Published workflow levels should not be edited directly.

Recommended constraint:

```sql
unique (workflow_version_id, level_number)
```

---

## 7.2 workflow_level_approvers

Stores approvers assigned to workflow levels.

Approvers may be static or dynamic.

```text
workflow_level_approvers
```

| Column            | Type      | Notes                                                                                       |
| ----------------- | --------- | ------------------------------------------------------------------------------------------- |
| id                | uuid      | Primary key                                                                                 |
| tenant_id         | uuid      | References tenants.id                                                                       |
| workflow_level_id | uuid      | References workflow_levels.id                                                               |
| approver_type     | text      | user, role, department_head, supervisor, branch_manager, position, permission, dynamic_rule |
| user_id           | uuid      | Optional, references platform_users.id                                                      |
| role_id           | uuid      | Optional, references roles.id                                                               |
| permission_id     | uuid      | Optional, references permissions.id                                                         |
| position_code     | text      | Optional                                                                                    |
| department_id     | uuid      | Future                                                                                      |
| branch_id         | uuid      | Optional, references branches.id                                                            |
| rule_definition   | jsonb     | Optional                                                                                    |
| sort_order        | integer   | Optional                                                                                    |
| status            | text      | active, inactive                                                                            |
| created_by        | uuid      | References platform_users.id                                                                |
| updated_by        | uuid      | References platform_users.id                                                                |
| created_at        | timestamp | Required                                                                                    |
| updated_at        | timestamp | Required                                                                                    |
| deleted_at        | timestamp | Optional                                                                                    |

Rules:

- Approvers are resolved dynamically at runtime.
- A level may have multiple approvers.
- Approver type determines which field is used.
- Do not hardcode approvers inside business modules.

---

## 7.3 workflow_conditions

Stores conditions that determine whether a workflow, level, or approver applies.

```text
workflow_conditions
```

| Column              | Type      | Notes                                                                                              |
| ------------------- | --------- | -------------------------------------------------------------------------------------------------- |
| id                  | uuid      | Primary key                                                                                        |
| tenant_id           | uuid      | References tenants.id                                                                              |
| workflow_id         | uuid      | Optional, references workflows.id                                                                  |
| workflow_version_id | uuid      | Optional, references workflow_versions.id                                                          |
| workflow_level_id   | uuid      | Optional, references workflow_levels.id                                                            |
| condition_scope     | text      | workflow, level, approver                                                                          |
| field_name          | text      | Required                                                                                           |
| operator            | text      | equals, not_equals, greater_than, less_than, greater_or_equal, less_or_equal, contains, in, not_in |
| value               | text      | Required                                                                                           |
| value_type          | text      | text, number, date, boolean, user_code, lookup                                                     |
| logical_operator    | text      | and, or                                                                                            |
| sort_order          | integer   | Optional                                                                                           |
| status              | text      | active, inactive                                                                                   |
| created_by          | uuid      | References platform_users.id                                                                       |
| updated_by          | uuid      | References platform_users.id                                                                       |
| created_at          | timestamp | Required                                                                                           |
| updated_at          | timestamp | Required                                                                                           |
| deleted_at          | timestamp | Optional                                                                                           |

Rules:

- Conditions must be tenant-scoped.
- Conditions may apply to a full workflow, a level, or an approver.
- Conditions should be evaluated before assigning approvers.
- Conditions should support future expansion into a rules engine.

---

## 7.4 workflow_level_notifications

Stores notification rules for workflow levels.

```text
workflow_level_notifications
```

| Column            | Type      | Notes                                                                           |
| ----------------- | --------- | ------------------------------------------------------------------------------- |
| id                | uuid      | Primary key                                                                     |
| tenant_id         | uuid      | References tenants.id                                                           |
| workflow_level_id | uuid      | References workflow_levels.id                                                   |
| event_type        | text      | started, pending, approved, rejected, returned, escalated, delegated, completed |
| channel           | text      | email, sms, in_app                                                              |
| template_id       | uuid      | References notification_templates.id                                            |
| recipient_type    | text      | initiator, approver, supervisor, role, specific_user                            |
| recipient_user_id | uuid      | Optional, references platform_users.id                                          |
| recipient_role_id | uuid      | Optional, references roles.id                                                   |
| status            | text      | active, inactive                                                                |
| created_by        | uuid      | References platform_users.id                                                    |
| updated_by        | uuid      | References platform_users.id                                                    |
| created_at        | timestamp | Required                                                                        |
| updated_at        | timestamp | Required                                                                        |
| deleted_at        | timestamp | Optional                                                                        |

Rules:

- Notifications must be configurable per workflow level.
- Notification templates should come from Platform Core.
- Workflow Engine should not hardcode notification messages.

---

# 8. Workflow Execution Tables

## 8.1 workflow_instances

Stores every running or completed workflow instance.

A workflow instance is created whenever a business process starts a workflow.

Examples:

- Expense Claim #1025
- Purchase Request #458
- Leave Request #89

```text
workflow_instances
```

| Column              | Type      | Notes                                                                    |
| ------------------- | --------- | ------------------------------------------------------------------------ |
| id                  | uuid      | Primary key                                                              |
| tenant_id           | uuid      | References tenants.id                                                    |
| workflow_id         | uuid      | References workflows.id                                                  |
| workflow_version_id | uuid      | References workflow_versions.id                                          |
| process_id          | uuid      | References workflow_processes.id                                         |
| entity_type         | text      | Example: ExpenseClaim                                                    |
| entity_id           | uuid      | Business record ID                                                       |
| initiated_by        | uuid      | References platform_users.id                                             |
| current_level_id    | uuid      | References workflow_levels.id                                            |
| status              | text      | pending, in_progress, approved, rejected, returned, cancelled, completed |
| started_at          | timestamp | Required                                                                 |
| completed_at        | timestamp | Optional                                                                 |
| remarks             | text      | Optional                                                                 |
| created_at          | timestamp | Required                                                                 |
| updated_at          | timestamp | Required                                                                 |

---

Rules

- Every workflow instance belongs to exactly one tenant.
- Every workflow instance references a specific workflow version.
- Workflow versions must never change after the instance starts.
- Every business record should have at most one active workflow instance unless explicitly allowed by business rules.

---

## 8.2 workflow_instance_levels

Stores the execution status of every workflow level.

```text
workflow_instance_levels
```

| Column               | Type      | Notes                                                                |
| -------------------- | --------- | -------------------------------------------------------------------- |
| id                   | uuid      | Primary key                                                          |
| tenant_id            | uuid      | References tenants.id                                                |
| workflow_instance_id | uuid      | References workflow_instances.id                                     |
| workflow_level_id    | uuid      | References workflow_levels.id                                        |
| assigned_to          | uuid      | References platform_users.id                                         |
| approval_mode        | text      | Copied from workflow level                                           |
| level_status         | text      | pending, approved, rejected, returned, skipped, escalated, delegated |
| assigned_at          | timestamp | Required                                                             |
| actioned_at          | timestamp | Optional                                                             |
| due_at               | timestamp | Optional                                                             |
| comments             | text      | Optional                                                             |
| created_at           | timestamp | Required                                                             |
| updated_at           | timestamp | Required                                                             |

---

Rules

- Each workflow level executed should create one workflow_instance_level record.
- Assigned approvers are resolved when the workflow reaches that level.
- Historical assignments must never be modified.

---

## 8.3 workflow_actions

Stores every action taken within a workflow.

```text
workflow_actions
```

| Column                     | Type      | Notes                                                                 |
| -------------------------- | --------- | --------------------------------------------------------------------- |
| id                         | uuid      | Primary key                                                           |
| tenant_id                  | uuid      | References tenants.id                                                 |
| workflow_instance_id       | uuid      | References workflow_instances.id                                      |
| workflow_instance_level_id | uuid      | References workflow_instance_levels.id                                |
| action_by                  | uuid      | References platform_users.id                                          |
| action                     | text      | submit, approve, reject, return, delegate, escalate, cancel, withdraw |
| comments                   | text      | Optional                                                              |
| action_date                | timestamp | Required                                                              |
| created_at                 | timestamp | Required                                                              |

---

Rules

- Every user action creates a workflow action.
- Workflow actions are immutable.
- Workflow actions should be displayed in chronological order.

---

## 8.4 workflow_history

Stores the complete audit history of every workflow instance.

```text
workflow_history
```

| Column               | Type      | Notes                                                                                          |
| -------------------- | --------- | ---------------------------------------------------------------------------------------------- |
| id                   | uuid      | Primary key                                                                                    |
| tenant_id            | uuid      | References tenants.id                                                                          |
| workflow_instance_id | uuid      | References workflow_instances.id                                                               |
| event_type           | text      | workflow_started, level_started, approved, rejected, returned, delegated, escalated, completed |
| performed_by         | uuid      | References platform_users.id                                                                   |
| workflow_level_id    | uuid      | Optional                                                                                       |
| description          | text      | Required                                                                                       |
| metadata             | jsonb     | Optional                                                                                       |
| created_at           | timestamp | Required                                                                                       |

---

Rules

- History records are append-only.
- History must never be edited.
- History provides the complete audit trail of the workflow.

---

## 8.5 workflow_comments

Stores discussion and comments related to a workflow instance.

```text
workflow_comments
```

| Column               | Type      | Notes                            |
| -------------------- | --------- | -------------------------------- |
| id                   | uuid      | Primary key                      |
| tenant_id            | uuid      | References tenants.id            |
| workflow_instance_id | uuid      | References workflow_instances.id |
| user_id              | uuid      | References platform_users.id     |
| comment              | text      | Required                         |
| is_internal          | boolean   | Default false                    |
| created_at           | timestamp | Required                         |

---

Rules

- Comments become part of the workflow history.
- Internal comments are visible only to authorized users.
- Future versions may support mentions and rich text.

---

## 8.6 workflow_attachments

Stores supporting documents attached to workflow instances.

```text
workflow_attachments
```

| Column               | Type      | Notes                            |
| -------------------- | --------- | -------------------------------- |
| id                   | uuid      | Primary key                      |
| tenant_id            | uuid      | References tenants.id            |
| workflow_instance_id | uuid      | References workflow_instances.id |
| file_name            | text      | Required                         |
| file_path            | text      | Required                         |
| file_size            | bigint    | Optional                         |
| mime_type            | text      | Optional                         |
| uploaded_by          | uuid      | References platform_users.id     |
| uploaded_at          | timestamp | Required                         |

---

Rules

- Attachments belong to the workflow instance.
- File access must respect tenant permissions.
- Files should be stored securely according to Platform Core security policies.

---

# 9. Workflow Support Tables

## 9.1 workflow_delegations

Stores workflow approval delegations.

Delegation allows one user to approve on behalf of another user for a specified period.

```text
workflow_delegations
```

| Column       | Type      | Notes                        |
| ------------ | --------- | ---------------------------- |
| id           | uuid      | Primary key                  |
| tenant_id    | uuid      | References tenants.id        |
| delegated_by | uuid      | References platform_users.id |
| delegated_to | uuid      | References platform_users.id |
| start_date   | timestamp | Required                     |
| end_date     | timestamp | Required                     |
| reason       | text      | Optional                     |
| status       | text      | active, expired, cancelled   |
| created_by   | uuid      | References platform_users.id |
| created_at   | timestamp | Required                     |
| updated_at   | timestamp | Required                     |

---

Rules

- Delegation belongs to a tenant.
- Delegation is time-bound.
- Expired delegations should not be considered during approver resolution.
- Delegations must be respected by the Workflow Engine.

---

## 9.2 workflow_escalations

Stores escalations generated by workflow execution.

```text
workflow_escalations
```

| Column               | Type      | Notes                            |
| -------------------- | --------- | -------------------------------- |
| id                   | uuid      | Primary key                      |
| tenant_id            | uuid      | References tenants.id            |
| workflow_instance_id | uuid      | References workflow_instances.id |
| workflow_level_id    | uuid      | References workflow_levels.id    |
| escalated_from       | uuid      | References platform_users.id     |
| escalated_to         | uuid      | References platform_users.id     |
| escalation_reason    | text      | Required                         |
| escalated_at         | timestamp | Required                         |
| resolved_at          | timestamp | Optional                         |
| status               | text      | pending, resolved                |

---

Rules

- Escalations are generated automatically or manually.
- Escalations should never overwrite the original approver.
- Every escalation becomes part of Workflow History.

---

## 9.3 workflow_notifications

Stores notifications generated by workflow events.

```text
workflow_notifications
```

| Column               | Type      | Notes                                                                  |
| -------------------- | --------- | ---------------------------------------------------------------------- |
| id                   | uuid      | Primary key                                                            |
| tenant_id            | uuid      | References tenants.id                                                  |
| workflow_instance_id | uuid      | References workflow_instances.id                                       |
| recipient_id         | uuid      | References platform_users.id                                           |
| channel              | text      | email, sms, in_app                                                     |
| event_type           | text      | approval_required, approved, rejected, delegated, escalated, completed |
| delivery_status      | text      | pending, sent, failed                                                  |
| delivered_at         | timestamp | Optional                                                               |
| created_at           | timestamp | Required                                                               |

---

Rules

- Notification generation should be asynchronous.
- Delivery failures should be logged.
- Notifications should respect tenant notification settings.

---

## 9.4 workflow_sla

Stores SLA configuration for workflow levels.

```text
workflow_sla
```

| Column            | Type      | Notes                         |
| ----------------- | --------- | ----------------------------- |
| id                | uuid      | Primary key                   |
| tenant_id         | uuid      | References tenants.id         |
| workflow_level_id | uuid      | References workflow_levels.id |
| response_hours    | integer   | Required                      |
| warning_hours     | integer   | Optional                      |
| escalation_hours  | integer   | Optional                      |
| status            | text      | active, inactive              |
| created_at        | timestamp | Required                      |
| updated_at        | timestamp | Required                      |

---

Rules

- SLA is optional.
- SLA timers begin when the level becomes active.
- SLA may trigger reminders and escalations.

---

## 9.5 workflow_snapshots

Stores a snapshot of the workflow definition used when a workflow instance starts.

```text
workflow_snapshots
```

| Column               | Type      | Notes                            |
| -------------------- | --------- | -------------------------------- |
| id                   | uuid      | Primary key                      |
| workflow_instance_id | uuid      | References workflow_instances.id |
| workflow_version_id  | uuid      | References workflow_versions.id  |
| snapshot             | jsonb     | Complete workflow definition     |
| created_at           | timestamp | Required                         |

---

Rules

- Snapshot preserves the exact workflow definition used.
- Historical workflow execution should never depend on later workflow edits.
- Snapshots simplify auditing and troubleshooting.

---

# 10. Database Constraints

The Workflow Engine must enforce:

- Foreign key integrity.
- Tenant isolation.
- Soft deletes where appropriate.
- UUID primary keys.
- Row Level Security.
- Audit logging.
- Immutable workflow history.

---

# 11. Recommended Indexes

Create indexes for:

- tenant_id
- workflow_id
- workflow_version_id
- process_id
- entity_type
- entity_id
- current_level_id
- assigned_to
- recipient_id
- status
- created_at

Composite indexes should be created for common queries such as:

- (tenant_id, status)
- (tenant_id, entity_type)
- (tenant_id, entity_id)
- (workflow_instance_id, created_at)

---

# 12. Database Design Principles

The Workflow Engine database should:

- Support unlimited workflow definitions.
- Support unlimited workflow versions.
- Support unlimited workflow levels.
- Support multiple approver types.
- Support configurable conditions.
- Support reusable business processes.
- Support complete audit history.
- Support future workflow enhancements without schema redesign.

The database should remain generic and independent of any specific business module.
