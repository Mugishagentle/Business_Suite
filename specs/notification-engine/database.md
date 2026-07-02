# Notification Engine Database Specification

Version: 1.0

Status: Draft

Module: Notification Engine

---

# 1. Purpose

This document defines the database structure for the Notification Engine.

The engine provides centralized management of business events, notification rules, templates, recipients, delivery queues, delivery attempts, user preferences, and notification history.

The design supports scalability, tenant isolation, configurable notification channels, and future communication providers.

---

# 2. Design Principles

The database shall be:

- Event-driven
- Multi-tenant
- Highly scalable
- Queue-based
- Channel-independent
- Template-driven
- Auditable
- Extensible

Business modules publish business events.

The Notification Engine determines how notifications are generated and delivered.

---

# 3. Core Tables

Version 1 consists of the following primary tables.

```text
notification_events
        │
        ▼
notification_rules
        │
        ▼
notifications
        │
        ├──────────────┐
        │              │
        ▼              ▼
notification_recipients
notification_deliveries

notifications
        │
        ▼
notification_history

notification_templates

notification_preferences
```

Future versions may introduce:

- notification_campaigns
- notification_provider_logs
- notification_batches
- notification_schedules

---

# 4. Table Responsibilities

## notification_events

Stores published business events.

Examples:

- Invoice Created
- Payment Received
- Workflow Approved
- Leave Approved

---

## notification_rules

Defines how events are translated into notifications.

Contains:

- Event
- Channel
- Template
- Recipient Resolution
- Status

---

## notifications

Represents generated notifications.

Contains:

- Subject
- Message
- Status
- Priority
- Tenant
- Event Reference

---

## notification_recipients

Stores recipients for each notification.

Supports:

- Multiple recipients
- Different recipient types
- Delivery tracking

---

## notification_deliveries

Stores delivery attempts.

Examples:

- Email Attempt
- SMS Attempt
- In-App Delivery

---

## notification_templates

Stores reusable notification templates.

Supports:

- Variables
- Languages
- Branding
- Channel-specific formatting

---

## notification_preferences

Stores user notification preferences.

Supports:

- Channel preferences
- Opt-in/Opt-out
- Quiet hours (Future)

---

# 5. Database Tables

## 5.1 notification_events

Stores business events published by platform services and business modules.

```text
notification_events
```

| Column       | Type      | Notes                       |
| ------------ | --------- | --------------------------- |
| id           | uuid      | Primary Key                 |
| tenant_id    | uuid      | Required                    |
| module_code  | text      | CRM, HR, Finance, Workflow  |
| event_code   | text      | Unique Business Event       |
| entity_type  | text      | Invoice, Customer, Employee |
| entity_id    | uuid      | Related Business Record     |
| payload      | jsonb     | Event Data                  |
| status       | text      | Pending, Processed, Failed  |
| created_at   | timestamp | Required                    |
| processed_at | timestamp | Optional                    |

Business Rules

- Business modules publish events.
- Events are immutable after publication.
- Events are processed asynchronously.

---

## 5.2 notification_rules

Defines how business events generate notifications.

```text
notification_rules
```

| Column             | Type      | Notes                         |
| ------------------ | --------- | ----------------------------- |
| id                 | uuid      | Primary Key                   |
| tenant_id          | uuid      | Nullable for Global Rules     |
| event_code         | text      | Business Event                |
| channel            | text      | Email, SMS, In-App            |
| template_id        | uuid      | Notification Template         |
| recipient_strategy | text      | User, Role, Workflow, Dynamic |
| priority           | text      | Low, Normal, High, Critical   |
| is_active          | boolean   | Default true                  |
| created_at         | timestamp | Required                      |

Business Rules

- Rules may be global or tenant-specific.
- Multiple rules may exist for the same event.
- Rules are evaluated in order.

---

## 5.3 notifications

Represents generated notifications.

```text
notifications
```

| Column       | Type      | Notes                                |
| ------------ | --------- | ------------------------------------ |
| id           | uuid      | Primary Key                          |
| tenant_id    | uuid      | Required                             |
| event_id     | uuid      | References notification_events.id    |
| template_id  | uuid      | References notification_templates.id |
| subject      | text      | Generated Subject                    |
| message      | text      | Generated Message                    |
| channel      | text      | Delivery Channel                     |
| priority     | text      | Delivery Priority                    |
| status       | text      | Queued, Sending, Delivered, Failed   |
| scheduled_at | timestamp | Optional                             |
| created_at   | timestamp | Required                             |

Business Rules

- One event may create multiple notifications.
- Notification content is generated from templates.
- Notifications are immutable after creation.

---

## 5.4 notification_recipients

Stores recipients for generated notifications.

```text
notification_recipients
```

| Column          | Type      | Notes                       |
| --------------- | --------- | --------------------------- |
| id              | uuid      | Primary Key                 |
| notification_id | uuid      | References notifications.id |
| recipient_type  | text      | User, Email, Phone, Role    |
| recipient_id    | uuid      | Nullable                    |
| recipient_value | text      | Email or Phone              |
| status          | text      | Pending, Delivered, Failed  |
| created_at      | timestamp | Required                    |

Business Rules

- One notification may have multiple recipients.
- Recipient resolution occurs before delivery.

---

## 5.5 notification_deliveries

Stores delivery attempts.

```text
notification_deliveries
```

| Column             | Type      | Notes                       |
| ------------------ | --------- | --------------------------- |
| id                 | uuid      | Primary Key                 |
| notification_id    | uuid      | References notifications.id |
| provider           | text      | SMTP, Twilio, Firebase      |
| channel            | text      | Email, SMS, In-App          |
| attempt_number     | integer   | Starts at 1                 |
| status             | text      | Sent, Delivered, Failed     |
| provider_reference | text      | Optional                    |
| error_message      | text      | Optional                    |
| attempted_at       | timestamp | Required                    |
| delivered_at       | timestamp | Optional                    |

Business Rules

- Every delivery attempt is recorded.
- Retries create additional delivery records.
- Delivery history is immutable.

---

## 5.6 notification_templates

Stores reusable notification templates.

```text
notification_templates
```

| Column        | Type      | Notes                         |
| ------------- | --------- | ----------------------------- |
| id            | uuid      | Primary Key                   |
| tenant_id     | uuid      | Nullable for Global Templates |
| channel       | text      | Email, SMS, In-App            |
| template_code | text      | Unique Code                   |
| language      | text      | ISO Language Code             |
| subject       | text      | Optional                      |
| body          | text      | Required                      |
| is_active     | boolean   | Default true                  |
| created_at    | timestamp | Required                      |

Business Rules

- Templates support placeholders.
- Templates may be global or tenant-specific.
- Templates should be versioned in future.

---

## 5.7 notification_preferences

Stores user notification preferences.

```text
notification_preferences
```

| Column     | Type      | Notes              |
| ---------- | --------- | ------------------ |
| id         | uuid      | Primary Key        |
| tenant_id  | uuid      | Required           |
| user_id    | uuid      | Platform User      |
| channel    | text      | Email, SMS, In-App |
| enabled    | boolean   | Default true       |
| updated_at | timestamp | Required           |

Business Rules

- Preferences are user-specific.
- Security notifications may bypass preferences.
- Preferences apply only within the tenant.

---

# 6. Reference Data Usage

The Notification Engine should use the Reference Data Engine for configurable values.

Examples include:

- Notification Channels
- Notification Status
- Notification Priority
- Recipient Types
- Delivery Status
- Event Categories

Examples:

| Reference Group       | Example Values                   |
| --------------------- | -------------------------------- |
| Notification Channels | Email, SMS, In-App               |
| Notification Priority | Low, Normal, High, Critical      |
| Delivery Status       | Pending, Sent, Delivered, Failed |
| Recipient Types       | User, Role, Email, Phone         |

This prevents hardcoded notification values.

---

# 7. Constraints

The following constraints should be enforced.

## Notification Templates

```sql
UNIQUE (tenant_id, template_code, channel, language)
```

## Notification Preferences

```sql
UNIQUE (tenant_id, user_id, channel)
```

## Notification Rules

```sql
UNIQUE (tenant_id, event_code, channel, template_id)
```

---

# 8. Indexing

Recommended indexes:

- tenant_id
- event_code
- module_code
- entity_type
- entity_id
- channel
- priority
- status
- created_at
- scheduled_at

Composite indexes:

```text
(tenant_id, event_code)

(tenant_id, status)

(tenant_id, channel, status)

(priority, status, scheduled_at)

(notification_id, status)

(user_id, channel)
```

These indexes optimize:

- Event processing
- Queue processing
- Delivery retries
- Notification history
- User notification preferences

---

# 9. Row Level Security

The Notification Engine must enforce tenant isolation.

Rules:

- Users may only view notifications belonging to their tenant.
- Users may only view notifications addressed to them unless they have administrative permissions.
- Tenant administrators may view tenant notification history.
- Super Administrators may manage global templates and rules.

RLS must apply to:

- notification_events
- notification_rules
- notifications
- notification_recipients
- notification_deliveries
- notification_templates
- notification_preferences

---

# 10. Delivery Queue Rules

Notifications should be processed using queue-style behavior.

Processing order should consider:

1. Priority
2. Scheduled Time
3. Created Date

Priority order:

```text
Critical

High

Normal

Low
```

Failed deliveries may be retried according to retry policy.

---

# 11. Retry Rules

Delivery retries should be configurable.

Rules:

- Failed deliveries should be retried.
- Retry count should be limited.
- Retry interval should be configurable.
- Failed final attempts should be marked permanently failed.
- All attempts must be recorded.

---

# 12. Seed Data

Default Reference Data should include:

## Notification Channels

- In-App
- Email
- SMS

## Notification Priorities

- Low
- Normal
- High
- Critical

## Delivery Status

- Pending
- Sent
- Delivered
- Failed

## Recipient Types

- User
- Role
- Email
- Phone
- Permission
- Dynamic

Default templates should include:

- User Invitation
- Password Reset
- Email Verification
- Trial Started
- Trial Expiring
- Workflow Approval Assigned
- Workflow Approved
- Workflow Rejected

---

# 13. Implementation Rules

The database implementation must follow:

- UUID primary keys
- Foreign key constraints
- Tenant isolation
- Row Level Security
- Immutable delivery history
- Service Layer access only
- Queue-based delivery processing

Business modules must never write directly to notification delivery tables.

---

# 14. Conclusion

The Notification Engine database provides a scalable, event-driven, and tenant-aware structure for managing notifications across Business Suite.

By separating events, rules, templates, recipients, deliveries, preferences, and history, the platform supports consistent communication, delivery tracking, retries, auditing, and future channels without redesign.
