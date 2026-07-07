# Sales Module - ARCHITECTURE.md

> Business Suite Enterprise Platform

---

# 1. Architecture Overview

The Sales Module is the commercial execution layer of the Business Suite Enterprise Platform.

It manages sales planning, pricing, pipeline execution, targets, forecasting, sales performance, and commercial coordination while relying on other modules and Platform Engines for customer ownership, document issuance, stock control, accounting, approvals, notifications, audit, reporting, and search.

---

# 2. Architectural Position

```text
CRM
    │
    ▼
Sales Module
    │
    ▼
Sales Documents Module
    │
    ▼
Inventory
    │
    ▼
Finance
```

The Sales Module sits between CRM and Sales Documents.

CRM owns customer relationships and opportunities.

Sales owns the commercial execution process.

Sales Documents owns official documents.

Inventory owns stock and fulfillment.

Finance owns receivables, payments, receipts, and accounting impact.

---

# 3. Core Architecture Principle

```text
Sales owns commercial execution.
CRM owns customer relationship context.
Sales Documents owns official document issuance.
Inventory owns stock control.
Finance owns accounting impact.
Platform Engines own shared enterprise services.
```

---

# 4. High-Level Sales Flow

```text
Qualified Opportunity
        │
        ▼
Sales Pipeline Entry
        │
        ▼
Pricing Review
        │
        ▼
Discount Request
        │
        ▼
Quotation Request
        │
        ▼
Customer Negotiation
        │
        ▼
Customer Acceptance
        │
        ▼
Sales Order Request
        │
        ▼
Inventory Coordination
        │
        ▼
Delivery Coordination
        │
        ▼
Invoice Request
        │
        ▼
Finance Receivable
        │
        ▼
Sales Performance Update
```

---

# 5. Main Architectural Components

The Sales Module consists of the following components:

- Sales Pipeline Service
- Sales Planning Service
- Sales Team Service
- Sales Territory Service
- Sales Target Service
- Pricing Service
- Discount Management Service
- Promotion Service
- Sales Forecasting Service
- Sales Performance Service
- Commission Service
- Customer Sales Insight Service
- Sales Coordination Service
- Sales Event Publisher
- Sales Integration Layer

---

# 6. Sales Pipeline Service

The Sales Pipeline Service manages sales movement after CRM qualification.

Responsibilities:

- Track qualified opportunities
- Manage sales stages
- Track sales probability
- Track estimated revenue
- Track expected close date
- Track next action
- Track customer decision status
- Update pipeline progress
- Provide pipeline visibility
- Support pipeline reporting

CRM owns the original opportunity record.

Sales owns the commercial pipeline execution after qualification.

---

# 7. Sales Planning Service

The Sales Planning Service supports structured sales execution.

Responsibilities:

- Define sales plans
- Assign sales objectives
- Plan customer engagement
- Plan sales campaigns
- Plan product focus areas
- Plan regional sales activities
- Align targets with sales strategy

---

# 8. Sales Team Service

The Sales Team Service manages sales organization structures.

Responsibilities:

- Define sales teams
- Assign sales representatives
- Assign sales managers
- Assign branch sales users
- Manage team hierarchy
- Track team performance
- Support team-based reporting

User identity is provided by Platform Core.

Permissions are enforced by the Authorization Engine.

---

# 9. Sales Territory Service

The Sales Territory Service manages geographic or business-based sales coverage.

Responsibilities:

- Define territories
- Assign territories to teams
- Assign territories to users
- Link territories to regions
- Link territories to branches
- Track territory revenue
- Track territory performance

Territories are configurable per tenant using Reference Data.

---

# 10. Sales Target Service

The Sales Target Service manages targets and quotas.

Responsibilities:

- Create sales targets
- Assign targets to users
- Assign targets to teams
- Assign targets to branches
- Assign targets to territories
- Track target achievement
- Track target variance
- Support target approvals
- Support target revision history

Targets may be based on:

- Revenue
- Quantity
- Product category
- Customer segment
- Territory
- Branch
- Sales channel

---

# 11. Pricing Service

The Pricing Service manages commercial pricing rules.

Responsibilities:

- Manage price lists
- Manage customer-specific pricing
- Manage product pricing
- Manage service pricing
- Manage branch pricing
- Manage promotional pricing
- Support effective dates
- Support currency rules
- Support price history
- Support pricing approval workflows

Pricing changes may require workflow approval.

---

# 12. Discount Management Service

The Discount Management Service controls discount requests and approvals.

Responsibilities:

- Capture discount requests
- Validate discount limits
- Route discount approvals
- Track approval history
- Enforce approved discount values
- Prevent unauthorized discounts
- Support discount analytics

Discount approval thresholds are configurable per tenant.

---

# 13. Promotion Service

The Promotion Service manages temporary sales campaigns and offers.

Responsibilities:

- Create promotions
- Define promotion periods
- Define eligible products
- Define eligible customer segments
- Define discount rules
- Define branch or territory applicability
- Track promotion performance
- Retire expired promotions

Promotions may require approval before activation.

---

# 14. Sales Forecasting Service

The Sales Forecasting Service estimates future revenue.

Responsibilities:

- Build sales forecasts
- Track expected revenue
- Track weighted pipeline
- Track committed revenue
- Track best-case revenue
- Track forecast revisions
- Compare forecast against actuals
- Support management review

Forecasts may be generated by:

- Salesperson
- Team
- Branch
- Territory
- Product category
- Customer segment
- Sales channel

---

# 15. Sales Performance Service

The Sales Performance Service tracks actual commercial results.

Responsibilities:

- Track sales volume
- Track sales value
- Track conversion rates
- Track average deal size
- Track target achievement
- Track customer retention
- Track sales cycle duration
- Track revenue by branch
- Track revenue by salesperson
- Track revenue by territory

Actual revenue is derived from issued invoices and finance records.

---

# 16. Commission Service

The Commission Service manages sales commissions and incentives.

Responsibilities:

- Define commission rules
- Calculate commission eligibility
- Track commissionable sales
- Support approval of commissions
- Support commission adjustments
- Provide commission reports

Commission payment processing remains owned by Finance or Payroll where applicable.

---

# 17. Customer Sales Insight Service

The Customer Sales Insight Service provides commercial customer intelligence.

Responsibilities:

- View sales history
- Analyze buying patterns
- Track customer revenue
- Track customer profitability
- Track product preferences
- Track quotation conversion
- Track outstanding sales opportunities
- Support upsell and cross-sell insights

CRM displays customer insight, but Sales calculates commercial sales performance.

---

# 18. Sales Coordination Service

The Sales Coordination Service coordinates downstream business operations.

Responsibilities:

- Request quotations from Sales Documents
- Request sales orders from Sales Documents
- Request inventory availability checks
- Request stock reservation
- Request delivery coordination
- Request invoice generation
- Track fulfillment progress
- Track invoice status
- Track payment status

This service connects sales execution to operational fulfillment.

---

# 19. Sales Integration Layer

The Sales Integration Layer connects the Sales Module to:

- CRM Module
- Sales Documents Module
- Inventory Module
- Finance Module
- Platform Core
- Authorization Engine
- Workflow Engine
- Notification Engine
- Reference Data Engine
- Activity & Audit Engine
- Search & Indexing Engine
- Reporting Engine
- Platform Event Bus

---

# 20. CRM Integration

CRM provides:

- Leads
- Accounts
- Customers
- Contacts
- Opportunities
- Activities
- Customer Timeline
- Customer 360

Sales consumes CRM information but does not own CRM records.

Sales updates CRM with:

- Pipeline stage changes
- Sales progress
- Customer negotiation updates
- Sales outcomes
- Customer buying history
- Lost reasons
- Revenue summaries

---

# 21. Sales Documents Integration

Sales requests official documents from the Sales Documents Module.

Examples:

```text
Quotation Request
Sales Order Request
Delivery Note Request
Invoice Request
```

Sales Documents owns:

- Official document lifecycle
- Document number generation request
- QR code
- Verification code
- PDF generation
- Official document storage
- Document security
- Document issuance

Sales owns the business reason behind the document request.

---

# 22. Inventory Integration

Inventory provides:

- Item master
- Product availability
- Stock levels
- Warehouse information
- Store information
- Reservations
- Delivery status
- Stock movements

Sales requests inventory actions but does not update stock directly.

---

# 23. Finance Integration

Finance provides:

- Customer financial account
- Credit limit
- Receivables
- Payments
- Receipts
- Outstanding balance
- Ledger postings
- Revenue records

Sales uses Finance summaries for commercial decisions.

Finance owns all accounting impact.

---

# 24. Platform Core Integration

Platform Core provides:

- Tenant
- Company
- Branch
- Department
- Business Unit
- User
- Organization profile

Used for:

- Sales team assignment
- Branch-based targets
- Territory ownership
- User context
- Company reporting

---

# 25. Authorization Engine Integration

The Authorization Engine enforces:

- Menu access
- Record access
- Field access
- Team visibility
- Branch visibility
- Territory visibility
- Approval authority

Example permissions:

```text
sales.pipeline.view
sales.pipeline.manage
sales.target.view
sales.target.manage
sales.pricing.view
sales.pricing.manage
sales.discount.request
sales.discount.approve
sales.forecast.view
sales.forecast.manage
sales.team.manage
sales.performance.view
```

---

# 26. Workflow Engine Integration

The Workflow Engine controls approvals for:

- Discount approvals
- Pricing changes
- Target approvals
- Commission approvals
- Promotion approvals
- Territory reassignment
- Sales plan approvals

Sales submits workflow requests but does not own approval logic.

---

# 27. Reference Data Engine Integration

The Reference Data Engine provides configurable values including:

- Sales Channels
- Sales Regions
- Sales Territories
- Sales Stages
- Sales Statuses
- Pricing Methods
- Discount Types
- Promotion Types
- Target Types
- Commission Types
- Lost Reasons
- Customer Segments

No lookup values are hardcoded.

---

# 28. Notification Engine Integration

The Notification Engine sends:

- Target assignment notifications
- Discount approval alerts
- Pricing approval alerts
- Pipeline reminders
- Forecast reminders
- Promotion alerts
- Sales performance alerts
- Customer follow-up reminders

Sales stores notification references only.

---

# 29. Activity & Audit Engine Integration

The Activity & Audit Engine records:

- Pipeline updates
- Pricing changes
- Discount requests
- Discount approvals
- Target assignments
- Forecast revisions
- Territory changes
- Promotion activations
- Commission calculations

All critical sales actions are auditable.

---

# 30. Search & Indexing Engine Integration

The Search & Indexing Engine indexes:

- Customers
- Pipeline records
- Sales targets
- Sales teams
- Territories
- Price lists
- Promotions
- Forecasts

Search respects:

- Tenant isolation
- Branch restrictions
- Team visibility
- User permissions

---

# 31. Reporting Engine Integration

The Reporting Engine produces:

- Sales dashboard
- Sales pipeline report
- Sales forecast report
- Sales target report
- Sales performance report
- Territory performance report
- Salesperson performance report
- Product sales analysis
- Customer sales analysis
- Discount report
- Promotion performance report
- Commission report

---

# 32. Platform Event Bus Integration

The Sales Module publishes events such as:

```text
SalesPipelineCreated
SalesPipelineUpdated
SalesStageChanged

SalesTargetAssigned
SalesTargetUpdated

PricingChanged
DiscountRequested
DiscountApproved
DiscountRejected

PromotionActivated
PromotionExpired

SalesForecastCreated
SalesForecastUpdated

SalesOrderRequested
InvoiceRequested

SalesCompleted
SalesLost
```

Other modules subscribe to relevant events.

---

# 33. Sales Pipeline Architecture

```text
CRM Opportunity
        │
        ▼
Sales Pipeline Record
        │
        ▼
Sales Stage
        │
        ▼
Pricing Review
        │
        ▼
Quotation Request
        │
        ▼
Customer Decision
        │
        ▼
Sales Outcome
```

The pipeline tracks execution without duplicating CRM ownership.

---

# 34. Pricing Architecture

```text
Item / Service
        │
        ▼
Price List
        │
        ▼
Customer / Segment Rules
        │
        ▼
Promotion Rules
        │
        ▼
Discount Rules
        │
        ▼
Final Sales Price
```

Pricing must support:

- Tenant pricing
- Branch pricing
- Customer pricing
- Segment pricing
- Currency pricing
- Time-based pricing
- Promotion pricing

---

# 35. Discount Architecture

```text
Discount Requested
        │
        ▼
Threshold Check
        │
        ├── Within Limit
        │       ▼
        │   Auto Approved
        │
        └── Above Limit
                ▼
            Workflow Approval
```

Approved discounts are passed to Sales Documents when quotation or invoice requests are created.

---

# 36. Forecasting Architecture

```text
Pipeline Value
        │
        ▼
Probability Weighting
        │
        ▼
Expected Close Date
        │
        ▼
Forecast Period
        │
        ▼
Forecast Amount
```

Forecasts are compared against:

- Issued sales orders
- Issued invoices
- Finance revenue summaries

---

# 37. Sales Performance Architecture

```text
Targets
        │
        ▼
Actual Sales
        │
        ▼
Variance
        │
        ▼
Performance Score
```

Actual sales values come from issued documents and Finance records.

---

# 38. Commission Architecture

```text
Commission Rule
        │
        ▼
Eligible Sale
        │
        ▼
Calculation
        │
        ▼
Approval
        │
        ▼
Finance / Payroll Processing
```

Sales calculates eligibility.

Finance or Payroll handles payment.

---

# 39. Data Ownership Summary

| Data / Capability        | Owner                            |
| ------------------------ | -------------------------------- |
| Leads                    | CRM                              |
| Opportunities            | CRM                              |
| Customers                | CRM                              |
| Sales Pipeline Execution | Sales                            |
| Sales Targets            | Sales                            |
| Sales Teams              | Sales                            |
| Territories              | Sales                            |
| Pricing                  | Sales                            |
| Discounts                | Sales                            |
| Promotions               | Sales                            |
| Forecasts                | Sales                            |
| Official Quotations      | Sales Documents                  |
| Official Sales Orders    | Sales Documents                  |
| Delivery Notes           | Sales Documents                  |
| Invoices                 | Sales Documents / Finance Impact |
| Stock                    | Inventory                        |
| Receivables              | Finance                          |
| Payments                 | Finance                          |
| Receipts                 | Finance                          |
| Ledger Entries           | Finance                          |
| Reports                  | Reporting Engine                 |
| Search Indexes           | Search & Indexing Engine         |
| Audit Logs               | Activity & Audit Engine          |

---

# 40. Architecture Benefits

This architecture provides:

- Clear CRM-to-Sales transition
- Clean separation from official documents
- Strong pricing control
- Controlled discount approvals
- Better sales visibility
- Accurate sales forecasting
- Strong sales performance tracking
- Reliable downstream coordination
- Enterprise reporting
- Multi-tenant scalability
- Future AI readiness

---

# 41. Next Document

The next specification document is:

```text
DATABASE.md
```

This document will define:

- Conceptual data model
- Sales pipeline tables
- Sales teams
- Sales targets
- Territories
- Pricing entities
- Discount entities
- Promotion entities
- Forecasting entities
- Commission entities
- Engine references
- Tenant and branch isolation
- No SQL implementation yet
