# CRM Module Database Specification

---

# 1. Database Overview

The CRM Module database is the authoritative repository for customer relationship data within the Business Suite Enterprise Platform.

It is designed around a **single customer identity model**, enabling organizations to manage the complete customer lifecycle—from lead acquisition to long-term customer engagement—without duplicating customer records.

Unlike traditional CRM systems that separate leads, prospects, and customers into unrelated entities, the Business Suite CRM uses an **Account-Centric Architecture**, where an Account evolves through different lifecycle stages while maintaining a single business identity.

The CRM database serves as the Customer Master for the entire Business Suite and provides customer information to other Business Modules including:

- Sales
- Finance
- Procurement
- Customer Support
- Projects
- Marketing
- Customer Portal
- Future AI Services

The CRM Module owns customer relationship data only.

Business transactions such as quotations, invoices, payments, accounting entries, support tickets, and projects remain owned by their respective modules.

---

# 2. Database Objectives

The CRM database is designed to:

- Maintain a single source of customer truth.
- Eliminate duplicate customer records.
- Support organizations and individuals.
- Track complete customer lifecycle.
- Support Customer 360.
- Support multi-tenancy.
- Support branch operations.
- Integrate with Platform Engines.
- Integrate with Business Modules.
- Support enterprise scalability.
- Support future AI capabilities.

---

# 3. Database Design Principles

The CRM database follows the Business Suite database standards.

## Customer-Centric

The customer is the central business entity.

All customer-related information is organized around a single Account.

---

## Account-Based Architecture

Every business relationship is represented by an Account.

An Account may represent:

- Organization
- Individual
- Prospect
- Lead
- Customer
- Partner

The account evolves throughout the customer lifecycle.

---

## Single Source of Truth

Customer information shall exist only once.

Other Business Modules reference CRM Accounts rather than maintaining duplicate customer records.

---

## Platform Engine Reuse

The CRM Module consumes Platform Engine services instead of duplicating functionality.

Examples include:

- Document Numbering
- Document Management
- Notifications
- Workflows
- Authorization
- Reference Data
- Reporting
- Search
- Audit Logging

---

## Modular Ownership

Each module owns only its business domain.

CRM owns customer relationships.

Finance owns accounting.

Sales owns commercial documents.

Support owns tickets.

Projects owns projects.

---

## Event-Driven Integration

CRM communicates with other modules through the Platform Event Bus.

Business events are published rather than invoking modules directly wherever appropriate.

---

## Multi-Tenant Isolation

Every CRM record belongs to a single tenant.

Tenant isolation is enforced through:

- Platform Core
- PostgreSQL Row Level Security
- Authorization Engine

---

## Extensibility

The schema must support future expansion without major redesign.

---

# 4. Module Ownership

The following table defines ownership boundaries.

| Business Capability         | Owner                         |
| --------------------------- | ----------------------------- |
| Customer Accounts           | CRM                           |
| Organizations               | CRM                           |
| Individuals                 | CRM                           |
| Contacts                    | CRM                           |
| Leads                       | CRM                           |
| Opportunities               | CRM                           |
| Activities                  | CRM                           |
| Customer Timeline           | CRM (Aggregated)              |
| Customer Financial Accounts | Finance Engine                |
| Customer Balances           | Finance Engine                |
| Receivables                 | Finance Engine                |
| Quotations                  | Sales Module                  |
| Sales Orders                | Sales Module                  |
| Sales Invoices              | Sales Module / Finance Engine |
| Documents                   | Document Management Engine    |
| Business Numbering          | Document Numbering Engine     |
| Notifications               | Notification Engine           |
| Reference Data              | Reference Data Engine         |
| Workflows                   | Workflow Engine               |
| Audit Logs                  | Activity & Audit Engine       |
| Reports                     | Reporting Engine              |
| Global Search               | Search & Indexing Engine      |

The CRM Module shall never duplicate functionality owned by another Platform Engine or Business Module.

---

# 5. Aggregate Root

The CRM Module is built around a single aggregate root.

## Account

The Account represents the master customer entity.

Every business relationship references an Account.

```text
Account
│
├── Organization
├── Individual
├── Contacts
├── Addresses
├── Opportunities
├── Activities
├── Communications
├── Timeline
├── Relationships
└── Tags
```

All customer-facing modules reference the Account.

The Account remains the same throughout the customer lifecycle.

---

# 6. Customer Lifecycle Model

An Account progresses through configurable lifecycle stages.

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

Supported alternative journeys include:

```text
Direct Customer
```

```text
Existing Customer

↓

New Opportunity
```

```text
Lead

↓

Disqualified
```

The lifecycle stage is a business attribute of the Account and should be configurable through the Reference Data Engine.

---

# 7. Core Database Concepts

The CRM database is organized around the following concepts.

## Account

Represents the customer identity.

---

## Contact

Represents an individual associated with an Account.

---

## Address

Represents one or more customer locations.

---

## Opportunity

Represents a potential business transaction.

---

## Activity

Represents customer interactions.

---

## Communication

Represents historical communication records.

---

## Relationship

Represents relationships between Accounts.

---

## Timeline

Represents chronological customer events.

---

## Customer 360

Represents the unified customer view assembled from CRM and other Business Modules.

CRM owns only CRM data while consuming information from integrated modules.

---

# 8. Database Architecture

The CRM database follows a layered architecture.

```text
                    CRM Module Database

                           │

               Aggregate Root (Account)

                           │

 ┌─────────────┬─────────────┬──────────────┬─────────────┐
 │             │             │              │             │
Contacts   Addresses   Opportunities   Activities   Timeline
 │             │             │              │             │
 └─────────────┴─────────────┴──────────────┴─────────────┘
                           │
                 Customer Relationships
                           │
                  Customer 360 Composition
```

This architecture keeps the customer identity centralized while allowing future modules to contribute additional business context.

---

# 9. Database Schema Overview

The CRM Module is composed of three logical layers.

## Core Domain

Stores CRM-owned business data.

Examples include:

- Accounts
- Contacts
- Addresses
- Opportunities
- Activities
- Communications
- Relationships
- Timeline

---

## Integration Layer

Provides references to external modules.

Examples include:

- Finance Integration
- Sales Integration
- Support Integration
- Project Integration
- Document Integration

The CRM Module stores references only.

Ownership remains with the originating module.

---

## Configuration Layer

Consumes configuration from Platform Engines.

Examples include:

- Customer Types
- Lifecycle Stages
- Opportunity Stages
- Activity Types
- Lead Sources
- Customer Categories
- Industries
- Address Types

These values originate from the Reference Data Engine.

---

# 10. Platform Engine Dependencies

The CRM database depends on the following Platform Engines.

| Platform Engine            | Database Responsibility                 |
| -------------------------- | --------------------------------------- |
| Platform Core              | Users, Tenants, Branches, Organizations |
| Authorization Engine       | Security Policies                       |
| Workflow Engine            | Workflow References                     |
| Document Numbering Engine  | Customer, Lead and Opportunity Numbers  |
| Document Management Engine | Document References                     |
| Notification Engine        | Communication References                |
| Reference Data Engine      | Lookup Values                           |
| Search & Indexing Engine   | Search Metadata                         |
| Reporting Engine           | Reporting Views                         |
| Activity & Audit Engine    | Audit References                        |
| Platform Event Bus         | Business Event Integration              |

CRM stores only references where appropriate.

---

# 11. Business Module Dependencies

The CRM Module integrates with other Business Modules.

| Business Module    | Database Integration                        |
| ------------------ | ------------------------------------------- |
| Finance            | Customer Financial Account References       |
| Sales              | Opportunity, Quotation and Sales References |
| Customer Support   | Ticket References                           |
| Projects           | Project References                          |
| Marketing (Future) | Campaign References                         |

CRM never owns business data belonging to these modules.

---

# 12. Database Summary

The CRM Module database is designed around a single Account aggregate that serves as the Customer Master for the entire Business Suite Enterprise Platform.

It maintains customer identity, relationships, contacts, opportunities, activities, communications, and customer history while integrating seamlessly with Platform Engines and Business Modules through well-defined ownership boundaries.

This architecture ensures:

- Single customer identity
- Customer 360 support
- Modular ownership
- Platform Engine reuse
- Event-driven integration
- Enterprise scalability
- Multi-tenant isolation
- Future extensibility

The following sections define the individual database tables, their relationships, business rules, integration points, and implementation requirements.

---

# 13. Core Tables

The CRM Module is built around a set of core business tables.

These tables represent the CRM domain and are fully owned by the CRM Module.

```text
crm_accounts
│
├── crm_contacts
├── crm_addresses
├── crm_opportunities
├── crm_activities
├── crm_communications
├── crm_account_relationships
├── crm_timeline_entries
├── crm_account_tags
└── crm_account_tag_assignments
```

These tables form the Customer Master for the Business Suite.

---

# 14. crm_accounts

## Purpose

The **crm_accounts** table is the primary business entity of the CRM Module.

Every customer relationship begins with an Account.

An Account may represent:

- Lead
- Prospect
- Customer
- Organization
- Individual
- Business Partner
- Vendor Prospect (Future)
- Strategic Partner (Future)

Unlike traditional CRMs, Business Suite does not duplicate customer records during lifecycle progression.

A Lead, Prospect, Opportunity Customer and Active Customer all remain the same Account with different lifecycle stages.

---

## Ownership

Owned By:

```text
CRM Module
```

Referenced By:

- Sales Module
- Finance Engine
- Customer Support
- Projects Module
- Marketing Module
- Procurement Module (Business Partners)
- Reporting Engine
- Search Engine

---

## Platform Engine Dependencies

| Platform Engine           | Purpose                                      |
| ------------------------- | -------------------------------------------- |
| Platform Core             | Tenant, Branch, User references              |
| Document Numbering Engine | Customer Number generation                   |
| Workflow Engine           | Customer approval workflows                  |
| Reference Data Engine     | Customer types, industries, lifecycle stages |
| Search & Indexing Engine  | Global customer search                       |
| Reporting Engine          | Customer reporting                           |
| Activity & Audit Engine   | Customer audit history                       |

---

## Business Module Dependencies

| Business Module  | Usage                        |
| ---------------- | ---------------------------- |
| Finance          | Customer Financial Account   |
| Sales            | Quotations, Orders, Invoices |
| Customer Support | Customer Tickets             |
| Projects         | Customer Projects            |
| Marketing        | Campaign Membership          |
| Procurement      | Business Partners            |

---

## Relationships

```text
crm_accounts

│

├── crm_contacts

├── crm_addresses

├── crm_opportunities

├── crm_activities

├── crm_communications

├── crm_account_relationships

├── crm_timeline_entries

├── crm_account_tags

└── crm_account_finance_links
```

---

## Key Fields

### Identity

```text
id

tenant_id

branch_id

customer_number

account_type

lifecycle_stage
```

---

### Organization

```text
legal_name

display_name

registration_number

tax_identification_number

industry_id

customer_category_id

customer_tier_id
```

---

### Individual

```text
first_name

middle_name

last_name

national_identification_number

date_of_birth
```

---

### Contact Information

```text
email

phone

alternate_phone

website
```

---

### Ownership

```text
assigned_user_id

account_manager_id

sales_team_id
```

---

### Lifecycle

```text
status

customer_since

converted_at

lead_source_id

qualified_at

last_activity_at
```

---

### Audit

```text
created_by

updated_by

created_at

updated_at

deleted_at
```

---

## Business Rules

- Customer Number is generated by the Document Numbering Engine.
- One Account represents one business relationship.
- Duplicate Accounts should be prevented.
- An Account may exist without Opportunities.
- An Account may have multiple Contacts.
- An Account may have multiple Addresses.
- An Account may belong to one Tenant.
- Lifecycle stages are configurable.
- Soft deletion is supported.

---

## Suggested Indexes

```text
tenant_id

customer_number

display_name

legal_name

email

phone

registration_number

tax_identification_number

status

assigned_user_id
```

---

## Events Published

```text
AccountCreated

AccountUpdated

CustomerCreated

CustomerArchived

CustomerActivated

LifecycleChanged
```

---

## Events Consumed

```text
CustomerFinancialAccountCreated

CustomerBalanceUpdated

QuotationCreated

InvoiceCreated

SupportTicketCreated
```

---

## Security Considerations

- Tenant isolation mandatory.
- Branch security supported.
- Sensitive customer information permission-controlled.
- Row Level Security enforced.
- Personally identifiable information protected.

---

## Future Expansion

Future fields may include:

- Customer Health Score
- AI Customer Score
- Preferred Language
- Preferred Currency
- Customer Portal Status
- Loyalty Status
- Marketing Consent

---

# 15. crm_contacts

## Purpose

Stores people associated with an Account.

An organization may have multiple contacts.

An individual customer may automatically become the primary contact.

---

## Ownership

Owned By:

```text
CRM Module
```

Referenced By:

- Activities
- Communications
- Opportunities
- Customer Support
- Marketing

---

## Platform Engine Dependencies

| Platform Engine         | Purpose                                  |
| ----------------------- | ---------------------------------------- |
| Platform Core           | User references                          |
| Reference Data Engine   | Contact Roles, Communication Preferences |
| Activity & Audit Engine | Audit history                            |

---

## Relationships

```text
crm_accounts

│

└── crm_contacts

      │

      ├── crm_activities

      └── crm_communications
```

---

## Key Fields

### Identity

```text
id

tenant_id

account_id
```

---

### Personal Information

```text
first_name

middle_name

last_name

display_name
```

---

### Employment

```text
job_title

department

contact_role_id
```

---

### Communication

```text
email

phone

alternate_phone

communication_preference_id
```

---

### Status

```text
is_primary

status
```

---

### Audit

```text
created_by

updated_by

created_at

updated_at

deleted_at
```

---

## Business Rules

- One Account may have many Contacts.
- Only one Primary Contact per Account.
- Contacts are not Platform Users.
- Contacts may later become Customer Portal Users.

---

## Suggested Indexes

```text
tenant_id

account_id

email

phone

display_name
```

---

## Events Published

```text
ContactCreated

ContactUpdated

PrimaryContactChanged
```

---

## Events Consumed

```text
CustomerCreated
```

---

## Security Considerations

- Contact visibility follows Account permissions.
- Personally identifiable information protected.

---

## Future Expansion

Future capabilities may include:

- Social Media Profiles
- Digital Business Cards
- Multiple Languages
- Time Zone
- Customer Portal Invitations

---

# 16. crm_addresses

## Purpose

Stores multiple addresses associated with Accounts and Contacts.

The CRM Module supports multiple address types.

---

## Ownership

Owned By:

```text
CRM Module
```

---

## Platform Engine Dependencies

| Platform Engine         | Purpose         |
| ----------------------- | --------------- |
| Reference Data Engine   | Address Types   |
| Activity & Audit Engine | Address history |

---

## Relationships

```text
crm_accounts

│

└── crm_addresses
```

---

## Key Fields

```text
id

tenant_id

account_id

contact_id

address_type

address_line_1

address_line_2

city

district

region

country

postal_code

latitude

longitude

is_primary
```

---

## Business Rules

- Multiple addresses supported.
- Multiple address types supported.
- One primary address per address type.
- GPS coordinates optional.

---

## Suggested Address Types

Provided by the Reference Data Engine.

Examples:

```text
Billing

Shipping

Physical

Postal

Head Office

Branch

Warehouse
```

---

## Events Published

```text
AddressCreated

AddressUpdated

PrimaryAddressChanged
```

---

## Future Expansion

Future support:

- Google Maps Integration
- Route Optimization
- Delivery Zones
- Geo-Fencing

---

# 17. crm_account_relationships

## Purpose

Stores relationships between Accounts.

Supports organizational structures and business relationships.

---

## Examples

```text
Parent Company

↓

Subsidiary

↓

Branch
```

```text
Distributor

↓

Retailer
```

```text
Holding Company

↓

Business Unit
```

---

## Ownership

Owned By:

```text
CRM Module
```

---

## Platform Engine Dependencies

| Platform Engine         | Purpose              |
| ----------------------- | -------------------- |
| Reference Data Engine   | Relationship Types   |
| Activity & Audit Engine | Relationship history |

---

## Key Fields

```text
id

tenant_id

parent_account_id

child_account_id

relationship_type

start_date

end_date

status
```

---

## Business Rules

- Accounts may have multiple relationships.
- Circular relationships are not allowed.
- Relationship types are configurable.
- Relationships support historical tracking.

---

## Events Published

```text
RelationshipCreated

RelationshipUpdated

RelationshipEnded
```

---

## Future Expansion

Future support:

- Ownership Percentages
- Group Structures
- Corporate Hierarchies
- Franchise Networks

---

# 18. crm_opportunities

## Purpose

The **crm_opportunities** table stores all potential business opportunities associated with a customer Account.

An Opportunity represents a potential revenue-generating transaction before it enters the Sales Module.

CRM owns the opportunity lifecycle until it is handed over to Sales.

---

## Ownership

Owned By:

```text
CRM Module
```

Referenced By:

- Sales Module
- Finance Engine
- Reporting Engine
- Activity Module
- Customer Timeline

---

## Platform Engine Dependencies

| Platform Engine           | Purpose                                      |
| ------------------------- | -------------------------------------------- |
| Platform Core             | Tenant, Branch, User references              |
| Document Numbering Engine | Opportunity Number                           |
| Workflow Engine           | Opportunity Approval                         |
| Reference Data Engine     | Opportunity Stage, Loss Reasons, Win Reasons |
| Activity & Audit Engine   | Opportunity Audit History                    |
| Reporting Engine          | Opportunity Analytics                        |

---

## Business Module Dependencies

| Business Module | Usage                   |
| --------------- | ----------------------- |
| Sales           | Quotation Generation    |
| Finance         | Revenue Forecasting     |
| Projects        | Future Project Creation |

---

## Relationships

```text
crm_accounts

│

└── crm_opportunities

        │

        ├── crm_activities

        ├── crm_communications

        ├── crm_timeline_entries

        └── sales_quotations (Reference)
```

---

## Key Fields

### Identity

```text
id

tenant_id

branch_id

account_id

opportunity_number
```

---

### Opportunity Information

```text
title

description

opportunity_stage

status

lead_source_id

expected_close_date

actual_close_date
```

---

### Financial Information

```text
expected_value

currency_id

probability

forecast_category
```

---

### Ownership

```text
assigned_user_id

sales_team_id
```

---

### Outcome

```text
won_at

lost_at

loss_reason_id

win_reason_id
```

---

### Audit

```text
created_by

updated_by

created_at

updated_at

deleted_at
```

---

## Business Rules

- Every Opportunity belongs to one Account.
- Opportunity Number is generated by the Document Numbering Engine.
- Opportunity stages are configurable through the Reference Data Engine.
- A Won Opportunity may initiate quotation generation.
- CRM never generates quotations directly.
- Lost opportunities remain part of customer history.

---

## Suggested Indexes

```text
tenant_id

account_id

opportunity_number

assigned_user_id

opportunity_stage

expected_close_date

status
```

---

## Events Published

```text
OpportunityCreated

OpportunityUpdated

OpportunityAssigned

OpportunityWon

OpportunityLost

OpportunityClosed
```

---

## Events Consumed

```text
QuotationCreated

SalesOrderCreated

InvoiceCreated
```

---

## Security Considerations

- Opportunity visibility follows CRM security policies.
- Pipeline visibility may be restricted by branch, team, or assigned user.

---

## Future Expansion

Future capabilities include:

- AI Opportunity Scoring
- Competitor Analysis
- Revenue Prediction
- Multiple Sales Teams
- Opportunity Collaboration

---

# 19. crm_activities

## Purpose

The **crm_activities** table stores all customer interactions.

Activities provide a historical record of customer engagement and form an important part of Customer 360.

---

## Ownership

Owned By:

```text
CRM Module
```

---

## Platform Engine Dependencies

| Platform Engine         | Purpose                       |
| ----------------------- | ----------------------------- |
| Workflow Engine         | Activity Approvals (optional) |
| Notification Engine     | Reminders                     |
| Reference Data Engine   | Activity Types, Priorities    |
| Activity & Audit Engine | Activity History              |

---

## Relationships

```text
crm_accounts

│

├── crm_contacts

│

└── crm_activities
```

Activities may optionally reference:

- Opportunity
- Contact

---

## Key Fields

```text
id

tenant_id

account_id

contact_id

opportunity_id

activity_type

subject

description

priority

status

activity_date

due_date

completed_at

assigned_user_id
```

---

## Activity Types

Configured using the Reference Data Engine.

Examples:

```text
Call

Meeting

Email

Task

Follow-up

Reminder

Site Visit

Product Demo

Presentation
```

---

## Business Rules

- Every activity belongs to an Account.
- Activities may belong to an Opportunity.
- Activities may be assigned to users.
- Completed activities become part of Customer Timeline.

---

## Suggested Indexes

```text
tenant_id

account_id

assigned_user_id

activity_date

status

activity_type
```

---

## Events Published

```text
ActivityCreated

ActivityUpdated

ActivityCompleted

ActivityAssigned
```

---

## Events Consumed

```text
ReminderSent

WorkflowApproved
```

---

## Future Expansion

Future support includes:

- Calendar Synchronization
- Recurring Activities
- GPS Visit Tracking
- Mobile Check-in
- Voice Notes

---

# 20. crm_communications

## Purpose

Stores historical communication records between the organization and customers.

CRM stores communication history only.

Actual delivery is performed by the Notification Engine.

---

## Ownership

Owned By:

```text
CRM Module
```

Delivery Owned By:

```text
Notification Engine
```

---

## Platform Engine Dependencies

| Platform Engine         | Purpose             |
| ----------------------- | ------------------- |
| Notification Engine     | Delivery            |
| Reference Data Engine   | Communication Types |
| Activity & Audit Engine | Audit Trail         |

---

## Relationships

```text
crm_accounts

│

└── crm_communications
```

---

## Key Fields

```text
id

tenant_id

account_id

contact_id

activity_id

communication_type

direction

subject

message_summary

communication_date

delivery_status

notification_reference_id
```

---

## Communication Types

Configured through the Reference Data Engine.

Examples:

```text
Email

SMS

Phone Call

Meeting

Internal Note

WhatsApp (Future)

Teams (Future)
```

---

## Business Rules

- CRM stores communication history only.
- Delivery remains the responsibility of the Notification Engine.
- Communications become part of Customer Timeline.

---

## Events Published

```text
CommunicationRecorded
```

---

## Events Consumed

```text
NotificationDelivered

NotificationFailed
```

---

## Future Expansion

Future support:

- Omnichannel Messaging
- Social Media Integration
- AI Communication Analysis

---

# 21. crm_timeline_entries

## Purpose

The Timeline represents the chronological history of the customer relationship.

It is one of the most important components of Customer 360.

The Timeline aggregates events from multiple modules while preserving ownership in the originating module.

---

## Ownership

Timeline Entries:

```text
CRM Module
```

Source Records:

Remain owned by originating modules.

---

## Platform Engine Dependencies

| Platform Engine         | Purpose          |
| ----------------------- | ---------------- |
| Platform Event Bus      | Timeline Events  |
| Activity & Audit Engine | Audit References |

---

## Business Module Dependencies

Timeline may reference events from:

- CRM
- Sales
- Finance
- Customer Support
- Projects
- Procurement
- Marketing (Future)

---

## Relationships

```text
Customer

│

└── Timeline

        │

        ├── CRM Events

        ├── Sales Events

        ├── Finance Events

        ├── Support Events

        ├── Project Events

        └── Workflow Events
```

---

## Key Fields

```text
id

tenant_id

account_id

source_module

source_record_id

event_type

title

description

event_date

event_actor_id

metadata
```

---

## Timeline Event Examples

```text
Lead Created

Contact Added

Meeting Held

Opportunity Created

Quotation Generated

Sales Order Created

Invoice Issued

Payment Received

Support Ticket Opened

Project Started

Project Completed
```

---

## Business Rules

- Timeline is chronological.
- Timeline does not duplicate business data.
- Every entry references its source module.
- Entries cannot exist without a valid Account.

---

## Suggested Indexes

```text
tenant_id

account_id

event_date

source_module

event_type
```

---

## Events Published

Timeline itself does not publish events.

Timeline consumes events from the Platform Event Bus.

---

## Future Expansion

Future support:

- AI Timeline Summary
- Timeline Search
- Timeline Pinning
- Customer Journey Analytics
- Relationship Intelligence

---

# 22. Supporting Tables

The CRM Module includes several supporting tables that enrich the customer domain without duplicating business functionality owned by other Platform Engines or Business Modules.

Supporting tables include:

```text
crm_account_tags
crm_account_tag_assignments
crm_duplicate_candidates
crm_customer_segments
crm_customer_preferences
crm_customer_classifications
crm_customer_notes
crm_account_watchers
crm_account_favorites
crm_import_batches
```

These tables support categorization, personalization, collaboration, and data quality.

---

# 23. crm_account_tags

## Purpose

Provides flexible tagging of customer Accounts.

Tags allow users to classify customers without modifying the database schema.

Examples:

- VIP
- Strategic
- High Value
- Government
- NGO
- SME
- Wholesale
- Retail
- Priority

---

## Ownership

Owned By:

```text
CRM Module
```

---

## Platform Engine Dependencies

| Platform Engine       | Purpose                  |
| --------------------- | ------------------------ |
| Reference Data Engine | Optional predefined tags |
| Reporting Engine      | Tag analytics            |

---

## Relationships

```text
crm_accounts

│

└── crm_account_tag_assignments

        │

        └── crm_account_tags
```

---

## Key Fields

```text
id

tenant_id

name

description

color

icon

status

created_at

updated_at
```

---

## Business Rules

- Tags are tenant-specific.
- Tags may be reused across multiple Accounts.
- Tags support reporting and filtering.

---

## Events Published

```text
TagCreated

TagUpdated

TagArchived
```

---

# 24. crm_account_tag_assignments

## Purpose

Associates one or more Tags with an Account.

Supports many-to-many relationships.

---

## Ownership

Owned By:

```text
CRM Module
```

---

## Relationships

```text
crm_accounts

│

└── crm_account_tag_assignments

        │

        └── crm_account_tags
```

---

## Key Fields

```text
id

tenant_id

account_id

tag_id

assigned_by

assigned_at
```

---

## Business Rules

- Accounts may have unlimited Tags.
- Duplicate tag assignments are not permitted.

---

## Events Published

```text
TagAssigned

TagRemoved
```

---

# 25. crm_duplicate_candidates

## Purpose

Supports duplicate detection.

Rather than automatically merging customer records, CRM identifies possible duplicates for user review.

---

## Ownership

Owned By:

```text
CRM Module
```

---

## Duplicate Detection Sources

Examples:

- Email
- Phone
- Registration Number
- Tax Identification Number
- National ID
- Similar Name

---

## Key Fields

```text
id

tenant_id

account_id

candidate_account_id

match_score

match_reason

status

reviewed_by

reviewed_at
```

---

## Business Rules

- Duplicate detection is advisory.
- Users decide whether to merge.
- Automatic merging is not performed.

---

## Future Expansion

Future AI capabilities may improve duplicate detection accuracy.

---

# 26. crm_customer_segments

## Purpose

Represents logical customer groups used for reporting, marketing, pricing, and analytics.

Examples:

- SMEs
- Government
- Education
- Healthcare
- Corporate
- NGOs
- Retail
- Wholesale

---

## Ownership

Owned By:

```text
CRM Module
```

Segment definitions may optionally reference the Reference Data Engine.

---

## Key Fields

```text
id

tenant_id

segment_name

description

status
```

---

## Future Integration

Future Marketing Module

Future AI Customer Insights

---

# 27. crm_customer_preferences

## Purpose

Stores customer communication and service preferences.

Examples:

- Preferred Contact Method
- Preferred Language
- Preferred Currency
- Preferred Branch
- Preferred Account Manager

---

## Ownership

Owned By:

```text
CRM Module
```

---

## Platform Engine Dependencies

Reference Data Engine

provides:

- Languages
- Communication Methods
- Currency

---

## Key Fields

```text
id

tenant_id

account_id

preferred_language

preferred_contact_method

preferred_currency

marketing_consent

do_not_contact
```

---

# 28. crm_customer_classifications

## Purpose

Stores business classifications assigned to customers.

Examples:

- Gold
- Silver
- Bronze
- Strategic
- Enterprise
- SME
- Government

Unlike Tags, classifications are generally managed by business policy.

---

## Ownership

Owned By:

```text
CRM Module
```

Values supplied through the Reference Data Engine.

---

# 29. crm_customer_notes

## Purpose

Stores structured internal notes associated with Accounts.

Examples:

- Customer observations
- Sales insights
- Relationship notes
- Visit summaries

---

## Ownership

Owned By:

```text
CRM Module
```

---

## Business Rules

- Notes are internal only.
- Notes are permission-controlled.
- Notes become part of Customer 360.

---

# 30. crm_account_watchers

## Purpose

Allows Platform Users to subscribe to customer Accounts.

Useful for:

- Sales Managers
- Branch Managers
- Customer Success
- Executives

---

## Ownership

Owned By:

```text
CRM Module
```

---

## Key Fields

```text
id

tenant_id

account_id

user_id

created_at
```

---

## Future Integration

Notification Engine

Notifies watchers of important customer events.

---

# 31. crm_account_favorites

## Purpose

Allows users to bookmark frequently accessed customers.

---

## Ownership

Owned By:

```text
CRM Module
```

---

## Key Fields

```text
id

tenant_id

account_id

user_id

created_at
```

---

## Business Rules

Favorites are user-specific.

---

# 32. crm_import_batches

## Purpose

Tracks bulk customer imports.

Supports:

- Excel
- CSV
- API Imports
- Migration Projects

---

## Ownership

Owned By:

```text
CRM Module
```

---

## Key Fields

```text
id

tenant_id

batch_number

source

status

total_records

successful_records

failed_records

started_at

completed_at
```

---

## Platform Engine Dependencies

| Platform Engine           | Purpose                         |
| ------------------------- | ------------------------------- |
| Document Numbering Engine | Import Batch Number             |
| Activity & Audit Engine   | Import Audit                    |
| Notification Engine       | Import Completion Notifications |

---

# 33. Integration Tables

The CRM Module maintains lightweight integration tables that reference external modules without duplicating their data.

These include:

```text
crm_account_finance_links

crm_account_sales_links

crm_account_project_links

crm_account_support_links

crm_account_document_links
```

The purpose of these tables is to maintain relationships between CRM Accounts and records owned by other Business Modules.

CRM never stores external business data.

Only references.

---

# 34. crm_account_finance_links

## Purpose

Links CRM Accounts with customer financial accounts in the Finance Engine.

---

## Ownership

CRM owns:

```text
account_id
```

Finance owns:

```text
finance_customer_account_id
```

---

## Key Fields

```text
id

tenant_id

account_id

finance_customer_account_id

finance_account_number

status

linked_at
```

---

## Business Rules

CRM must never store:

- Ledger Entries
- Receivables
- Payments
- Customer Balances

Those remain owned by the Finance Engine.

---

# 35. crm_account_sales_links

## Purpose

Maintains references between CRM Opportunities and Sales documents.

---

## Ownership

CRM owns:

- Account reference
- Opportunity reference

Sales owns:

- Quotations
- Orders
- Deliveries
- Commercial documents

---

## Key Fields

```text
id

tenant_id

account_id

opportunity_id

sales_record_type

sales_record_id

sales_record_number

linked_at
```

---

## Sales Record Types

```text
Quotation

Sales Order

Delivery

Invoice
```

---

# 36. crm_account_project_links

## Purpose

Links customer Accounts with Projects.

Projects remain owned by the Projects Module.

---

# 37. crm_account_support_links

## Purpose

Links customer Accounts with Support Cases and Tickets.

Ticket ownership remains within the Customer Support Module.

---

# 38. crm_account_document_links

## Purpose

Maintains references to customer documents stored by the Document Management Engine.

CRM stores only document references.

The Document Management Engine owns:

- Storage
- Versioning
- Metadata
- Security
- Downloads
- Previews

---

# 39. Integration Philosophy

The CRM database follows a strict ownership model.

```text
CRM

↓

Customer Relationship

↓

Sales

↓

Commercial Transactions

↓

Finance

↓

Financial Transactions
```

Each module owns its business domain.

Modules communicate using:

- Platform Event Bus
- APIs
- Integration References

This architecture eliminates duplication while ensuring every module remains independently scalable and maintainable.

---

# 40. Reference Data Dependencies

The CRM Module relies extensively on the Reference Data Engine to provide configurable business values.

CRM must never hardcode business lookup values that may vary between tenants or evolve over time.

All reference values should be centrally managed through the Reference Data Engine.

---

## Reference Data Categories

The following values are supplied by the Reference Data Engine.

| Category                  | Examples                             |
| ------------------------- | ------------------------------------ |
| Account Types             | Organization, Individual, Partner    |
| Customer Lifecycle Stages | Lead, Prospect, Customer, VIP        |
| Customer Categories       | Retail, Wholesale, Government        |
| Customer Tiers            | Platinum, Gold, Silver, Bronze       |
| Industries                | Banking, Manufacturing, Education    |
| Lead Sources              | Website, Referral, Campaign          |
| Opportunity Stages        | Qualification, Proposal, Negotiation |
| Activity Types            | Call, Meeting, Follow-up             |
| Communication Types       | Email, SMS, Phone Call               |
| Contact Roles             | Director, Procurement Officer        |
| Address Types             | Billing, Shipping, Physical          |
| Relationship Types        | Parent, Subsidiary, Distributor      |
| Priority Levels           | Low, Medium, High, Critical          |
| Status Values             | Active, Inactive, Archived           |
| Win Reasons               | Price, Relationship, Product Fit     |
| Loss Reasons              | Budget, Competitor, Timing           |
| Languages                 | English, French, Swahili             |
| Currencies                | UGX, USD, EUR                        |

---

## Reference Data Rules

The CRM Module shall:

- Never duplicate lookup values.
- Consume reference values through APIs or cached reference data.
- Support tenant-specific configuration where permitted.
- Support future localization and regionalization.

---

# 41. Platform Engine Dependencies

The CRM Module depends on shared Platform Engines.

These dependencies must be maintained through well-defined contracts.

| Platform Engine            | CRM Dependency                          |
| -------------------------- | --------------------------------------- |
| Platform Core              | Users, Tenants, Branches, Organizations |
| Authorization Engine       | Permissions, Policies                   |
| Workflow Engine            | Lead and Customer Approval              |
| Document Numbering Engine  | Customer, Lead and Opportunity Numbers  |
| Document Management Engine | Customer Documents                      |
| Notification Engine        | Communication Delivery                  |
| Reference Data Engine      | Business Lookup Values                  |
| Search & Indexing Engine   | Global Search                           |
| Reporting Engine           | Dashboards and Reports                  |
| Activity & Audit Engine    | Audit History                           |
| Platform Event Bus         | Business Events                         |

CRM consumes these services and never reimplements them.

---

# 42. Business Module Dependencies

The CRM Module integrates with multiple Business Modules while respecting ownership boundaries.

## Finance Engine

CRM consumes:

- Customer Financial Account
- Customer Balance
- Receivable Summary
- Payment Summary
- Credit Status

Finance owns all accounting records.

---

## Sales Module

CRM consumes:

- Quotations
- Sales Orders
- Invoice Summary
- Sales Progress

Sales owns all commercial transactions.

---

## Procurement Module

CRM may reference:

- Business Partners
- Supplier Relationships (Future)
- Strategic Partners

Procurement owns supplier management.

---

## Customer Support Module

CRM consumes:

- Tickets
- Cases
- SLA Status
- Customer Satisfaction

Customer Support owns all ticketing operations.

---

## Projects Module

CRM consumes:

- Customer Projects
- Project Status
- Milestones
- Delivery Progress

Projects owns project execution.

---

## Marketing Module (Future)

CRM may integrate with:

- Campaigns
- Segments
- Customer Engagement
- Lead Nurturing
- Marketing Automation

---

# 43. Database Relationship Overview

The CRM database follows an Account-Centric model.

```text
                          crm_accounts
                                │
      ┌─────────────────────────┼──────────────────────────┐
      │                         │                          │
crm_contacts              crm_addresses          crm_opportunities
      │                                                 │
      │                                          crm_activities
      │                                                 │
      │                                          crm_communications
      │                                                 │
      └──────────────────────────────┬──────────────────┘
                                     │
                          crm_timeline_entries
                                     │
             ┌──────────────┬─────────┼───────────┬──────────────┐
             │              │         │           │              │
         Finance        Sales     Support     Projects     Documents
```

This structure ensures a single customer identity while enabling Customer 360 composition across the platform.

---

# 44. Multi-Tenant Strategy

Every CRM record belongs to a single tenant.

All CRM business tables must contain:

```text
tenant_id
```

Tenant isolation is enforced through:

- Platform Core Tenant Context
- PostgreSQL Row Level Security (RLS)
- Authorization Engine Policies
- API Validation
- Query Scoping

Cross-tenant access is prohibited.

---

# 45. Branch Strategy

The CRM Module supports branch-level operations.

Applicable entities include:

- Accounts
- Opportunities
- Activities
- Communications

Branch ownership enables:

- Regional customer management
- Branch reporting
- Territory assignment
- Sales team organization

Branch access is enforced through the Authorization Engine.

---

# 46. Numbering Strategy

Business identifiers are generated by the Document Numbering Engine.

CRM must never generate business numbers directly.

The following identifiers are requested from the Document Numbering Engine:

| Entity       | Number              |
| ------------ | ------------------- |
| Customer     | Customer Number     |
| Lead         | Lead Number         |
| Opportunity  | Opportunity Number  |
| Import Batch | Import Batch Number |

Examples:

```text
CUS-000001

LED-000001

OPP-000001

IMP-000001
```

Number formats remain tenant configurable.

---

# 47. Search Strategy

The CRM Module integrates with the Search & Indexing Engine.

Searchable entities include:

- Accounts
- Contacts
- Opportunities
- Activities
- Communications

Searchable fields include:

- Customer Number
- Customer Name
- Legal Name
- Contact Name
- Email
- Phone
- Registration Number
- Tax Number
- Opportunity Number

Search results must respect:

- Tenant Isolation
- Branch Security
- Authorization Policies
- Record Ownership

---

# 48. Audit Strategy

The CRM Module publishes audit events to the Activity & Audit Engine.

Examples include:

- Account Created
- Account Updated
- Contact Added
- Contact Updated
- Lead Qualified
- Lead Converted
- Opportunity Won
- Opportunity Lost
- Activity Completed
- Customer Archived

CRM does not maintain its own audit store.

The Activity & Audit Engine is the system of record for audit history.

---

# 49. Event Strategy

CRM communicates with the rest of the Business Suite using the Platform Event Bus.

## Published Events

```text
AccountCreated

AccountUpdated

LeadCreated

LeadQualified

LeadConverted

CustomerCreated

OpportunityCreated

OpportunityWon

OpportunityLost

ContactCreated

ActivityCreated

CommunicationRecorded
```

---

## Consumed Events

Examples include:

```text
CustomerFinancialAccountCreated

QuotationCreated

SalesOrderCreated

InvoiceIssued

PaymentReceived

SupportTicketCreated

ProjectCreated

WorkflowCompleted
```

The CRM Module responds to these events to update Customer 360 while preserving ownership in the originating module.

---

# 50. Performance Strategy

The CRM database is designed for enterprise scalability.

Recommended practices include:

- Appropriate indexing
- Pagination for large datasets
- Lazy loading of related entities
- Background processing for imports
- Optimized Customer 360 queries
- Read-optimized reporting views
- Event-driven synchronization

The Customer Workspace should remain responsive even for customers with extensive history.

---

# 51. Scalability Strategy

The CRM Module should support:

- Thousands of concurrent users
- Millions of customer records
- Millions of activities
- Large communication histories
- High-volume integrations
- Multi-region deployments
- Horizontal application scaling

The database design should avoid bottlenecks that require future redesign.

---

# 52. Security Strategy

Database security is enforced through multiple layers.

The CRM Module relies on:

- Platform Core
- Authorization Engine
- PostgreSQL Row Level Security
- Branch Security
- Field-Level Security
- Activity & Audit Engine

Sensitive information such as tax identifiers and national identification numbers should only be accessible to authorized users.

CRM must never bypass Platform security mechanisms.

---

# 53. Future Expansion

The CRM database has been designed to support future capabilities without structural redesign.

Future enhancements include:

- Marketing Automation
- Customer Loyalty
- Customer Portal
- AI Lead Scoring
- AI Opportunity Forecasting
- AI Customer Health
- Customer Journey Analytics
- Territory Management
- Multi-Brand Organizations
- Omnichannel Engagement
- Social CRM
- Relationship Intelligence

These capabilities should build upon the existing Account-Centric architecture.

---

# 54. Database Summary

The CRM Module database establishes the Customer Master domain for the Business Suite Enterprise Platform.

By adopting an Account-Centric architecture, the CRM Module maintains a single customer identity throughout the customer lifecycle while integrating seamlessly with Platform Engines and Business Modules.

The design is built around clear ownership boundaries:

- CRM owns customer relationships.
- Sales owns commercial transactions.
- Finance owns financial transactions.
- Customer Support owns service interactions.
- Projects owns project execution.
- Platform Engines provide reusable enterprise services.

This architecture enables a unified Customer 360 experience, eliminates data duplication, supports enterprise scalability, enforces multi-tenant security, and provides a robust foundation for future Business Suite capabilities.
