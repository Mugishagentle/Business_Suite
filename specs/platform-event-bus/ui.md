# Platform Event Bus User Interface Specification

Version: 1.0  
Status: Approved  
Module: Platform Event Bus

---

# 1. Purpose

This document defines the user interface standards for the Platform Event Bus.

The UI allows authorized administrators to monitor events, manage event types, manage subscribers, view deliveries, inspect failures, and retry failed event processing.

---

# 2. Design Principles

The Platform Event Bus UI shall be:

- Simple
- Technical but understandable
- Fast
- Tenant-aware
- Permission-aware
- Audit-friendly
- Consistent with the Business Suite Design Language

The UI is primarily intended for administrators, developers, and support users.

---

# 3. Navigation

The Platform Event Bus should appear under:

````text
Platform Administration

↓

Event Bus


---

# 5. Event Types

The Event Types screen manages the platform event catalog.

Columns:

- Event Code
- Event Name
- Category
- Publisher
- Version
- Status

Toolbar:

- Search
- Filter
- Refresh
- Export
- Register Event

Row Actions:

- View
- Edit
- View Subscribers
- View Recent Events
- Activate
- Deactivate

Only authorized administrators may register or modify event types.

---

# 6. Event Subscribers

The Event Subscribers screen manages registered subscribers.

Columns:

- Subscriber Code
- Subscriber Name
- Subscriber Type
- Status
- Last Activity

Toolbar:

- Search
- Filter
- Refresh
- Register Subscriber

Row Actions:

- View
- Edit
- Enable
- Disable
- View Subscriptions
- View Delivery Statistics

Subscribers should display health and processing status where available.

---

# 7. Event Subscriptions

The Event Subscriptions screen defines which subscribers receive which events.

Columns:

- Event
- Subscriber
- Priority
- Status

Toolbar:

- Search
- Filter
- Refresh
- Create Subscription

Row Actions:

- View
- Edit
- Enable
- Disable

Subscriptions should clearly show processing order where multiple subscribers exist.

---

# 8. Published Events

The Published Events screen displays runtime event activity.

Columns:

- Event Code
- Category
- Publisher
- Entity
- Tenant
- Published At
- Overall Status

Filters:

- Event
- Category
- Publisher
- Tenant
- Date Range
- Status

Row Actions:

- View Details
- View Deliveries
- View History

Published events are read-only.

---

# 9. Event Details

The Event Details page displays complete information about a published event.

Sections:

## Event Information

- Event Code
- Category
- Publisher
- Entity Type
- Entity ID
- Correlation ID
- Published At

---

## Payload

Display the event payload in formatted JSON.

Sensitive fields should be masked where required.

---

## Subscribers

Display:

- Subscriber
- Status
- Retry Count
- Processing Time
- Last Attempt

---

## History

Display complete processing history for the event.

History is read-only.

---

# 10. Event Deliveries

The Event Deliveries screen displays subscriber processing.

Columns:

- Event
- Subscriber
- Status
- Retry Count
- Processing Time
- Last Attempt

Filters:

- Subscriber
- Status
- Event
- Date Range

Row Actions:

- View
- Retry
- View Error

Retry should only be available for failed deliveries where permitted.

---

# 11. Dead Letter Queue

The Dead Letter Queue displays permanently failed event deliveries.

Columns:

- Event
- Subscriber
- Failure Reason
- Retry Attempts
- Created Date
- Resolution Status

Filters:

- Event
- Subscriber
- Failure Reason
- Date Range
- Resolution Status

Row Actions:

- View Details
- Retry
- Mark Resolved
- Export

Only authorized administrators may retry or resolve failed deliveries.

---

# 12. Event History

The Event History screen provides a complete audit trail of event processing.

Columns:

- Event
- Publisher
- Subscriber
- Action
- Status
- Processing Time
- Date & Time

Filters:

- Event
- Subscriber
- Category
- Tenant
- Date Range

History is read-only.

---

# 13. Event Settings

The Event Settings screen allows administrators to configure the Event Bus.

Settings include:

- Default Retry Attempts
- Retry Interval
- Retry Strategy
- Dead Letter Queue Policy
- Event Retention Period
- Maximum Payload Size
- Correlation ID Requirement

Future settings may include:

- Event Replay
- Event Priorities
- Event Streaming
- External Connectors

---

# 14. UI States

Every screen shall support the following states.

## Loading

Display loading indicators or skeleton components.

---

## Empty

Example:

```text
No events found.
````

Provide a relevant action where appropriate.

---

## Validation Error

Display field-level validation messages.

Examples:

- Event Code is required.
- Subscriber is required.
- Retry Attempts must be greater than zero.

---

## Error

Display friendly error messages.

Examples:

- Event could not be loaded.
- Subscriber unavailable.
- Retry failed.

Provide a retry option where appropriate.

---

## No Permission

Display:

```text
You do not have permission to manage platform events.
```

---

## Success

Display toast notifications.

Examples:

- Event registered successfully.
- Subscription created successfully.
- Event retried successfully.

---

# 15. Shared Components

The Platform Event Bus shall use shared Platform Framework components.

Examples:

- PageHeader
- DataTable
- AppModal
- AppButton
- AppInput
- AppSelect
- SearchToolbar
- FilterPanel
- StatusBadge
- JsonViewer
- EmptyState
- LoadingSkeleton
- ErrorState
- PermissionGate
- Timeline
- Badge

Custom components should only be created where shared components cannot satisfy the requirement.

---

# 16. Responsive Design

The interface shall support:

- Desktop
- Tablet
- Mobile

Responsive behavior:

- Tables support horizontal scrolling.
- JSON payloads wrap appropriately.
- Timelines collapse vertically.
- Filters collapse into drawers.
- Detail pages stack vertically.

---

# 17. Implementation Rules

The UI implementation shall comply with:

- docs/architecture/Architecture.md
- docs/architecture/DesignLanguage.md
- docs/architecture/CodingStandards.md
- docs/architecture/FolderStructure.md

Implementation requirements:

- React + TypeScript
- Tailwind CSS
- shadcn/ui
- React Hook Form
- Zod
- React Router
- Service Layer Architecture
- Shared Platform Framework Components

---

# 18. Success Criteria

The Platform Event Bus UI is considered complete when:

- Event Types can be managed.
- Subscribers can be managed.
- Subscriptions can be managed.
- Published Events can be viewed.
- Event Deliveries can be monitored.
- Dead Letter Queue can be managed.
- Event History is available.
- Settings can be configured.
- Permissions are enforced.
- The interface is responsive and consistent.

---

# 19. Conclusion

The Platform Event Bus UI provides administrators and operators with a centralized interface for managing and monitoring event-driven communication across Business Suite.

By separating configuration from monitoring and providing visibility into events, subscribers, deliveries, failures, and history, the platform delivers a robust operational experience suitable for enterprise-scale event processing.
