# Platform Activity & Audit Engine Database Specification

Version: 1.0

Status: Approved

Module: Platform Activity & Audit Engine

---

# 1. Purpose

This document defines the database structure for the Platform Activity & Audit Engine.

The engine provides centralized storage for business activity, compliance audit records, security events, timelines, and change history across Business Suite.

The design supports tenant isolation, immutable audit records, correlation tracing, and enterprise compliance requirements.

---

# 2. Design Principles

The database shall be:

- Event-driven
- Immutable
- Tenant-aware
- Highly scalable
- Searchable
- Auditable
- Extensible

Business modules publish events through the Platform Event Bus.

The Platform Activity & Audit Engine records those events.

---

# 3. Core Tables

Version 1 consists of the following primary tables.

```text
activity_events
        │
        ├──────────────┐
        │              │
        ▼              ▼
audit_events     activity_timelines
        │
        ▼
audit_changes

security_events

system_events
```

Future versions may introduce:

- compliance_cases
- legal_hold
- audit_signatures
- audit_replay

---

# 4. Table Responsibilities

## activity_events

Stores business activity.

Supports:

- User Timeline
- Entity Timeline
- Recent Activity

---

## audit_events

Stores immutable audit records.

Supports:

- Compliance
- Investigations
- Reporting

---

## audit_changes

Stores detailed field-level changes.

Supports:

- Before Values
- After Values
- Field History

---

## security_events

Stores security-related activity.

Supports:

- Security Monitoring
- Threat Investigation

---

## system_events

Stores operational platform events.

Supports:

- Troubleshooting
- Platform Monitoring

---

## activity_timelines

Provides optimized timeline views for users and entities.

Supports:

- Customer Timeline
- Employee Timeline
- Invoice Timeline
- Workflow Timeline
- Document Timeline

---

# 5. Database Tables

## 5.1 audit_entities

Defines the catalog of auditable entity types.

```text
audit_entities
```

| Column      | Type      | Notes              |
| ----------- | --------- | ------------------ |
| id          | uuid      | Primary Key        |
| entity_code | text      | Unique Entity Code |
| entity_name | text      | Display Name       |
| module_code | text      | Owning Module      |
| description | text      | Optional           |
| is_active   | boolean   | Default true       |
| created_at  | timestamp | Required           |

### Business Rules

- Entity codes must be unique.
- Every audit event must reference a registered audit entity.
- Deprecated entities should be marked inactive rather than deleted.

---

## 5.2 activity_events

Stores business activity.

```text
activity_events
```

| Column          | Type      | Notes                            |
| --------------- | --------- | -------------------------------- |
| id              | uuid      | Primary Key                      |
| tenant_id       | uuid      | Required                         |
| audit_entity_id | uuid      | References audit_entities.id     |
| entity_id       | uuid      | Business Record                  |
| activity_type   | text      | Created, Updated, Approved, etc. |
| title           | text      | Timeline Title                   |
| description     | text      | Optional                         |
| performed_by    | uuid      | User or Service                  |
| correlation_id  | uuid      | Platform Correlation ID          |
| metadata        | jsonb     | Optional                         |
| occurred_at     | timestamp | Required                         |

### Business Rules

- Activity events support user and entity timelines.
- Activity events are append-only.
- Activity events preserve Correlation IDs.

---

## 5.3 audit_events

Stores immutable compliance audit records.

```text
audit_events
```

| Column          | Type      | Notes                                      |
| --------------- | --------- | ------------------------------------------ |
| id              | uuid      | Primary Key                                |
| tenant_id       | uuid      | Required                                   |
| audit_entity_id | uuid      | References audit_entities.id               |
| entity_id       | uuid      | Business Record                            |
| event_type      | text      | Create, Update, Delete, Login, etc.        |
| classification  | text      | Public, Internal, Confidential, Restricted |
| performed_by    | uuid      | User or Service                            |
| correlation_id  | uuid      | Platform Correlation ID                    |
| ip_address      | text      | Optional                                   |
| user_agent      | text      | Optional                                   |
| created_at      | timestamp | Required                                   |

### Business Rules

- Audit events are immutable.
- Audit events support compliance reporting.
- Audit events preserve Correlation IDs.

---

## 5.4 audit_changes

Stores detailed field-level changes.

```text
audit_changes
```

| Column         | Type      | Notes                      |
| -------------- | --------- | -------------------------- |
| id             | uuid      | Primary Key                |
| audit_event_id | uuid      | References audit_events.id |
| field_name     | text      | Updated Field              |
| old_value      | text      | Optional                   |
| new_value      | text      | Optional                   |
| data_type      | text      | String, Number, Date, JSON |
| created_at     | timestamp | Required                   |

### Business Rules

- One audit event may contain many field changes.
- Field changes are immutable.
- Large values may be truncated or stored securely according to platform policy.

---

---

## 5.5 audit_snapshots

Stores point-in-time snapshots of important business records.

```text
audit_snapshots
```

| Column          | Type      | Notes                                |
| --------------- | --------- | ------------------------------------ |
| id              | uuid      | Primary Key                          |
| tenant_id       | uuid      | Required                             |
| audit_entity_id | uuid      | References audit_entities.id         |
| entity_id       | uuid      | Business Record                      |
| audit_event_id  | uuid      | Optional, references audit_events.id |
| snapshot_data   | jsonb     | Record state at point in time        |
| snapshot_reason | text      | Optional                             |
| created_by      | uuid      | User or Service                      |
| created_at      | timestamp | Required                             |

### Business Rules

- Snapshots are optional.
- Snapshots are immutable.
- Snapshots should be enabled only for selected entities.
- High-value entities may require snapshots.
- Snapshot data should respect security and retention policies.

### Example Use Cases

- Invoice approval snapshot
- Journal posting snapshot
- Payroll processing snapshot
- Workflow completion snapshot
- Contract signing snapshot

## 5.6 security_events

Stores security-related events.

```text
security_events
```

| Column         | Type      | Notes                         |
| -------------- | --------- | ----------------------------- |
| id             | uuid      | Primary Key                   |
| tenant_id      | uuid      | Required                      |
| event_type     | text      | LoginFailed, MFAEnabled, etc. |
| severity       | text      | Low, Medium, High, Critical   |
| user_id        | uuid      | Optional                      |
| ip_address     | text      | Optional                      |
| correlation_id | uuid      | Platform Correlation ID       |
| details        | jsonb     | Optional                      |
| occurred_at    | timestamp | Required                      |

### Business Rules

- Security events are immutable.
- High-severity events should support alerting.
- Security events remain tenant-aware where applicable.

---

## 5.7 system_events

Stores operational platform events.

```text
system_events
```

| Column         | Type      | Notes                           |
| -------------- | --------- | ------------------------------- |
| id             | uuid      | Primary Key                     |
| event_source   | text      | Platform Service or Module      |
| event_type     | text      | JobCompleted, QueueFailed, etc. |
| severity       | text      | Info, Warning, Error            |
| correlation_id | uuid      | Platform Correlation ID         |
| details        | jsonb     | Optional                        |
| occurred_at    | timestamp | Required                        |

### Business Rules

- System events support troubleshooting.
- System events may originate outside tenant context.
- Correlation IDs should be preserved.

---

## 5.8 activity_timelines

Provides optimized timeline entries.

```text
activity_timelines
```

| Column            | Type      | Notes                         |
| ----------------- | --------- | ----------------------------- |
| id                | uuid      | Primary Key                   |
| tenant_id         | uuid      | Required                      |
| audit_entity_id   | uuid      | References audit_entities.id  |
| entity_id         | uuid      | Business Record               |
| activity_event_id | uuid      | References activity_events.id |
| timeline_type     | text      | Entity, User, System          |
| created_at        | timestamp | Required                      |

### Business Rules

- Timeline entries reference Activity Events.
- Timelines are append-only.
- Timeline generation may be asynchronous.

---

# 6. Reference Data Usage

The Platform Activity & Audit Engine should use the Reference Data Engine for configurable values.

Examples include:

- Activity Types
- Audit Event Types
- Security Event Types
- System Event Types
- Severity Levels
- Audit Classifications
- Timeline Types
- Snapshot Reasons

Examples:

| Reference Group   | Example Values                             |
| ----------------- | ------------------------------------------ |
| Activity Types    | Created, Updated, Approved, Rejected       |
| Audit Event Types | Create, Update, Delete, Login              |
| Severity          | Info, Low, Medium, High, Critical          |
| Timeline Types    | Entity, User, System                       |
| Classification    | Public, Internal, Confidential, Restricted |

This prevents hardcoded audit values.

---

# 7. Constraints

The following constraints should be enforced.

## Audit Entities

```sql
UNIQUE (entity_code)
```

## Activity Timelines

```sql
UNIQUE (activity_event_id, timeline_type)
```

---

# 8. Indexing

Recommended indexes:

- tenant_id
- audit_entity_id
- entity_id
- performed_by
- correlation_id
- classification
- occurred_at
- created_at

Composite indexes:

```text
(tenant_id, audit_entity_id)

(audit_entity_id, entity_id)

(performed_by, occurred_at)

(correlation_id)

(event_type, created_at)

(severity, occurred_at)
```

These indexes optimize:

- Entity timelines
- User timelines
- Compliance searches
- Security investigations
- Correlation tracing

---

# 9. Row Level Security

The Platform Activity & Audit Engine must enforce tenant isolation.

Rules:

- Users may access audit records only within their active tenant.
- Activity history must remain tenant-aware.
- Security events may require elevated permissions.
- System events may exist outside tenant context where appropriate.

RLS must apply to:

- activity_events
- audit_events
- audit_changes
- audit_snapshots
- security_events
- activity_timelines

Global configuration tables such as `audit_entities` may be excluded from tenant filtering.

---

# 10. Platform Event Bus Integration

The Platform Activity & Audit Engine records activity through the Platform Event Bus.

Typical events include:

- CustomerCreated
- CustomerUpdated
- InvoiceApproved
- PaymentReceived
- DocumentUploaded
- WorkflowCompleted
- LoginFailed

Business Rules

- Business modules publish events.
- The Audit Engine subscribes to relevant events.
- Correlation IDs must be preserved.
- Audit records are generated asynchronously.

---

# 11. Data Retention

Audit data follows configurable retention policies.

Rules:

- Activity records follow tenant retention policies.
- Audit records remain immutable.
- Security events may require extended retention.
- Expired records should be archived before permanent removal where permitted.
- Legal hold policies override normal retention rules.

Retention policies should be configurable by entity type and event category.

---

# 12. Processing Rules

The Audit Engine shall follow these principles.

- Audit records are immutable.
- Activity records are append-only.
- Field changes are linked to audit events.
- Snapshots are optional.
- Timeline generation may be asynchronous.
- Correlation IDs are preserved.

---

# 13. Seed Data

Default Activity Types:

- Created
- Updated
- Approved
- Rejected
- Deleted
- Viewed

Default Severity Levels:

- Info
- Low
- Medium
- High
- Critical

Default Timeline Types:

- Entity
- User
- System

---

# 14. Implementation Rules

The database implementation shall follow:

- UUID Primary Keys
- Foreign Key Constraints
- Tenant Isolation
- Row Level Security
- Immutable Audit Records
- Event-Driven Processing
- Service Layer Architecture

Business modules must never write directly to audit tables.

All audit records shall originate through the Platform Event Bus.

---

# 15. Conclusion

The Platform Activity & Audit Engine database provides a centralized, immutable, and event-driven audit foundation for Business Suite.

By separating activity events, audit events, field changes, snapshots, security events, system events, and timelines while integrating with the Platform Event Bus, the platform delivers enterprise-grade traceability, compliance, and operational visibility across all services and business modules.
