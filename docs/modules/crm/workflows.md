# CRM Module Workflow Specification

---

# 1. Workflow Overview

The CRM Module supports the complete customer journey from the first interaction with a prospective customer through long-term customer relationship management.

The CRM Module **does not execute workflows directly**.

Business workflows are orchestrated by the **Workflow Engine**, while the CRM Module initiates, participates in, and responds to workflow events.

This document defines the business processes, lifecycle transitions, module interactions, approval points, and event flow for the CRM Module.

---

# 2. Workflow Principles

The CRM workflows follow these principles:

- Customer-Centric
- Event-Driven
- Workflow Engine Orchestrated
- API First
- Multi-Tenant
- Configurable
- Auditable
- Modular
- Extensible
- Enterprise Scalable

---

# 3. Workflow Ownership

| Responsibility         | Owner                   |
| ---------------------- | ----------------------- |
| Workflow Execution     | Workflow Engine         |
| Workflow Configuration | Workflow Engine         |
| Workflow Approvals     | Workflow Engine         |
| Customer Data          | CRM Module              |
| Notifications          | Notification Engine     |
| Audit History          | Activity & Audit Engine |
| Business Events        | Platform Event Bus      |

CRM owns the business data.

The Workflow Engine owns the workflow.

---

# 4. Customer Journey

The CRM Module supports multiple customer journeys.

## Standard Journey

```text
Lead
      │
      ▼
Qualification
      │
      ▼
Opportunity
      │
      ▼
Customer
      │
      ▼
Sales
      │
      ▼
Finance
      │
      ▼
Customer Support
      │
      ▼
Projects
      │
      ▼
Customer Retention
```

---

## Direct Customer

```text
Customer
      │
      ▼
Sales
      │
      ▼
Finance
```

No lead is required.

---

## Existing Customer

```text
Customer
      │
      ▼
New Opportunity
      │
      ▼
Sales
      │
      ▼
Finance
```

---

## Lost Lead

```text
Lead
      │
      ▼
Disqualified
```

---

## Lost Opportunity

```text
Lead
      │
      ▼
Opportunity
      │
      ▼
Lost
```

---

# 5. Lead Management Workflow

## Objective

Manage the lifecycle of a prospective customer.

---

## Flow

```text
Lead Created
      │
      ▼
Lead Assigned
      │
      ▼
Initial Contact
      │
      ▼
Qualification
      │
      ▼
Decision
      │
      ├──────────────┐
      ▼              ▼
Qualified      Disqualified
      │
      ▼
Create Opportunity
```

---

## Trigger Events

- Lead Created
- Lead Assigned
- Lead Updated
- Lead Qualified
- Lead Disqualified

---

## Platform Engine Usage

Workflow Engine

- Assignment
- Approval
- Routing

Notification Engine

- Assignment alerts
- Reminder notifications

Activity & Audit Engine

- Lead history

---

# 6. Lead Conversion Workflow

## Objective

Convert a qualified lead into an active customer relationship.

---

## Flow

```text
Qualified Lead
        │
        ▼
Convert Lead
        │
        ▼
Create Customer Account
        │
        ▼
Create Primary Contact
        │
        ▼
Create Opportunity
        │
        ▼
Publish CustomerCreated Event
```

---

## Integration

Finance Engine

Creates Customer Financial Account.

Sales Module

Receives Opportunity.

Notification Engine

Notifies assigned users.

Activity & Audit Engine

Records conversion history.

---

# 7. Customer Creation Workflow

Customers may originate from multiple sources.

---

## Path A

```text
Lead
      │
      ▼
Converted
      │
      ▼
Customer
```

---

## Path B

```text
Direct Customer Entry
        │
        ▼
Customer
```

---

## Path C

```text
Imported Customer
        │
        ▼
Customer
```

---

## Path D

```text
API Customer
        │
        ▼
Customer
```

---

## Outputs

- Customer Number requested from Document Numbering Engine
- Customer Account created
- Customer Timeline initialized
- CustomerCreated event published

---

# 8. Opportunity Workflow

## Flow

```text
Opportunity Created
        │
        ▼
Qualification
        │
        ▼
Proposal
        │
        ▼
Negotiation
        │
 ┌──────┴──────┐
 ▼             ▼
Won          Lost
```

---

## Won Opportunity

```text
Won
     │
     ▼
Sales Module
     │
     ▼
Quotation
```

CRM does not generate quotations.

---

# 9. Sales Handover Workflow

Once an opportunity is won:

```text
Opportunity Won
       │
       ▼
Publish OpportunityWon
       │
       ▼
Sales Module
       │
       ▼
Quotation
       │
       ▼
Sales Order
       │
       ▼
Invoice
```

Ownership transfers to the Sales Module while CRM continues tracking customer engagement.

---

# 10. Finance Integration Workflow

Finance integration begins after a customer exists.

```text
Customer Created
       │
       ▼
CustomerCreated Event
       │
       ▼
Finance Engine
       │
       ▼
Create Customer Financial Account
       │
       ▼
Receivables Ready
```

CRM owns:

- Customer identity

Finance owns:

- Financial account
- Receivables
- Balances
- Payments

---

# 11. Customer Activity Workflow

Activities follow a common lifecycle.

```text
Create Activity
        │
        ▼
Assign User
        │
        ▼
Reminder
        │
        ▼
Complete Activity
        │
        ▼
Timeline Update
```

Reminders are delivered by the Notification Engine.

---

# 12. Communication Workflow

CRM records communication.

Notification Engine performs delivery.

```text
User Action
      │
      ▼
Notification Request
      │
      ▼
Notification Engine
      │
      ▼
Email / SMS / Push
      │
      ▼
Delivery Status
      │
      ▼
CRM Communication History
```

---

# 13. Customer Timeline Workflow

Every significant event contributes to Customer 360.

Examples include:

```text
Customer Created

↓

Contact Added

↓

Meeting Held

↓

Opportunity Created

↓

Quotation Generated

↓

Invoice Issued

↓

Payment Received

↓

Support Ticket Opened

↓

Project Started
```

CRM aggregates the timeline.

Each originating module owns its own records.

---

# 14. Customer Support Workflow (Future)

```text
Customer
      │
      ▼
Support Ticket
      │
      ▼
Assignment
      │
      ▼
Resolution
      │
      ▼
Customer Satisfaction
      │
      ▼
Timeline Update
```

Customer Support owns the workflow.

CRM displays the results.

---

# 15. Project Workflow (Future)

```text
Customer
      │
      ▼
Project Created
      │
      ▼
Milestones
      │
      ▼
Completion
      │
      ▼
Timeline Update
```

Projects remain owned by the Projects Module.

---

# 16. Approval Workflows

The following workflows may be configured through the Workflow Engine.

- Lead Approval
- Customer Approval
- Opportunity Approval
- Customer Status Change
- Customer Archive Approval

Approval logic must never be implemented inside CRM.

---

# 17. Event Publishing

CRM publishes events including:

- AccountCreated
- CustomerCreated
- LeadCreated
- LeadQualified
- LeadConverted
- OpportunityCreated
- OpportunityWon
- OpportunityLost
- ContactCreated
- ActivityCreated

Events are published through the Platform Event Bus.

---

# 18. Notification Points

Notifications may be generated for:

- Lead Assignment
- Opportunity Assignment
- Activity Reminder
- Meeting Reminder
- Follow-up Reminder
- Workflow Approval
- Customer Assignment

Notification delivery is owned by the Notification Engine.

---

# 19. Workflow Summary

The CRM Module participates in business workflows while delegating workflow execution, notifications, auditing, numbering, and document management to their respective Platform Engines.

This separation ensures that CRM remains focused on customer relationships while the Business Suite Platform provides reusable enterprise services that can be consumed consistently across Sales, Finance, Procurement, Inventory, HR, Customer Support, Projects, and all future Business Modules.
