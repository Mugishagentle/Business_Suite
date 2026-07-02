# Platform Event Bus Database Specification

Version: 1.0

Status: Draft

Module: Platform Event Bus

---

# 1. Purpose

This document defines the database structure for the Platform Event Bus.

The Event Bus provides centralized management of business events, event subscriptions, event deliveries, retry handling, dead letter queues, and event history.

The design supports tenant isolation, loose coupling, asynchronous processing, auditing, and future distributed event processing.

---

# 2. Design Principles

The database shall be:

- Event-driven
- Multi-tenant
- Queue-based
- Subscriber-independent
- Highly scalable
- Auditable
- Extensible

Business modules publish events.

Subscribers process events independently.

---

# 3. Core Tables

Version 1 consists of the following primary tables.

```text
platform_events
        │
        ├──────────────┐
        │              │
        ▼              ▼
event_deliveries   event_subscriptions

platform_events
        │
        ▼
event_history

dead_letter_queue
```

Future versions may introduce:

- event_replays
- event_streams
- event_versions
- external_event_connectors

---

# 4. Table Responsibilities

## platform_events

Stores published business events.

Contains:

- Event metadata
- Payload
- Publisher
- Tenant
- Correlation information

---

## event_subscriptions

Defines which subscribers receive which events.

Contains:

- Event Code
- Subscriber
- Status
- Processing Rules

---

## event_deliveries

Stores delivery attempts for every subscriber.

Contains:

- Subscriber
- Status
- Retry Count
- Processing Time
- Error Information

---

## event_history

Stores the complete lifecycle of every event.

Supports:

- Audit
- Monitoring
- Troubleshooting
- Performance Analysis

---

## dead_letter_queue

Stores permanently failed event deliveries.

Supports:

- Manual Retry
- Investigation
- Export
- Resolution

---

# 5. Database Tables

## 5.1 event_types

Defines the catalog of supported business events.

```text
event_types
```

| Column      | Type      | Notes                              |
| ----------- | --------- | ---------------------------------- |
| id          | uuid      | Primary Key                        |
| event_code  | text      | Unique Event Code                  |
| event_name  | text      | Display Name                       |
| category    | text      | Platform, Business, Workflow, etc. |
| publisher   | text      | Owning Module or Engine            |
| description | text      | Optional                           |
| version     | integer   | Default 1                          |
| is_active   | boolean   | Default true                       |
| created_at  | timestamp | Required                           |

### Business Rules

- Event codes must be unique.
- Every published event must exist in the event catalog.
- Event codes should remain stable.
- Deprecated events should be marked inactive rather than deleted.

---

## 5.2 platform_events

Stores published business events.

```text
platform_events
```

| Column         | Type      | Notes                        |
| -------------- | --------- | ---------------------------- |
| id             | uuid      | Primary Key                  |
| tenant_id      | uuid      | Required                     |
| event_type_id  | uuid      | References event_types.id    |
| entity_type    | text      | Customer, Invoice, Employee  |
| entity_id      | uuid      | Business Record              |
| correlation_id | uuid      | Distributed Trace Identifier |
| payload        | jsonb     | Event Payload                |
| published_by   | uuid      | User or Service              |
| published_at   | timestamp | Required                     |

### Business Rules

- Events are immutable.
- Events reference registered event types.
- Payloads should be serializable.
- Events should not contain unnecessary sensitive information.

---

## 5.3 event_subscriptions

Defines subscriber registrations.

```text
event_subscriptions
```

| Column        | Type      | Notes                     |
| ------------- | --------- | ------------------------- |
| id            | uuid      | Primary Key               |
| event_type_id | uuid      | References event_types.id |
| subscriber    | text      | Engine or Module          |
| priority      | integer   | Processing Order          |
| is_active     | boolean   | Default true              |
| created_at    | timestamp | Required                  |

### Business Rules

- One event may have many subscribers.
- Subscribers may subscribe to many events.
- Disabled subscriptions receive no deliveries.

---

## 5.4 event_deliveries

Stores delivery attempts.

```text
event_deliveries
```

| Column             | Type      | Notes                                |
| ------------------ | --------- | ------------------------------------ |
| id                 | uuid      | Primary Key                          |
| event_id           | uuid      | References platform_events.id        |
| subscriber         | text      | Processing Subscriber                |
| status             | text      | Pending, Processing, Success, Failed |
| retry_count        | integer   | Default 0                            |
| processing_time_ms | integer   | Duration                             |
| error_message      | text      | Optional                             |
| processed_at       | timestamp | Optional                             |

### Business Rules

- Each subscriber receives its own delivery record.
- Subscriber failures are independent.
- Retry count applies per subscriber.
- Delivery history is immutable.

---

## 5.5 event_history

Stores lifecycle history for published events.

```text
event_history
```

| Column     | Type      | Notes                                 |
| ---------- | --------- | ------------------------------------- |
| id         | uuid      | Primary Key                           |
| event_id   | uuid      | References platform_events.id         |
| action     | text      | Published, Delivered, Retried, Failed |
| subscriber | text      | Optional                              |
| details    | jsonb     | Optional                              |
| created_at | timestamp | Required                              |

### Business Rules

- Every significant event action is recorded.
- History is immutable.
- History supports audit and troubleshooting.

---

## 5.6 dead_letter_queue

Stores permanently failed event deliveries.

```text
dead_letter_queue
```

| Column            | Type      | Notes                          |
| ----------------- | --------- | ------------------------------ |
| id                | uuid      | Primary Key                    |
| event_delivery_id | uuid      | References event_deliveries.id |
| failure_reason    | text      | Required                       |
| retry_attempts    | integer   | Final Retry Count              |
| resolved          | boolean   | Default false                  |
| resolved_by       | uuid      | Optional                       |
| resolved_at       | timestamp | Optional                       |
| created_at        | timestamp | Required                       |

### Business Rules

- Only permanently failed deliveries enter the Dead Letter Queue.
- Events remain available for investigation.
- Administrators may retry or resolve failed deliveries.
- Resolution history should be audited.

---

## 5.2 event_subscribers

Defines registered event subscribers.

```text
event_subscribers
```

| Column          | Type      | Notes                      |
| --------------- | --------- | -------------------------- |
| id              | uuid      | Primary Key                |
| subscriber_code | text      | Unique Subscriber Code     |
| subscriber_name | text      | Display Name               |
| subscriber_type | text      | platform, module, external |
| description     | text      | Optional                   |
| is_active       | boolean   | Default true               |
| created_at      | timestamp | Required                   |

### Business Rules

- Subscriber codes must be unique.
- Only active subscribers receive event deliveries.
- Subscribers should be registered before they can subscribe to events.
- External subscribers may be supported in future versions.

---

## Updated Table Numbering

After adding `event_subscribers`, renumber the remaining tables as follows:

```text
5.1 event_types
5.2 event_subscribers
5.3 platform_events
5.4 event_subscriptions
5.5 event_deliveries
5.6 event_history
5.7 dead_letter_queue
```

Also update these references:

```markdown
event_subscriptions.subscriber_id → References event_subscribers.id
event_deliveries.subscriber_id → References event_subscribers.id
event_history.subscriber_id → Optional, references event_subscribers.id
```

---

# 6. Reference Data Usage

The Platform Event Bus should use the Reference Data Engine for configurable values.

Examples include:

- Event Categories
- Event Status
- Delivery Status
- Subscriber Types
- Retry Strategies

Examples:

| Reference Group  | Example Values                                   |
| ---------------- | ------------------------------------------------ |
| Event Categories | Platform, Business, Workflow, Document, Security |
| Event Status     | Published, Archived                              |
| Delivery Status  | Pending, Processing, Success, Failed             |
| Subscriber Types | Platform, Module, External                       |
| Retry Strategy   | Fixed Interval, Exponential Backoff              |

This prevents hardcoded event values.

---

# 7. Constraints

The following constraints should be enforced.

## Event Types

```sql
UNIQUE (event_code)
```

## Event Subscribers

```sql
UNIQUE (subscriber_code)
```

## Event Subscriptions

```sql
UNIQUE (event_type_id, subscriber_id)
```

---

# 8. Indexing

Recommended indexes:

- tenant_id
- event_type_id
- entity_type
- entity_id
- correlation_id
- published_at
- status

Composite indexes:

```text
(tenant_id, published_at)

(event_type_id, published_at)

(event_id, subscriber_id)

(status, retry_count)

(correlation_id)
```

These indexes optimize:

- Event routing
- Event tracing
- Retry processing
- Monitoring
- Troubleshooting

---

# 9. Row Level Security

The Platform Event Bus must enforce tenant isolation.

Rules:

- Platform services may access events they are authorized to process.
- Tenant users may only access events belonging to their tenant where permitted.
- Event history must remain tenant isolated.
- Dead Letter Queue access should be restricted to administrators.

RLS must apply to:

- platform_events
- event_deliveries
- event_history
- dead_letter_queue

Global configuration tables such as `event_types` and `event_subscribers` may be excluded from tenant filtering.

---

# 10. Retry Rules

Failed deliveries shall follow configurable retry policies.

Rules:

- Retries apply per subscriber.
- Retry count is limited.
- Retry intervals are configurable.
- Retry history must be retained.
- Permanent failures are moved to the Dead Letter Queue.

Retry processing should be asynchronous.

---

# 11. Event Processing Rules

Event processing shall follow these principles.

- Events are immutable.
- Deliveries are independent.
- Subscribers process events asynchronously.
- Subscriber failures must not block other subscribers.
- Duplicate event processing should be prevented.
- Event ordering should be preserved where required.

Subscribers should implement idempotent processing.

---

# 12. Seed Data

Default Event Categories:

- Platform
- Business
- Workflow
- Document
- Security
- System
- Integration

Default Subscriber Types:

- Platform
- Module
- External

Default Delivery Status:

- Pending
- Processing
- Success
- Failed

---

# 13. Implementation Rules

The database implementation shall follow:

- UUID Primary Keys
- Foreign Key Constraints
- Tenant Isolation
- Row Level Security
- Queue-Based Processing
- Immutable Event History
- Service Layer Architecture

Business modules must publish events through the Platform Event Bus.

They must never write directly to event delivery tables.

---

# 14. Conclusion

The Platform Event Bus database provides a scalable, event-driven foundation for communication across Business Suite.

By separating event definitions, subscribers, published events, deliveries, history, and dead letter handling, the platform enables reliable, loosely coupled, and auditable communication between platform services and business modules while remaining extensible for future distributed architectures.

# Platform Event Bus User Interface Specification

Version: 1.0  
Status: Approved  
Module: Platform Event Bus
