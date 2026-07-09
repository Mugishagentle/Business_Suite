# Procurement & Supplier Management Module

## DATABASE.md

---

# 1. Database Overview

The Procurement & Supplier Management Module database provides the persistent storage layer for all procurement operations within the Business Suite Enterprise Platform.

It stores procurement master data, transactional data, operational records, supplier information, purchasing documents, sourcing records, receiving records, contract information, and procurement analytical data.

The Procurement database is designed using the following principles:

- Domain Driven Design (DDD)
- Aggregate Ownership
- Multi-Tenant Architecture
- PostgreSQL Best Practices
- Event Driven Architecture
- API First Design
- Document-Based Transactions
- Immutable Business History
- Platform Engine Reuse

The Procurement Module owns only procurement business data.

Shared platform capabilities such as authentication, workflows, notifications, audit trails, reporting, document storage, and reference data remain owned by their respective Platform Engines.

---

# 2. Database Design Principles

The Procurement database follows the Business Suite database standards.

---

## Principle 1 — Single Ownership

Every table has a single owning module.

Examples:

| Table                      | Owner            |
| -------------------------- | ---------------- |
| proc_suppliers             | Procurement      |
| proc_purchase_orders       | Procurement      |
| inv_inventory_transactions | Inventory Engine |
| fin_accounts_payable       | Finance Engine   |
| auth_users                 | Platform Core    |
| wf_workflow_instances      | Workflow Engine  |

Modules may reference each other's data but must never duplicate ownership.

---

## Principle 2 — Aggregate Design

Tables are grouped into aggregates.

Example:

Supplier

↓

Supplier Contacts

↓

Supplier Addresses

↓

Supplier Bank Accounts

↓

Supplier Documents

↓

Supplier Performance

↓

Supplier Risk

The Supplier aggregate is updated as a single business unit.

---

## Principle 3 — Immutable Transactions

Transactional tables should not be modified after approval.

Instead:

- Amendments create new versions.
- Corrections create reversal documents.
- Cancellations change document status.
- Audit history is preserved.

Examples:

- Purchase Orders
- Goods Receipts
- Contracts
- Invoice Matching

---

## Principle 4 — Multi-Tenant Isolation

Every business record belongs to a tenant.

Standard ownership hierarchy:

```text
Tenant

↓

Company

↓

Branch

↓

Department

↓

Business Record
```

Tenant isolation is enforced using PostgreSQL Row Level Security (RLS).

---

## Principle 5 — Event Driven Persistence

Business events are not stored inside transactional tables.

Instead:

```text
Business Transaction

↓

Platform Event Bus

↓

Outbox Event

↓

Subscribers
```

The Procurement Module publishes events.

The Platform Event Bus distributes them.

---

## Principle 6 — Platform Engine Reuse

The Procurement Module stores only procurement business data.

Examples:

Authentication

→ Platform Core

Workflow

→ Workflow Engine

Documents

→ Document Management Engine

Audit

→ Activity & Audit Engine

Reports

→ Reporting Engine

Search

→ Search Engine

Notifications

→ Notification Engine

Reference Data

→ Reference Data Engine

---

## Principle 7 — Performance First

The schema is optimized for:

- Large datasets
- Millions of Purchase Orders
- Millions of Goods Receipts
- Large supplier databases
- Fast reporting
- Fast search
- Partitioning
- Indexing

---

# 3. Multi-Tenant Data Architecture

Every Procurement record belongs to a tenant.

```text
Platform

↓

Tenant

↓

Company

↓

Branch

↓

Department

↓

Procurement Record
```

Each procurement transaction stores tenant ownership information to guarantee complete data isolation.

---

## Standard Ownership Columns

Every major Procurement table should contain:

```text
tenant_id

company_id

branch_id

department_id
```

Where applicable, records may also reference:

- cost_center_id
- project_id
- grant_id
- warehouse_id

These references enable organization-wide reporting and financial allocation.

---

# 4. Standard Entity Metadata

Every Procurement business entity should inherit the Business Suite standard metadata.

## Identity

```text
id UUID PRIMARY KEY
```

---

## Ownership

```text
tenant_id

company_id

branch_id

department_id
```

---

## Lifecycle

```text
status

is_active

is_deleted
```

---

## Workflow

```text
workflow_instance_id

workflow_status

approval_status

current_step

current_approver
```

Workflow execution remains owned by the Workflow Engine.

---

## Versioning

```text
version

row_version
```

Supports optimistic concurrency and document versioning.

---

## Auditing

```text
created_at

created_by

updated_at

updated_by

deleted_at

deleted_by
```

Detailed audit history is maintained by the Activity & Audit Engine.

---

# 5. Standard Business Document Structure

All Procurement business documents share a common structure.

Examples include:

- Procurement Plan
- Purchase Requisition
- RFQ
- RFP
- Purchase Order
- Goods Receipt
- Service Receipt
- Supplier Contract
- Supplier Return

Each document contains the following common fields.

---

## Document Identity

```text
document_number

document_type

document_date

reference_number
```

---

## Status

```text
document_status

approval_status

workflow_status
```

---

## Financial Context

```text
currency_id

exchange_rate
```

---

## Business Context

```text
supplier_id

project_id

cost_center_id

grant_id
```

Where applicable.

---

## Workflow Context

```text
workflow_instance_id

current_approval_level

submitted_at

approved_at
```

---

## Remarks

```text
remarks

internal_notes
```

---

# 6. Database Naming Standards

The Procurement Module follows consistent naming conventions.

## Tables

All Procurement tables use the prefix:

```text
proc_
```

Examples:

```text
proc_suppliers

proc_purchase_orders

proc_goods_receipts

proc_contracts
```

---

## Primary Keys

Every table uses:

```text
id UUID
```

---

## Foreign Keys

Foreign keys follow:

```text
supplier_id

purchase_order_id

contract_id

warehouse_id
```

---

## Boolean Fields

Boolean columns begin with:

```text
is_

has_

allow_

requires_
```

Examples:

```text
is_active

is_framework

has_contract

requires_inspection
```

---

## Date Fields

Examples:

```text
created_at

approved_at

delivery_date

expiry_date
```

---

## Monetary Fields

Examples:

```text
subtotal

discount_amount

tax_amount

total_amount
```

All monetary values should use high-precision numeric types.

---

# 7. Database Domains

The Procurement database is organized into the following logical domains.

```text
Master Data

↓

Planning

↓

Suppliers

↓

Requests

↓

Compliance

↓

Strategic Sourcing

↓

Purchasing

↓

Receiving

↓

Invoice Matching

↓

Contracts

↓

Analytics

↓

Views

↓

Events
```

Each domain owns its own tables and business rules while collaborating through well-defined relationships.

---

# 8. Procurement Planning Schema

The Procurement Planning schema stores planned procurement activities before operational purchasing begins.

It supports:

- Annual Procurement Plans
- Department Plans
- Branch Plans
- Project Plans
- Grant Plans
- Procurement Forecasts
- Procurement Calendar
- Plan Revisions

---

---

# 9. Procurement Planning Database Architecture

The Procurement Planning schema is responsible for storing all procurement planning information before operational procurement begins.

Unlike transactional procurement data, planning data represents future procurement intentions rather than actual purchasing commitments.

The schema supports:

- Annual Procurement Plans
- Department Procurement Plans
- Branch Procurement Plans
- Project Procurement Plans
- Grant Procurement Plans
- Procurement Forecasts
- Procurement Calendar
- Budget References
- Procurement Priorities
- Plan Revisions

The Planning schema is the starting point of the Procurement lifecycle.

---

# 10. Procurement Planning Aggregate

The Procurement Planning aggregate is organized as follows.

```text
Procurement Plan

│

├── Plan Lines

├── Forecasts

├── Calendar Activities

├── Budget References

├── Version History

└── Related Procurement Requests
```

The Procurement Plan is the aggregate root.

All child records belong to a Procurement Plan.

---

# 11. Procurement Planning Ownership

The Procurement Module owns:

- Procurement Plans
- Procurement Plan Lines
- Procurement Forecasts
- Procurement Calendar
- Procurement Plan Versions

The following remain owned by other modules:

Finance Engine

- Budgets
- Budget Lines
- Budget Commitments

Inventory Engine

- Inventory Items
- Reorder Levels
- Stock Availability

Projects Module

- Projects
- Activities

Reference Data Engine

- Procurement Categories
- Procurement Methods
- Priorities
- Units of Measure

Workflow Engine

- Plan Approval Workflow

---

# 12. Procurement Planning Relationships

The Procurement Planning schema interacts with multiple business domains.

```text
Procurement Plan

│

├── Finance Budget

├── Project

├── Grant

├── Department

├── Branch

├── Procurement Requests

└── Forecasts
```

Approved Procurement Plans become the primary source for Procurement Requests.

Procurement Requests may optionally reference Procurement Plan Lines.

---

# 13. Procurement Planning Versioning

Procurement Plans are immutable after approval.

Every significant revision creates a new version.

```text
Annual Procurement Plan

↓

Version 1

↓

Approved

↓

Version 2

↓

Approved

↓

Version 3

↓

Current
```

Only one version may be active.

Historical versions remain available for audit and reporting.

The Activity & Audit Engine maintains a complete audit trail.

---

# 14. Procurement Planning Database Rules

The Procurement Planning schema shall enforce the following rules.

- Every Procurement Plan shall belong to one tenant.
- Every Procurement Plan shall have a unique document number.
- Every Procurement Plan shall contain at least one Procurement Plan Line.
- Approved Procurement Plans shall not be modified directly.
- Plan revisions shall create new versions.
- Procurement Plans may reference Finance budgets.
- Procurement Plans may reference Projects, Grants, Departments, and Branches.
- Procurement Requests may reference approved Procurement Plans.
- Every Planning transaction shall publish business events through the Platform Event Bus.
- All Planning records shall support Row Level Security (RLS).

---

# 15. Supplier Management Schema

The Supplier Management schema stores the complete supplier lifecycle.

Unlike traditional ERP systems where suppliers are simple vendor records, the Business Suite maintains suppliers as enterprise business partners with full lifecycle management.

The schema supports:

- Supplier Registration
- Supplier Qualification
- Supplier Performance
- Supplier Risk
- Supplier Compliance
- Supplier Banking
- Supplier Documents
- Supplier Contacts
- Supplier Contracts
- Supplier History

The Supplier Management schema is the authoritative source of supplier information throughout the Business Suite.

---

# 16. Supplier Aggregate

The Supplier aggregate is organized as follows.

```text
Supplier

│

├── Contacts

├── Addresses

├── Bank Accounts

├── Tax Information

├── Compliance

├── Certifications

├── Documents

├── Categories

├── Performance

├── Risk

├── Contracts

└── Procurement History
```

All child entities belong to a Supplier.

---

# 17. proc_suppliers

## Purpose

Stores the Supplier Master.

This is the primary business entity representing suppliers.

---

## Ownership

Owned by:

```text
Procurement Module
```

---

## Key Fields

```text
id

tenant_id

company_id

branch_id

supplier_number

supplier_name

supplier_code

supplier_type_id

registration_number

tin_number

vat_number

industry_id

supplier_category_id

preferred_supplier

approved_supplier

supplier_status

currency_id

payment_terms_id

credit_limit

risk_rating

performance_score

onboarding_date

approval_status

workflow_status

remarks

created_at

created_by

updated_at

updated_by
```

---

## Business Rules

- Supplier Number is generated by the Document Numbering Engine.
- Supplier Code must be unique within a tenant.
- Approval is managed by the Workflow Engine.
- Only approved suppliers may receive Purchase Orders where configured.
- Risk Rating and Performance Score are calculated automatically.
- Procurement owns supplier records.
- Finance references suppliers for Accounts Payable.

---

## Events Published

```text
SupplierRegistered

SupplierSubmitted

SupplierApproved

SupplierActivated

SupplierSuspended

SupplierBlacklisted

SupplierUpdated
```

---

# 18. proc_supplier_contacts

## Purpose

Stores multiple contacts for each supplier.

---

## Key Fields

```text
id

tenant_id

supplier_id

contact_name

job_title

department

email

phone_number

mobile_number

preferred_contact

is_primary

status

created_at

updated_at
```

---

## Business Rules

- Suppliers may have unlimited contacts.
- One contact may be marked as Primary.
- Contacts may be referenced during procurement communication.

---

# 19. proc_supplier_addresses

## Purpose

Stores supplier addresses.

---

## Address Types

```text
Registered

Physical

Billing

Delivery

Warehouse

Postal
```

---

## Key Fields

```text
id

tenant_id

supplier_id

address_type

country_id

region_id

district_id

city

postal_code

physical_address

latitude

longitude

is_primary

status
```

---

## Business Rules

- Suppliers may maintain multiple addresses.
- Delivery addresses may differ from billing addresses.
- GPS coordinates support logistics and delivery planning.

---

# 20. proc_supplier_bank_accounts

## Purpose

Stores supplier banking and payment information.

---

## Key Fields

```text
id

tenant_id

supplier_id

bank_id

branch_name

account_name

account_number

currency_id

swift_code

iban

mobile_money_provider

mobile_money_number

is_default

status
```

---

## Business Rules

- Suppliers may have multiple bank accounts.
- Finance validates bank information before payments.
- Procurement stores supplier payment instructions only.

---

---

# 21. Supplier Management Database Architecture

The Supplier Management schema provides the master data foundation for all supplier-related activities within the Procurement Module.

Unlike traditional ERP systems where suppliers are simply vendor records, Business Suite models suppliers as strategic business partners with complete lifecycle management.

The Supplier Management schema supports:

- Supplier Registration
- Supplier Qualification
- Supplier Classification
- Supplier Contacts
- Supplier Addresses
- Banking Information
- Tax Profiles
- Compliance Management
- Certifications
- Performance Management
- Risk Management
- Supplier Evaluation
- Supplier Documents
- Supplier Relationships

This schema serves as the authoritative supplier repository for the entire Business Suite.

---

# 22. Supplier Management Aggregate

The Supplier aggregate follows Domain Driven Design (DDD).

```text
Supplier

│

├── Contacts

├── Addresses

├── Bank Accounts

├── Tax Profiles

├── Categories

├── Documents

├── Certifications

├── Compliance

├── Performance

├── Risk Assessments

├── Evaluations

└── Contracts
```

The Supplier entity is the aggregate root.

All related records belong to a Supplier.

---

# 23. Supplier Ownership

The Procurement Module owns:

- Suppliers
- Supplier Contacts
- Supplier Addresses
- Supplier Banks
- Supplier Categories
- Supplier Documents
- Supplier Certifications
- Supplier Compliance
- Supplier Performance
- Supplier Risk
- Supplier Evaluations

The following remain owned by other modules:

Finance Engine

- Accounts Payable
- Vendor Ledger
- Vendor Payments

Document Management Engine

- Physical Files
- Document Versions

Reference Data Engine

- Supplier Types
- Countries
- Banks
- Industries
- Payment Terms

Workflow Engine

- Supplier Approval Workflow

Notification Engine

- Expiry Notifications
- Supplier Alerts

---

# 24. Supplier Relationships

The Supplier schema integrates with multiple Procurement domains.

```text
Supplier

│

├── Purchase Requisitions

├── RFQs

├── Supplier Quotations

├── Purchase Orders

├── Goods Receipts

├── Invoice Matching

├── Contracts

├── Performance

└── Payments (Finance)
```

Every procurement transaction references a Supplier.

Finance also references Suppliers for Accounts Payable and Vendor Payments.

---

# 25. Supplier Database Rules

The Supplier schema shall enforce the following rules.

- Every Supplier shall belong to one tenant.
- Supplier Number shall be generated automatically.
- Supplier Code shall be unique per tenant.
- Supplier approval shall use the Workflow Engine.
- Only approved suppliers may receive Purchase Orders where configured.
- Suppliers may have multiple Contacts.
- Suppliers may have multiple Addresses.
- Suppliers may have multiple Bank Accounts.
- Supplier documents shall be stored by the Document Management Engine.
- Supplier performance shall be calculated automatically.
- Supplier risk shall support multiple assessment types.
- Supplier evaluations shall remain historically immutable.
- All Supplier records shall support Row Level Security (RLS).
- Every Supplier transaction shall publish business events.

---

# 26. proc_suppliers

## Purpose

Stores the Supplier Master.

This is the primary business entity representing suppliers throughout the Business Suite.

---

## Key Fields

```text
id

tenant_id

company_id

branch_id

supplier_number

supplier_code

supplier_name

legal_name

supplier_type_id

industry_id

registration_number

tin_number

vat_number

payment_terms_id

default_currency_id

preferred_supplier

approved_supplier

supplier_status

overall_rating

risk_rating

performance_score

onboarding_date

workflow_status

approval_status

remarks

created_at

created_by

updated_at

updated_by
```

---

## Business Rules

- Supplier Number generated by Document Numbering Engine.
- Supplier Code unique within Tenant.
- Supplier approval through Workflow Engine.
- One Supplier may participate in multiple procurement activities.
- Finance references Supplier for Vendor Ledger.

---

## Events

```text
SupplierRegistered

SupplierApproved

SupplierActivated

SupplierUpdated

SupplierSuspended

SupplierBlacklisted
```

---

# 27. proc_supplier_contacts

## Purpose

Stores supplier contact persons.

## Key Fields

```text
id

tenant_id

supplier_id

contact_name

job_title

department

email

phone

mobile

is_primary

status
```

One supplier may have multiple contacts.

---

# 28. proc_supplier_addresses

## Purpose

Stores supplier addresses.

## Address Types

```text
Registered

Physical

Billing

Delivery

Warehouse

Postal
```

---

## Key Fields

```text
id

tenant_id

supplier_id

address_type

country_id

district_id

city

postal_code

address

latitude

longitude

is_primary
```

---

# 29. proc_supplier_bank_accounts

## Purpose

Stores supplier banking information.

## Key Fields

```text
id

tenant_id

supplier_id

bank_id

branch_name

account_name

account_number

currency_id

swift_code

iban

mobile_money_provider

mobile_money_number

is_default
```

Finance validates payment destinations before payments are made.

---

# 30. proc_supplier_tax_profiles

## Purpose

Stores supplier taxation references.

## Key Fields

```text
id

tenant_id

supplier_id

tin_number

vat_registration_number

withholding_tax

tax_category_id

tax_exemption_number

effective_from

effective_to
```

Finance owns tax calculations.

---

# 31. proc_supplier_categories

## Purpose

Associates suppliers with procurement categories.

Examples:

- ICT
- Construction
- Consultancy
- Cleaning
- Medical Supplies
- Office Supplies
- Raw Materials

---

## Key Fields

```text
id

tenant_id

supplier_id

category_id

is_primary
```

---

# 32. proc_supplier_documents

## Purpose

Stores references to supplier documents.

## Examples

- Certificate of Incorporation
- Trading License
- Tax Clearance
- Insurance
- Bank Letter
- Company Profile
- ISO Certificates

---

## Key Fields

```text
id

tenant_id

supplier_id

document_type

document_reference_id

issue_date

expiry_date

verified

verified_by
```

The actual files are managed by the Document Management Engine.

---

# 33. proc_supplier_certifications

## Purpose

Stores supplier certifications.

## Key Fields

```text
id

tenant_id

supplier_id

certification_name

certificate_number

issuing_body

issue_date

expiry_date

verified
```

Certification expiry should generate notifications automatically.

---

# 34. proc_supplier_compliance

## Purpose

Stores supplier compliance records.

## Examples

- Tax Compliance
- Trading License
- Environmental Compliance
- Health & Safety
- Insurance
- Regulatory Compliance

---

## Key Fields

```text
id

tenant_id

supplier_id

compliance_type

status

verification_date

verified_by

expiry_date

remarks
```

---

# 35. proc_supplier_performance

## Purpose

Stores supplier performance metrics.

Performance is automatically calculated.

## Metrics

- Delivery Performance
- Lead Time
- Quality
- Pricing
- Responsiveness
- Compliance

---

## Key Fields

```text
id

tenant_id

supplier_id

evaluation_period

delivery_score

quality_score

pricing_score

responsiveness_score

compliance_score

overall_score

calculated_at
```

---

# 36. proc_supplier_risk

## Purpose

Stores supplier risk assessments.

## Risk Types

- Financial
- Operational
- Compliance
- Country
- Supply Chain
- Legal
- Cyber

---

## Key Fields

```text
id

tenant_id

supplier_id

risk_type

risk_level

risk_score

assessment_date

review_date

mitigation_plan
```

---

# 37. proc_supplier_evaluations

## Purpose

Stores structured supplier evaluations.

## Key Fields

```text
id

tenant_id

supplier_id

evaluation_template_id

evaluation_date

evaluator_id

technical_score

commercial_score

overall_score

recommendation

approval_status
```

---

# 38. Supplier Management Schema Summary

The Supplier Management schema provides a comprehensive enterprise supplier repository.

It enables organizations to manage supplier registration, qualification, compliance, performance, risk, evaluations, banking information, documentation, and business relationships while integrating seamlessly with Strategic Sourcing, Purchasing, Finance, Contracts, and future Supplier Portal capabilities.

This schema establishes Suppliers as long-term strategic business partners rather than simple vendor records.

---

# 39. Procurement Requests Schema

The Procurement Requests schema stores all internal requests for goods, services, assets, and works.

It represents the official demand management layer of the Procurement Module.

Every procurement process should begin with an approved Procurement Request unless organizational policy explicitly allows emergency procurement.

The schema supports:

- Purchase Requisitions
- Service Requests
- Material Requests
- Asset Requests
- Emergency Requests
- Project Requests
- Grant Requests
- Framework Call-Off Requests
- Request Attachments
- Request Approvals
- Request Revisions

The Procurement Request is the starting point for Strategic Sourcing.

---

# 40. Procurement Request Aggregate

The Procurement Request aggregate is organized as follows.

```text
Procurement Request

│

├── Request Lines

├── Budget References

├── Attachments

├── Workflow

├── Approvals

├── Revisions

├── Related RFQs

├── Related Purchase Orders

└── Audit History
```

The Procurement Request is the aggregate root.

---

# 41. Procurement Request Ownership

The Procurement Module owns:

- Procurement Requests
- Request Lines
- Request Revisions
- Request Attachments
- Request Budget References
- Request Allocations

The following remain owned by other modules:

Finance Engine

- Budgets
- Budget Lines
- Budget Commitments

Inventory Engine

- Inventory Items
- Warehouses

Projects Module

- Projects
- Activities

Workflow Engine

- Approval Execution

Document Management Engine

- Attachments

Reference Data Engine

- Procurement Types
- Priorities
- Request Categories

---

# 42. Procurement Request Relationships

```text
Procurement Request

│

├── Procurement Plan

├── Budget

├── Cost Centre

├── Project

├── Grant

├── Inventory Item

├── Supplier (Optional)

├── RFQ

├── Purchase Order

└── Goods Receipt
```

One Procurement Request may generate:

- Multiple RFQs
- Multiple Purchase Orders
- Multiple Deliveries

---

# 43. Procurement Request Database Rules

The Procurement Request schema shall enforce the following rules.

- Every Request belongs to one Tenant.
- Every Request has a unique document number.
- Every Request contains at least one Request Line.
- Approved Requests are immutable.
- Amendments create new versions.
- Budget validation occurs before approval.
- Workflow approval is mandatory unless configured otherwise.
- Every Request supports complete audit history.
- Every Request publishes business events.

---

# 44. proc_procurement_requests

## Purpose

Stores Procurement Request headers.

---

## Key Fields

```text
id

tenant_id

company_id

branch_id

department_id

request_number

request_type_id

request_date

required_date

priority_id

procurement_plan_id

budget_id

cost_center_id

project_id

grant_id

requested_by

estimated_total

currency_id

workflow_status

approval_status

document_status

remarks

created_at

created_by

updated_at

updated_by
```

---

## Business Rules

- Request Number generated by Document Numbering Engine.
- One Request may contain many Request Lines.
- Budget validated by Finance.
- Workflow managed by Workflow Engine.
- Request may originate from an approved Procurement Plan.

---

## Events

```text
ProcurementRequestCreated

ProcurementRequestSubmitted

ProcurementRequestApproved

ProcurementRequestRejected

ProcurementRequestCancelled
```

---

# 45. proc_procurement_request_lines

## Purpose

Stores requested goods, services, assets, or works.

---

## Key Fields

```text
id

tenant_id

procurement_request_id

line_number

item_id

service_description

asset_description

quantity

unit_of_measure_id

estimated_unit_cost

estimated_total_cost

currency_id

required_date

warehouse_id

budget_line_id

project_activity_id

preferred_supplier_id

status
```

---

## Business Rules

- One Request must contain at least one line.
- Line may reference Inventory Item or describe Service/Asset.
- Estimated values support budgeting only.

---

# 46. proc_request_budget_allocations

## Purpose

Links Procurement Requests to Finance Budgets.

---

## Key Fields

```text
id

tenant_id

procurement_request_id

budget_id

budget_line_id

allocated_amount

currency_id

validation_status
```

Finance validates allocations.

---

# 47. proc_request_revisions

## Purpose

Stores Procurement Request revisions.

Approved Requests are never overwritten.

---

## Key Fields

```text
id

tenant_id

procurement_request_id

version_number

revision_reason

previous_version_id

approved_at

approved_by

is_current
```

---

# 48. proc_request_attachments

## Purpose

Stores references to Procurement Request documents.

Examples:

- Technical Specifications
- Drawings
- Scope of Work
- BOQs
- Images
- Budget Justification

---

## Key Fields

```text
id

tenant_id

procurement_request_id

document_type

document_reference_id

uploaded_by

uploaded_at
```

Files remain owned by the Document Management Engine.

---

# 49. proc_request_allocations

## Purpose

Supports allocation of a Procurement Request across multiple organizational dimensions.

Examples:

- Multiple Cost Centres
- Multiple Projects
- Multiple Grants
- Multiple Departments

---

## Key Fields

```text
id

tenant_id

procurement_request_id

allocation_type

reference_id

allocation_percentage

allocated_amount
```

---

# 50. Request Relationships

```text
proc_procurement_requests

        │

        ├──── proc_procurement_request_lines

        ├──── proc_request_budget_allocations

        ├──── proc_request_revisions

        ├──── proc_request_attachments

        └──── proc_request_allocations
```

---

---

# 51. Procurement Request Schema Summary

The Procurement Request schema serves as the official demand management layer of the Procurement Module.

It transforms business requirements into structured procurement transactions while ensuring every procurement activity follows organizational policies, financial controls, and workflow approvals.

The schema provides support for:

- Purchase Requisitions
- Material Requests
- Service Requests
- Asset Requests
- Emergency Procurement Requests
- Project Procurement Requests
- Grant Procurement Requests
- Framework Call-Off Requests
- Budget Allocations
- Organizational Allocations
- Version Control
- Document Attachments

Each Procurement Request becomes the authoritative source for downstream procurement activities including:

```text
Procurement Request

↓

Compliance Validation

↓

Strategic Sourcing

↓

RFQ / RFP / Tender

↓

Supplier Evaluation

↓

Purchase Order

↓

Goods Receipt

↓

Invoice Matching

↓

Finance
```

The Procurement Request schema integrates directly with:

| Module / Engine            | Purpose                                       |
| -------------------------- | --------------------------------------------- |
| Finance Engine             | Budget Validation, Cost Centres, Budget Lines |
| Inventory Engine           | Inventory Items, Warehouses                   |
| Workflow Engine            | Approval Routing                              |
| Document Numbering Engine  | Request Number Generation                     |
| Document Management Engine | Request Attachments                           |
| Notification Engine        | Approval Notifications                        |
| Reporting Engine           | Procurement Analytics                         |
| Activity & Audit Engine    | Audit Trail                                   |
| Platform Event Bus         | Business Events                               |

The schema enforces the following enterprise principles:

- Every Procurement Request belongs to one Tenant.
- Every Request shall contain one or more Request Lines.
- Budget validation shall occur before approval.
- Approved Requests shall be immutable.
- Amendments create new document versions.
- Procurement Requests may reference Procurement Plans.
- Procurement Requests may reference Projects, Grants, and Cost Centres.
- All Procurement Requests shall support Row Level Security (RLS).
- Every Procurement Request shall generate a complete audit trail.
- Every Procurement Request shall publish business events through the Platform Event Bus.

This schema establishes the controlled entry point into the Procure-to-Pay lifecycle and ensures that every procurement activity within the Business Suite begins with a properly governed, traceable, and auditable business request.
