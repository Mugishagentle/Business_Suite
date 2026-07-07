# Sales Module - DATABASE.md

> Business Suite Enterprise Platform

---

# 1. Database Overview

The Sales Module database stores commercial execution data required to manage sales pipelines, sales teams, territories, targets, pricing, discounts, promotions, forecasts, commissions, and customer sales insights.

The database does **not** own customer master records, official business documents, inventory stock balances, accounting records, document files, approvals, notifications, audit logs, or reports.

Those capabilities are owned by their respective Business Suite modules and Platform Engines.

---

# 2. Database Design Principles

The Sales Module database follows these principles:

- Multi-tenant by design
- Branch-aware where applicable
- Commercial execution focused
- Engine-integrated
- Auditable
- Normalized
- Extensible
- Soft-delete capable
- Event-driven
- Reporting-ready
- Search-ready
- No duplication of external module ownership

---

# 3. Data Ownership

The Sales Module owns:

- Sales Pipeline Records
- Sales Pipeline History
- Sales Stage History
- Sales Teams
- Sales Team Members
- Sales Territories
- Sales Territory Assignments
- Sales Targets
- Sales Target Assignments
- Price Lists
- Price List Items
- Customer Pricing Rules
- Discount Requests
- Promotions
- Promotion Items
- Sales Forecasts
- Sales Performance Metrics
- Commission Rules
- Commission Calculations
- Customer Sales Insights

The Sales Module references, but does not own:

- Leads
- Opportunities
- Customers
- Contacts
- Quotations
- Sales Orders
- Delivery Notes
- Invoices
- Receipts
- Stock Balances
- Warehouses
- Stores
- Receivables
- Payments
- Ledger Entries

---

# 4. External Module References

## CRM Module

References:

- Lead
- Opportunity
- Customer
- Contact
- Account
- Customer Timeline
- Customer 360

CRM remains the owner of customer relationship data.

---

## Sales Documents Module

References:

- Quotation
- Sales Order
- Delivery Note
- Sales Invoice

Sales Documents remains the owner of official business documents, document lifecycle, numbering, PDFs, QR codes, and verification.

---

## Inventory Module

References:

- Item
- Item Category
- Unit of Measure
- Warehouse
- Store
- Reservation
- Fulfillment Status

Inventory remains the owner of stock and fulfillment data.

---

## Finance Module

References:

- Customer Financial Account
- Credit Limit
- Outstanding Balance
- Receivable
- Payment
- Receipt
- Revenue Summary
- Ledger Posting

Finance remains the owner of accounting and financial impact.

---

# 5. Platform Engine References

## Platform Core

References:

- Tenant
- Company
- Branch
- Department
- Business Unit
- User

---

## Authorization Engine

References:

- Role
- Permission
- Policy

---

## Workflow Engine

References:

- Workflow Definition
- Workflow Instance
- Approval Stage
- Approval Decision

---

## Reference Data Engine

References:

- Sales Stage
- Sales Status
- Sales Channel
- Sales Region
- Sales Territory Type
- Customer Segment
- Pricing Method
- Discount Type
- Promotion Type
- Target Type
- Commission Type
- Lost Reason

---

## Notification Engine

References:

- Notification
- Delivery History
- Message Template

---

## Activity & Audit Engine

References:

- Audit Event
- Activity History
- Change History

---

## Search & Indexing Engine

References:

- Search Index
- Indexed Metadata

---

## Reporting Engine

References:

- Report Definition
- Dashboard Definition
- Export Job

---

# 6. Core Entities

The Sales Module consists of the following core entities:

- SalesPipeline
- SalesPipelineHistory
- SalesStageHistory
- SalesTeam
- SalesTeamMember
- SalesTerritory
- SalesTerritoryAssignment
- SalesTarget
- SalesTargetAssignment
- PriceList
- PriceListItem
- CustomerPricing
- DiscountRequest
- Promotion
- PromotionItem
- SalesForecast
- SalesPerformance
- CommissionRule
- CommissionCalculation
- CustomerSalesInsight

---

# 7. SalesPipeline

Represents an active commercial sales process linked to a CRM opportunity.

Contains:

- Tenant Reference
- Company Reference
- Branch Reference
- Opportunity Reference
- Customer Reference
- Contact Reference
- Sales Representative
- Sales Team
- Territory
- Sales Channel
- Current Stage
- Probability
- Estimated Revenue
- Weighted Revenue
- Expected Close Date
- Next Follow-Up Date
- Last Activity Date
- Status
- Lost Reason Reference
- Created By
- Created Date

Business Rules:

- A Sales Pipeline record must reference a CRM Opportunity.
- The CRM Opportunity remains owned by CRM.
- Pipeline progress may update the CRM Customer Timeline.
- Pipeline history must be preserved.
- Closed pipeline records become read-only except through authorized reopening policies.

---

# 8. SalesPipelineHistory

Stores historical changes to pipeline records.

Contains:

- Pipeline Reference
- Previous Stage
- New Stage
- Previous Probability
- New Probability
- Previous Estimated Revenue
- New Estimated Revenue
- Change Reason
- Changed By
- Changed Date

Business Rules:

- Pipeline history is immutable.
- Every stage movement must create a history record.
- Pipeline history supports sales cycle analysis and forecasting accuracy.

---

# 9. SalesStageHistory

Tracks how long a pipeline stayed in each stage.

Contains:

- Pipeline Reference
- Stage Reference
- Entered Date
- Exited Date
- Duration
- Responsible User
- Status

Business Rules:

- Stage duration is used for sales performance analysis.
- Stage history supports bottleneck reporting.
- Stage records cannot be deleted after creation.

---

# 10. SalesTeam

Represents a group of users responsible for sales execution.

Contains:

- Tenant Reference
- Company Reference
- Branch Reference
- Team Name
- Team Code
- Team Manager
- Territory Reference
- Status
- Description

Business Rules:

- A team may belong to a branch, region, or territory.
- A team may have one manager.
- Team visibility is controlled by the Authorization Engine.

---

# 11. SalesTeamMember

Stores membership of users in sales teams.

Contains:

- Sales Team Reference
- User Reference
- Team Role
- Start Date
- End Date
- Active Flag
- Assigned By
- Assigned Date

Business Rules:

- Users originate from Platform Core.
- A user may belong to multiple teams if tenant policy allows.
- Historical membership must be preserved.

---

# 12. SalesTerritory

Defines sales coverage areas.

Contains:

- Tenant Reference
- Company Reference
- Branch Reference
- Territory Name
- Territory Code
- Region Reference
- Territory Type
- Status
- Description

Business Rules:

- Territories are configurable per tenant.
- Territories may be geographic, branch-based, product-based, or customer-segment-based.
- Territory security may restrict record visibility.

---

# 13. SalesTerritoryAssignment

Assigns territories to teams or users.

Contains:

- Territory Reference
- Sales Team Reference
- User Reference
- Assignment Type
- Effective From
- Effective To
- Active Flag
- Assigned By

Business Rules:

- Territory assignments are effective-date driven.
- Historical assignments must be retained.
- A territory may be assigned to a team, user, or branch depending on configuration.

---

# 14. SalesTarget

Represents a sales objective for a defined period.

Contains:

- Tenant Reference
- Company Reference
- Branch Reference
- Target Name
- Target Type
- Target Period
- Currency
- Target Amount
- Target Quantity
- Start Date
- End Date
- Workflow Reference
- Approval Status
- Status

Target types may include:

- Revenue
- Quantity
- Product Category
- Customer Segment
- Territory
- Branch
- Sales Channel

---

# 15. SalesTargetAssignment

Assigns a target to a user, team, branch, territory, or product category.

Contains:

- Sales Target Reference
- Assignment Type
- Assignment Reference
- Assigned Amount
- Assigned Quantity
- Effective From
- Effective To
- Status

Business Rules:

- Assigned target values must not exceed the parent target unless tenant policy allows.
- Historical target assignments must be preserved.
- Closed target periods are read-only.

---

# 16. PriceList

Represents a pricing structure used by Sales.

Contains:

- Tenant Reference
- Company Reference
- Branch Reference
- Price List Name
- Price List Code
- Currency
- Pricing Method
- Effective From
- Effective To
- Workflow Reference
- Approval Status
- Status

Business Rules:

- Multiple price lists may exist per tenant.
- Only approved and active price lists may be used for pricing.
- Expired price lists become read-only.
- Price history must be preserved.

---

# 17. PriceListItem

Stores item-level pricing.

Contains:

- Price List Reference
- Item Reference
- Unit of Measure Reference
- Unit Price
- Minimum Quantity
- Maximum Quantity
- Effective From
- Effective To
- Status

Business Rules:

- Item references come from Inventory.
- Unit of Measure references come from Reference Data or Inventory.
- Historical prices cannot be overwritten after use in issued documents.

---

# 18. CustomerPricing

Stores customer-specific pricing overrides.

Contains:

- Tenant Reference
- Customer Reference
- Item Reference
- Price List Reference
- Special Price
- Discount Reference
- Currency
- Effective From
- Effective To
- Workflow Reference
- Approval Status
- Status

Business Rules:

- Customer pricing overrides general price lists where applicable.
- Customer pricing may require approval.
- Expired customer pricing cannot be used.
- Historical customer pricing remains preserved.

---

# 19. DiscountRequest

Represents a requested commercial discount.

Contains:

- Tenant Reference
- Branch Reference
- Pipeline Reference
- Customer Reference
- Item Reference
- Requested Discount Type
- Requested Discount Value
- Approved Discount Value
- Request Reason
- Workflow Reference
- Approval Status
- Requested By
- Approved By
- Status

Business Rules:

- Discounts above threshold require workflow approval.
- Approved discounts become read-only.
- Rejected discounts cannot be applied.
- Discount history must be retained.

---

# 20. Promotion

Represents a sales promotion or campaign.

Contains:

- Tenant Reference
- Company Reference
- Branch Reference
- Promotion Name
- Promotion Code
- Promotion Type
- Sales Channel
- Customer Segment
- Territory Reference
- Start Date
- End Date
- Workflow Reference
- Approval Status
- Status

Business Rules:

- Promotions must have effective dates.
- Promotions may apply by branch, territory, channel, product, or customer segment.
- Expired promotions are excluded from pricing.
- Promotions may require approval before activation.

---

# 21. PromotionItem

Stores products or services included in a promotion.

Contains:

- Promotion Reference
- Item Reference
- Discount Type
- Discount Value
- Promotional Price
- Minimum Quantity
- Maximum Quantity
- Status

Business Rules:

- Promotion items must belong to an active promotion.
- Promotional pricing must respect approval rules.
- Expired promotion items are read-only.

---

# 22. SalesForecast

Represents future expected revenue.

Contains:

- Tenant Reference
- Company Reference
- Branch Reference
- Forecast Period
- Salesperson Reference
- Sales Team Reference
- Territory Reference
- Sales Channel
- Expected Revenue
- Weighted Revenue
- Committed Revenue
- Best Case Revenue
- Forecast Version
- Created By
- Created Date
- Status

Business Rules:

- Forecasts are versioned.
- Forecast changes are audited.
- Forecasts are calculated from pipeline values and probabilities.
- Forecasts may be compared to actual invoices and finance revenue summaries.

---

# 23. SalesPerformance

Stores calculated sales performance metrics.

Contains:

- Tenant Reference
- Company Reference
- Branch Reference
- Salesperson Reference
- Sales Team Reference
- Territory Reference
- Period
- Revenue
- Quantity Sold
- Target Amount
- Target Achievement
- Conversion Rate
- Average Deal Size
- Sales Cycle Duration
- Win Rate
- Customer Retention Rate

Business Rules:

- Performance values are calculated from Sales Documents, CRM, and Finance.
- Performance snapshots may be periodically generated.
- Historical performance records are preserved.

---

# 24. CommissionRule

Defines commission and incentive rules.

Contains:

- Tenant Reference
- Rule Name
- Rule Code
- Commission Type
- Calculation Method
- Percentage
- Fixed Amount
- Minimum Sales Amount
- Product Category Reference
- Effective From
- Effective To
- Workflow Reference
- Approval Status
- Status

Business Rules:

- Commission rules may require approval.
- Expired rules are read-only.
- Commission payment processing is owned by Finance or Payroll.

---

# 25. CommissionCalculation

Stores calculated commission values.

Contains:

- Commission Rule Reference
- Salesperson Reference
- Sales Document Reference
- Invoice Reference
- Revenue Amount
- Commission Amount
- Calculation Date
- Workflow Reference
- Approval Status
- Payment Status

Business Rules:

- Commission calculations must reference eligible sales.
- Approved commissions become locked.
- Finance or Payroll owns actual payout.

---

# 26. CustomerSalesInsight

Stores calculated commercial insight about customers.

Contains:

- Tenant Reference
- Customer Reference
- Lifetime Revenue
- Total Orders
- Total Invoices
- Average Order Value
- Last Purchase Date
- Preferred Products
- Preferred Categories
- Sales Trend
- Risk Indicator
- Last Calculated Date

Business Rules:

- Customer master data remains in CRM.
- Financial values are derived from Sales Documents and Finance.
- Insights may be recalculated periodically.

---

# 27. Aggregate Roots

Primary aggregate roots include:

- SalesPipeline
- SalesTeam
- SalesTerritory
- SalesTarget
- PriceList
- DiscountRequest
- Promotion
- SalesForecast
- CommissionRule

Aggregate roots control related child records and lifecycle rules.

Examples:

```text
PriceList
    └── PriceListItem

SalesTeam
    └── SalesTeamMember

Promotion
    └── PromotionItem

SalesTarget
    └── SalesTargetAssignment
```

---

# 28. Business Keys

Business keys should be unique within tenant scope.

Examples:

- Sales Pipeline Number
- Sales Team Code
- Territory Code
- Target Code
- Price List Code
- Promotion Code
- Commission Rule Code

Where official numbering is required, the Document Numbering Engine should be used.

---

# 29. Surrogate Keys

Each table should use an internal surrogate primary key.

Purpose:

- Stable relationships
- Cleaner references
- Easier migrations
- Better indexing
- Multi-tenant scalability

Business keys should not replace surrogate keys.

---

# 30. Snapshot Strategy

The Sales Module uses snapshots to preserve historical accuracy.

Snapshots include:

- Pricing Snapshot
- Discount Snapshot
- Promotion Snapshot
- Pipeline Snapshot
- Forecast Snapshot
- Commission Snapshot

Snapshots prevent historical reports from changing when business rules are updated later.

---

# 31. Pricing Snapshot Strategy

When pricing is applied, the platform stores:

- Price List Used
- Item Price
- Customer Price
- Promotion Applied
- Discount Applied
- Currency
- Exchange Rate
- Effective Date

The snapshot is passed to Sales Documents during quotation or invoice requests.

---

# 32. Discount Snapshot Strategy

Stores:

- Requested Discount
- Approved Discount
- Approval Reference
- Approval Date
- Approver
- Discount Reason

Discount snapshots are immutable after approval.

---

# 33. Promotion Snapshot Strategy

Stores:

- Promotion Name
- Promotion Code
- Promotion Type
- Promotion Value
- Effective Period
- Eligibility Rules

Promotion snapshots preserve the commercial basis of the sale.

---

# 34. Pipeline Snapshot Strategy

Stores:

- Stage
- Probability
- Estimated Revenue
- Expected Close Date
- Salesperson
- Team
- Territory

Used for historical forecast and performance reporting.

---

# 35. Forecast Versioning

Forecasts must support version history.

Each forecast version records:

- Forecast Period
- Forecast Amounts
- Created By
- Created Date
- Reason for Revision
- Status

Previous forecast versions remain available for comparison.

---

# 36. External Reference Strategy

External module references must store only identifiers and lightweight metadata where needed.

Examples:

- CRM Opportunity Reference
- CRM Customer Reference
- Sales Document Reference
- Inventory Item Reference
- Finance Revenue Reference
- Workflow Instance Reference
- Notification Reference
- Audit Reference

The owning module remains the source of truth.

---

# 37. Search Metadata

The module exposes searchable metadata including:

- Customer Name
- Opportunity Number
- Salesperson
- Sales Team
- Territory
- Pipeline Stage
- Price List
- Promotion
- Target
- Forecast Period

Search indexes are maintained by the Search & Indexing Engine.

---

# 38. Reporting Dimensions

Reporting dimensions include:

- Tenant
- Company
- Branch
- Salesperson
- Sales Team
- Territory
- Customer
- Customer Segment
- Product Category
- Sales Channel
- Currency
- Period
- Target Type
- Promotion
- Pipeline Stage

---

# 39. Audit References

The Sales Module stores references to audit events for:

- Pipeline Creation
- Stage Change
- Pricing Change
- Discount Request
- Discount Approval
- Promotion Activation
- Target Assignment
- Territory Assignment
- Forecast Revision
- Commission Calculation

Detailed logs remain in the Activity & Audit Engine.

---

# 40. Tenant Isolation

Every Sales Module record must include tenant context.

Rules:

- All records must belong to one tenant.
- Cross-tenant relationships are prohibited.
- Tenant-specific pricing, targets, territories, promotions, and forecasts are isolated.
- Queries must always enforce tenant filtering.

---

# 41. Branch Isolation

Branch-aware entities include:

- SalesPipeline
- SalesTeam
- SalesTerritory
- SalesTarget
- PriceList
- DiscountRequest
- Promotion
- SalesForecast
- SalesPerformance

Branch visibility is enforced by the Authorization Engine.

---

# 42. Referential Integrity Rules

The following rules apply:

- SalesPipeline requires a CRM Opportunity reference.
- SalesTeamMember requires a SalesTeam.
- SalesTerritoryAssignment requires a SalesTerritory.
- SalesTargetAssignment requires a SalesTarget.
- PriceListItem requires a PriceList.
- PromotionItem requires a Promotion.
- CommissionCalculation requires a CommissionRule.
- DiscountRequest may require a Pipeline reference.

Deletion of active parent records is prohibited when dependent records exist.

---

# 43. Immutability Rules

The following records become immutable after approval or closure:

- Approved Discount Requests
- Approved Price Lists
- Active Promotions after usage
- Closed Sales Targets
- Approved Commission Calculations
- Closed Pipeline History
- Published Forecast Versions

Corrections require new revisions or authorized reversal processes.

---

# 44. Soft Delete Strategy

Soft delete applies to configurable and operational records where business history must be preserved.

Soft-deleted records:

- Are hidden from normal views
- Remain available for audit
- Remain available for historical reports
- Cannot be reused if business keys must remain unique

Issued or historically referenced records should not be physically deleted.

---

# 45. Concurrency Control

Concurrency control is required for:

- Pricing updates
- Discount approvals
- Target revisions
- Forecast revisions
- Commission calculations
- Pipeline stage updates

The platform should prevent lost updates and conflicting approvals.

---

# 46. Indexing Strategy

Recommended indexes include:

- Tenant
- Branch
- Customer Reference
- Opportunity Reference
- Salesperson Reference
- Sales Team Reference
- Territory Reference
- Status
- Date Period
- Sales Channel
- Price List Code
- Promotion Code

Indexes should support dashboards, search, workflow queues, and reports.

---

# 47. Archiving Strategy

Historical records should be archived based on tenant retention policies.

Archivable records include:

- Closed Pipelines
- Expired Promotions
- Old Forecast Versions
- Closed Targets
- Historical Performance Snapshots
- Old Commission Calculations

Archived data remains available for reporting and compliance.

---

# 48. Event Publishing Points

Events should be published when:

- Pipeline is created
- Pipeline stage changes
- Pricing is changed
- Discount is requested
- Discount is approved
- Promotion is activated
- Target is assigned
- Forecast is updated
- Commission is calculated
- Sales is completed
- Sales is lost

Events are published through the Platform Event Bus.

---

# 49. Read Model Considerations

Read models may be created for:

- Sales Dashboard
- Pipeline Funnel
- Target Achievement
- Sales Forecast
- Sales Performance
- Promotion Performance
- Commission Summary
- Customer Sales Insight

Read models improve performance without changing source-of-truth ownership.

---

# 50. Scalability Strategy

The Sales Module database supports:

- Large customer bases
- Multiple branches
- Multiple companies
- Multiple sales teams
- High transaction volumes
- Long sales histories
- Large reports
- AI-driven forecasting
- Distributed search
- Dashboard read models

The model is designed for enterprise-scale growth.

---

# 51. Database Summary

The Sales Module database is focused on commercial execution.

It owns pipeline execution, pricing, targets, teams, territories, promotions, forecasting, commissions, and customer sales insights while integrating with CRM, Sales Documents, Inventory, Finance, and Platform Engines.

This separation provides:

- Clear ownership boundaries
- Strong tenant isolation
- Reliable commercial history
- Clean integration with official documents
- Secure pricing and discount control
- Reporting readiness
- Enterprise scalability

---

# 52. Next Document

The next specification document is:

```text
SECURITY.md
```

This document has already been generated for the Sales Module.
