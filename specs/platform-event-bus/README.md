# Platform Event Bus Specification

Version: 1.0  
Status: Draft  
Module: Platform Event Bus

---

# 1. Purpose

The Platform Event Bus is a shared platform service responsible for publishing, routing, processing, and tracking business events across Business Suite.

It allows platform services and business modules to communicate without being tightly coupled.

---

# 2. Core Principle

Business modules and platform engines should not call each other directly when reacting to business events.

Instead, they publish events to the Platform Event Bus.

Interested services subscribe to those events.

Example:

```text
Invoice Created

↓

Platform Event Bus

├── Notification Engine
├── Search Engine
├── Audit Engine
└── Reporting Engine
```

---

# 3. Objectives

The Platform Event Bus aims to:

- Decouple platform services.
- Decouple business modules.
- Standardize business event publishing.
- Support event subscriptions.
- Support event routing.
- Support event retry.
- Support event history.
- Support tenant-aware events.
- Support future event replay.

---

# 4. Scope

The Platform Event Bus includes:

- Event Publishing
- Event Subscriptions
- Event Routing
- Event Processing
- Event History
- Retry Handling
- Failed Event Handling
- Dead Letter Queue
- Event Payloads
- Event Metadata

The Platform Event Bus serves as the communication backbone for all platform services and business modules.

---

# 5. Out of Scope

The Platform Event Bus does not:

- Contain business logic.
- Execute business workflows.
- Modify business data.
- Generate notifications.
- Build search indexes directly.
- Create audit records directly.
- Generate reports.

The Event Bus is responsible only for receiving, storing, routing, and tracking events.

Each subscribing engine is responsible for deciding how to process an event.

---

# 6. Core Concepts

The Platform Event Bus is built around the following concepts:

- Event
- Publisher
- Subscriber
- Event Handler
- Event Payload
- Event Metadata
- Event Subscription
- Retry
- Dead Letter Queue
- Event History

---

# 7. Event

An Event represents something important that has already occurred within Business Suite.

Events communicate facts between platform services and business modules.

Examples:

- Tenant Created
- User Invited
- Customer Created
- Invoice Created
- Payment Received
- Workflow Approved
- Document Uploaded
- Stock Updated

Events should be named using the **Past Tense**.

Examples:

```text
CustomerCreated

InvoiceCreated

PaymentReceived

WorkflowApproved

DocumentUploaded
```

Events are immutable after publication.

---

# 8. Event Categories

Every event belongs to a category.

Supported Version 1 categories include:

- Platform
- Business
- Workflow
- Document
- Security
- System
- Integration

Examples:

| Category    | Event            |
| ----------- | ---------------- |
| Platform    | TenantCreated    |
| Business    | InvoiceCreated   |
| Workflow    | WorkflowApproved |
| Document    | DocumentUploaded |
| Security    | PasswordChanged  |
| System      | JobCompleted     |
| Integration | WebhookReceived  |

Categories improve routing, monitoring, reporting, and administration.

---

# 9. Publisher

A Publisher is a platform service or business module that publishes an event.

Examples:

Platform Services:

- Platform Core
- Workflow Engine
- Notification Engine
- Search Engine
- Reporting Engine

Business Modules:

- CRM
- Sales
- Inventory
- Procurement
- Finance
- HR
- POS

Publishers publish events without knowing which subscribers will consume them.

This ensures loose coupling.

---

# 10. Subscriber

A Subscriber listens for one or more event types.

Examples:

```text
InvoiceCreated

↓

Notification Engine

↓

Search Engine

↓

Audit Engine
```

Subscribers process events independently.

One subscriber failing must not prevent other subscribers from processing the same event.

---

# 11. Event Handler

Each subscriber processes events using one or more Event Handlers.

Example:

```text
InvoiceCreated

↓

Notification Handler

↓

Send Customer Notification
```

Another example:

```text
InvoiceCreated

↓

Search Handler

↓

Update Search Index
```

Event Handlers should:

- Process one responsibility.
- Be idempotent.
- Be independently testable.
- Handle failures gracefully.

---

# 12. Event Payload

The Event Payload contains business information required by subscribers.

Example:

```json
{
  "invoice_id": "uuid",
  "invoice_number": "INV-2026-000145",
  "customer_id": "uuid",
  "tenant_id": "uuid",
  "amount": 2500000
}
```

Payloads should:

- Contain only required information.
- Avoid sensitive data where unnecessary.
- Be version-compatible.
- Be serializable.

Subscribers should not depend on fields outside the published event contract.

---

# 13. Event Metadata

Every published event shall include metadata.

Metadata should include:

- Event ID
- Event Code
- Event Category
- Tenant ID
- Module Code
- Entity Type
- Entity ID
- Correlation ID
- Published By
- Published At
- Event Version

Metadata supports:

- Routing
- Auditing
- Monitoring
- Troubleshooting
- Event tracing

---

# 14. Event Subscription

An Event Subscription defines which subscribers receive which events.

Example:

```text
InvoiceCreated

↓

Subscribers

• Notification Engine
• Search Engine
• Audit Engine
• Reporting Engine
```

Business Rules

- One event may have many subscribers.
- Subscribers may listen to many events.
- Subscriptions should be configurable.
- Disabled subscriptions should not receive events.

---

# 15. Event Routing

The Platform Event Bus routes events to the appropriate subscribers.

Process:

```text
Publish Event

↓

Identify Subscribers

↓

Queue Deliveries

↓

Process Deliveries

↓

Record Results
```

Business Rules

- Routing should be asynchronous.
- Subscriber processing should be independent.
- One subscriber failure must not affect others.

---

# 16. Event Retry

Failed event deliveries should be retried.

Retry policy may define:

- Maximum Attempts
- Retry Interval
- Backoff Strategy

Business Rules

- Retries apply per subscriber.
- Retry history must be recorded.
- Retry limits should be configurable.

---

# 17. Dead Letter Queue

Events that cannot be processed successfully shall be moved to the Dead Letter Queue.

The Dead Letter Queue stores:

- Event
- Subscriber
- Failure Reason
- Retry Count
- Last Attempt

Administrators should be able to:

- View failed deliveries.
- Retry processing.
- Mark as resolved.
- Export failure details.

---

# 18. Event History

Every event shall maintain a complete processing history.

History includes:

- Event Published
- Subscriber Processing
- Retry Attempts
- Processing Success
- Processing Failure
- Resolution

History supports:

- Audit
- Monitoring
- Troubleshooting
- Performance Analysis

History must be immutable.

---

# 19. Integration

Platform services and business modules communicate through the Platform Event Bus.

Integration flow:

```text
Business Module

↓

Publish Event

↓

Platform Event Bus

↓

Subscribed Services

↓

Independent Processing
```

Direct service-to-service event communication should be avoided wherever possible.

---

# 20. Future Enhancements

Future versions may support:

- Event Replay
- Scheduled Events
- Event Versioning
- Event Filtering
- Event Priorities
- Distributed Event Processing
- Event Streaming
- External Event Connectors
- Webhook Publishing
- Kafka / RabbitMQ Integration

These enhancements should integrate without requiring redesign of the event architecture.

---

# 21. Implementation Rules

The Platform Event Bus shall comply with:

- docs/architecture/Architecture.md
- docs/architecture/CodingStandards.md
- docs/architecture/DesignLanguage.md
- specs/platform-core/security.md

Implementation requirements:

- React + TypeScript (Administration UI)
- PostgreSQL
- Supabase
- UUID Primary Keys
- Service Layer Architecture
- API-First Design
- Queue-based Processing
- Tenant Isolation
- Immutable Event History

---

# 22. Success Criteria

The Platform Event Bus is considered complete when:

- Events can be published.
- Events are routed correctly.
- Subscribers receive subscribed events.
- Retry policies function correctly.
- Failed deliveries enter the Dead Letter Queue.
- Event history is maintained.
- Tenant isolation is enforced.
- Services remain loosely coupled.

---

# 23. Conclusion

The Platform Event Bus provides the communication backbone of Business Suite.

By separating publishers from subscribers and centralizing event routing, retry handling, and event history, the platform enables scalable, loosely coupled, and event-driven communication across all current and future platform services and business modules.
