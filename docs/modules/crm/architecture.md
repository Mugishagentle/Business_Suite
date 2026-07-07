# CRM Module Architecture

---

# 1. Architecture Overview

The CRM Module is the customer relationship domain of the Business Suite Enterprise Platform.

It manages the complete customer lifecycle, customer identity, customer interactions, leads, opportunities, contacts, and customer relationship history.

The CRM Module is designed as a Business Module that integrates with Platform Engines and other Business Modules without duplicating their responsibilities.

The CRM Module provides the foundation for:

- Sales
- Finance
- Marketing
- Customer Support
- Projects
- Customer Portal
- Customer 360
- AI Customer Insights

---

# 2. Architectural Position

The CRM Module sits between Platform Engines and Business Modules.

```text
                         Platform Engines
                                │
                                │
        ┌───────────────────────▼───────────────────────┐
        │                  CRM Module                    │
        └───────────────────────┬───────────────────────┘
                                │
                                │
                         Business Modules
```

The CRM Module owns customer relationship data.

Other modules consume CRM customer identity and contribute customer-related activity back to CRM.

---

# 3. Architectural Principles

The CRM Module follows these principles:

- Customer-centric architecture
- Single customer identity
- Customer 360 visibility
- API-first design
- Event-driven integration
- Multi-tenant isolation
- Platform Engine reuse
- Business Module separation
- Finance Engine integration
- Extensible domain model
- Auditability
- Enterprise scalability

---

# 4. CRM Domain Architecture

The CRM domain is built around the Account aggregate.

```text
                         CRM Module
                              │
        ┌─────────────────────┼─────────────────────┐
        │                     │                     │
     Accounts              Contacts             Opportunities
        │                     │                     │
        ├─────────────┬───────┼───────────┬─────────┤
        │             │       │           │         │
   Organizations  Individuals Leads   Activities  Timeline
        │
        ├─────────────────────────────────────────────┐
        │                                             │
   Relationships                                  Addresses
```

The Account is the central CRM record referenced by Sales, Finance, Customer Support, Projects, and Marketing.

---

# 5. Account Architecture

An Account represents any party with whom the business has or may have a relationship.

An Account may represent:

- Organization
- Individual
- Prospect
- Lead
- Customer
- Partner
- Corporate Account
- Business Relationship

The Account preserves a single identity throughout the customer journey.

```text
Account
│
├── Account Type
│   ├── Organization
│   └── Individual
│
├── Lifecycle Stage
│   ├── Lead
│   ├── Prospect
│   ├── Qualified
│   ├── Customer
│   ├── Active Customer
│   ├── Dormant
│   ├── Inactive
│   └── Archived
│
├── Contacts
├── Addresses
├── Opportunities
├── Activities
├── Relationships
└── Timeline
```

This avoids duplicate records when a lead becomes a customer.

---

# 6. Customer Lifecycle Architecture

The CRM Module supports flexible customer lifecycle progression.

```text
Lead
  ↓
Prospect
  ↓
Qualified
  ↓
Opportunity
  ↓
Customer
  ↓
Active Customer
  ↓
VIP Customer
  ↓
Dormant
  ↓
Inactive
  ↓
Archived
```

The lifecycle is not mandatory or linear.

Supported paths include:

```text
Lead → Qualified → Opportunity → Customer
```

```text
Direct Customer
```

```text
Existing Customer → New Opportunity
```

```text
Lead → Disqualified
```

```text
Opportunity → Lost
```

---

# 7. Lead Architecture

A Lead is an Account in an early lifecycle stage.

The CRM Module supports:

- Manual lead creation
- Imported leads
- API-created leads
- Lead source tracking
- Lead assignment
- Lead qualification
- Lead conversion
- Lead disqualification
- Duplicate lead detection

Lead conversion may result in:

- Customer Account
- Opportunity
- Contact
- Sales activity

---

# 8. Opportunity Architecture

An Opportunity represents a potential business deal.

Opportunities may belong to:

- Lead Account
- Prospect Account
- Existing Customer Account

Opportunity stages may include:

```text
Prospecting
  ↓
Qualification
  ↓
Proposal
  ↓
Negotiation
  ↓
Won / Lost
```

A won opportunity may trigger Sales Module processes such as:

- Quotation
- Sales Order
- Invoice

CRM owns the opportunity.

Sales owns quotations, orders, and invoices.

---

# 9. Activity Architecture

Activities represent customer-facing or internal relationship actions.

Activities may be linked to:

- Account
- Contact
- Lead
- Opportunity
- Future Ticket
- Future Project

Activity types include:

- Call
- Meeting
- Email
- SMS
- Task
- Note
- Follow-up
- Reminder
- Site Visit
- Demo

The CRM Module records activities, while reminders and message delivery are handled by the Notification Engine.

---

# 10. Communication Architecture

CRM stores communication history only.

The Notification Engine owns delivery.

```text
CRM Activity / Communication Record
              │
              ▼
Notification Engine
              │
              ▼
Email / SMS / Push / In-App
```

CRM may store:

- Email record
- SMS record
- Call log
- Meeting note
- Internal note
- Communication status reference

CRM does not own notification templates, queues, or delivery infrastructure.

---

# 11. Customer Timeline Architecture

The Customer Timeline provides a unified chronological history of the customer relationship.

Timeline entries may come from:

- CRM
- Sales
- Finance
- Support
- Projects
- Marketing
- Document Management
- Activity & Audit Engine

Example:

```text
Lead Created
  ↓
Call Logged
  ↓
Meeting Held
  ↓
Opportunity Created
  ↓
Quotation Issued
  ↓
Invoice Generated
  ↓
Payment Received
  ↓
Support Ticket Opened
  ↓
Ticket Resolved
  ↓
New Opportunity Created
```

CRM owns CRM timeline entries and consumes timeline events from other modules.

---

# 12. Customer 360 Architecture

Customer 360 is the unified customer profile experience.

```text
                       Customer 360
                            │
 ┌───────────┬──────────────┼──────────────┬──────────────┐
 │           │              │              │              │
Profile   Contacts      Activities    Opportunities    Timeline
 │           │              │              │              │
 └───────────┴──────┬───────┴──────┬───────┴──────────────┘
                    │              │
                Finance          Sales
                    │              │
             Support / Projects / Marketing
```

Customer 360 may display:

- Customer profile
- Contacts
- Addresses
- Opportunities
- Activities
- Communication history
- Sales history
- Financial summary
- Documents
- Support history
- Project history
- Timeline
- Analytics

CRM aggregates this information but does not own all of it.

---

# 13. Finance Engine Integration Architecture

CRM must integrate directly with the Finance Engine.

The ownership rule is:

```text
CRM owns the customer relationship.
Finance Engine owns the customer financial account.
```

## CRM Owns

- Account identity
- Customer profile
- Lifecycle stage
- Contacts
- Addresses
- Relationships
- Opportunities
- Activities
- Customer timeline

## Finance Engine Owns

- Customer financial account
- Receivables
- Customer balances
- Payment history
- Credit status
- Financial postings
- Ledger impact
- Accounting rules

---

## CRM to Finance Flow

When a CRM Account becomes a Customer:

```text
CRM Account Created / Converted
          ↓
CRM publishes CustomerCreated event
          ↓
Finance Engine creates Customer Financial Account
          ↓
Finance links receivables to CRM Account
          ↓
Customer balance becomes available to Customer 360
```

---

## Finance to CRM Flow

When financial activity occurs:

```text
Invoice Generated
        ↓
Payment Received
        ↓
Customer Balance Updated
        ↓
Finance publishes CustomerFinancialSummaryUpdated
        ↓
CRM Customer 360 displays updated finance summary
```

CRM must not duplicate balances, invoices, receipts, or accounting records.

It should only display finance-owned summaries through integration contracts.

---

# 14. Sales Integration Architecture

The Sales Module consumes CRM Accounts and Opportunities.

```text
CRM Opportunity
       ↓
Sales Quotation
       ↓
Sales Order
       ↓
Invoice
       ↓
Finance Receivable
```

CRM owns the opportunity and customer profile.

Sales owns:

- Quotations
- Sales Orders
- Sales Invoices
- Sales documents

Finance owns:

- Receivables
- Ledger postings
- Payment allocation
- Customer balances

---

# 15. Customer Support Integration Architecture

Customer Support is a future Business Module.

CRM should be designed with Support integration in mind.

Support will own:

- Tickets
- Cases
- Complaints
- Service Requests
- SLAs
- Escalations
- Support Queues
- Resolutions
- Customer Satisfaction

CRM will display support activity in Customer 360.

```text
Customer
   ↓
Ticket Opened
   ↓
Ticket Assigned
   ↓
Ticket Resolved
   ↓
Customer Feedback
   ↓
Retention / Upsell Opportunity
```

CRM does not own support tickets.

---

# 16. Platform Engine Integration Architecture

## Platform Core

CRM consumes:

- Tenants
- Branches
- Users
- Organizations
- Business Units

---

## Authorization Engine

CRM uses:

- Roles
- Permissions
- Policies
- Access Rules

---

## Workflow Engine

CRM may use workflows for:

- Lead approval
- Customer approval
- Opportunity approval
- Customer status changes

---

## Notification Engine

CRM uses the Notification Engine for:

- Follow-up reminders
- Customer emails
- SMS notifications
- Opportunity alerts
- Activity reminders

---

## Document Management Engine

CRM stores document references only.

Documents may include:

- Contracts
- Agreements
- Certificates
- Attachments
- Customer documents

---

## Activity & Audit Engine

CRM sends audit events for:

- Account changes
- Contact changes
- Opportunity changes
- Lead conversion
- Activity updates
- Customer lifecycle changes

---

## Reporting Engine

CRM provides data for:

- CRM dashboards
- Lead reports
- Opportunity reports
- Pipeline reports
- Customer reports
- Activity reports

---

## Search & Indexing Engine

CRM exposes searchable data for:

- Account search
- Contact search
- Lead search
- Opportunity search
- Customer global search

---

# 17. Event Architecture

The CRM Module uses the Platform Event Bus for integration.

CRM may publish events such as:

- AccountCreated
- AccountUpdated
- AccountArchived
- LeadCreated
- LeadQualified
- LeadDisqualified
- LeadConverted
- ContactCreated
- ContactUpdated
- OpportunityCreated
- OpportunityUpdated
- OpportunityWon
- OpportunityLost
- ActivityCreated
- CustomerCreated
- CustomerUpdated
- CustomerLifecycleChanged

Other modules may subscribe to these events.

Finance may subscribe to:

- CustomerCreated
- CustomerUpdated
- CustomerArchived

Sales may subscribe to:

- OpportunityWon
- CustomerCreated

Reporting may subscribe to:

- LeadCreated
- OpportunityCreated
- OpportunityWon
- OpportunityLost

Search may subscribe to:

- AccountCreated
- AccountUpdated
- ContactUpdated

---

# 18. Data Ownership Architecture

The CRM Module owns:

- Accounts
- Contacts
- Leads
- Opportunities
- Activities
- Communications
- Addresses
- Relationships
- Lifecycle stages
- Customer classification
- CRM timeline entries

The CRM Module references:

- Finance customer accounts
- Customer balances
- Receivables
- Quotations
- Sales orders
- Invoices
- Payments
- Receipts
- Support tickets
- Project records
- Documents
- Audit logs
- Reports

CRM must not duplicate records owned by other modules.

---

# 19. Multi-Tenant Architecture

Every CRM record must be tenant-scoped.

Tenant isolation applies to:

- Accounts
- Contacts
- Leads
- Opportunities
- Activities
- Communications
- Addresses
- Relationships
- Timeline records

CRM data access must be enforced using:

- Tenant ID
- Branch ID where applicable
- User assignment
- Authorization policies
- PostgreSQL Row Level Security

---

# 20. Branch and Assignment Architecture

CRM supports branch-level ownership where applicable.

Accounts, leads, opportunities, and activities may be assigned to:

- Branch
- Department
- Sales representative
- Account manager
- Team

This supports SMEs and enterprise structures.

---

# 21. Security Architecture

CRM security is enforced through the Authorization Engine and Platform Core.

Security considerations include:

- Tenant isolation
- Branch access
- Role-based access
- Record ownership
- Team-based access
- Field-level restrictions
- Sensitive customer data protection
- Audit logging

Example permissions:

```text
crm.account.view
crm.account.create
crm.account.update
crm.account.archive
crm.contact.manage
crm.lead.convert
crm.opportunity.manage
crm.activity.manage
crm.timeline.view
crm.finance_summary.view
```

Finance summary access should be permission-controlled because it exposes financial information.

---

# 22. Scalability Architecture

The CRM Module must support growth from SMEs to enterprise organizations.

Scalability considerations include:

- Indexed customer records
- Search indexing
- Event-driven updates
- Asynchronous timeline building
- Pagination
- Activity archiving
- Duplicate detection optimization
- Customer 360 summary caching
- Financial summary caching where appropriate
- Reporting snapshots
- Tenant-level scaling

---

# 23. Extensibility Architecture

The CRM Module should support future extension for:

- Marketing automation
- Customer portal
- Customer support
- Help desk
- WhatsApp communication
- Telephony integration
- Customer satisfaction surveys
- Customer health scoring
- AI lead scoring
- AI opportunity forecasting
- Customer journey analytics
- Loyalty programs

---

# 24. High-Level CRM Architecture Diagram

```text
                           Business Suite Platform

 ┌────────────────────────────────────────────────────────────────────┐
 │                         Platform Engines                           │
 │                                                                    │
 │ Core | Auth | Workflow | Events | Notifications | Docs | Search     │
 └────────────────────────────────────────────────────────────────────┘
                                  │
                                  ▼
 ┌────────────────────────────────────────────────────────────────────┐
 │                            CRM Module                              │
 │                                                                    │
 │ Accounts | Contacts | Leads | Opportunities | Activities | Timeline │
 └────────────────────────────────────────────────────────────────────┘
                                  │
        ┌─────────────────────────┼─────────────────────────┐
        │                         │                         │
        ▼                         ▼                         ▼
 ┌──────────────┐          ┌──────────────┐          ┌──────────────┐
 │    Sales     │          │   Finance    │          │   Support    │
 │ Quotations   │          │ Receivables  │          │ Tickets      │
 │ Orders       │          │ Payments     │          │ Cases        │
 │ Invoices     │          │ Balances     │          │ SLAs         │
 └──────────────┘          └──────────────┘          └──────────────┘
        │                         │                         │
        └─────────────────────────┼─────────────────────────┘
                                  ▼
                          Customer 360 View
```

---

# 25. Architecture Summary

The CRM Module is the customer relationship foundation of the Business Suite Enterprise Platform.

It is built around a central Account model that supports organizations, individuals, leads, prospects, and customers using a unified lifecycle architecture.

CRM owns customer identity, relationships, leads, opportunities, activities, and customer timelines.

It integrates with the Finance Engine for customer financial accounts, balances, receivables, payments, and financial summaries while ensuring that Finance remains the owner of financial records.

The module is designed to support Customer 360, Sales, Finance, Customer Support, Marketing, Projects, Customer Portal, and future AI-powered customer intelligence without violating module boundaries.
