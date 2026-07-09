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

---

# 52. Procurement Compliance Schema

The Procurement Compliance schema provides the governance and policy enforcement layer for the Procurement Module.

Unlike workflow approvals, which determine **who approves**, Procurement Compliance determines **whether procurement is allowed to proceed**.

This schema ensures every procurement transaction complies with:

- Organizational Procurement Policies
- Budget Availability
- Procurement Thresholds
- Supplier Qualification
- Delegation of Authority
- Procurement Regulations
- Internal Controls
- Donor Requirements
- Government Procurement Rules
- Risk Management Policies

The Compliance schema provides configurable rule evaluation without hardcoding procurement policies.

---

# 53. Procurement Compliance Aggregate

The Procurement Compliance Aggregate consists of:

```text
Compliance Rules

│

├── Threshold Rules

├── Procurement Method Rules

├── Approval Policies

├── Validation Results

├── Conflict Declarations

├── Policy Exceptions

├── Emergency Procurement

└── Compliance History
```

The Compliance Rule is the aggregate root.

---

# 54. Procurement Compliance Ownership

The Procurement Module owns:

- Procurement Rules
- Threshold Rules
- Procurement Policies
- Compliance Validations
- Compliance Exceptions
- Conflict of Interest Records
- Emergency Procurement Records

The following remain owned by other modules:

Finance Engine

- Budget Validation
- Budget Commitments

Workflow Engine

- Approval Execution

Reference Data Engine

- Procurement Methods
- Compliance Statuses
- Approval Types

Activity & Audit Engine

- Audit History

Notification Engine

- Compliance Alerts

---

# 55. Procurement Compliance Relationships

```text
Compliance Rule

│

├── Procurement Request

├── RFQ

├── Purchase Order

├── Supplier

├── Contract

├── Budget

└── Workflow
```

Every procurement transaction may undergo one or more compliance validations.

---

# 56. Procurement Compliance Database Rules

The Procurement Compliance schema shall enforce the following rules.

- Every Compliance Rule belongs to one Tenant.
- Compliance Rules may apply by Company, Branch, Department or Procurement Category.
- Procurement Rules may have effective dates.
- Threshold Rules determine Procurement Methods.
- Validation Results are immutable.
- Policy Overrides require Workflow Approval.
- Emergency Procurement requires justification.
- Every Compliance Validation publishes business events.
- Every Compliance transaction supports Row Level Security (RLS).

---

# 57. proc_compliance_rules

## Purpose

Stores configurable procurement compliance rules.

---

## Key Fields

```text
id

tenant_id

rule_code

rule_name

rule_type

procurement_category_id

company_id

branch_id

department_id

minimum_amount

maximum_amount

currency_id

effective_from

effective_to

requires_budget_validation

requires_supplier_validation

requires_contract

requires_competitive_bidding

requires_conflict_declaration

requires_workflow

is_active

created_at

created_by

updated_at

updated_by
```

---

## Business Rules

- Rules are configurable.
- Rules are evaluated dynamically.
- Multiple rules may apply to one procurement transaction.
- Rules support versioning.

---

## Events

```text
ComplianceRuleCreated

ComplianceRuleUpdated

ComplianceRuleActivated

ComplianceRuleExpired
```

---

# 58. proc_threshold_rules

## Purpose

Defines procurement thresholds.

Thresholds determine:

- Procurement Method
- Approval Matrix
- Competition Requirements

---

## Key Fields

```text
id

tenant_id

threshold_name

minimum_amount

maximum_amount

currency_id

procurement_method_id

minimum_supplier_quotes

requires_tender

approval_matrix_id

is_active
```

---

## Example

```text
0 - 5M

↓

Direct Procurement

--------------------

5M - 20M

↓

Three Quotations

--------------------

20M -100M

↓

RFQ

--------------------

Above 100M

↓

Tender
```

---

# 59. proc_procurement_methods

## Purpose

Defines procurement methods available to an organization.

Although master values originate from the Reference Data Engine, Procurement stores tenant-specific configuration.

---

## Examples

```text
Direct Procurement

Three Quotations

RFQ

RFP

Open Tender

Restricted Tender

Framework Agreement

Call-Off Order

Emergency Procurement

Sole Source
```

---

## Key Fields

```text
id

tenant_id

method_name

method_code

requires_competition

requires_publication

requires_supplier_evaluation

requires_award

requires_contract

status
```

---

# 60. proc_compliance_validations

## Purpose

Stores procurement validation history.

Every validation executed by the Procurement Rules Engine is recorded.

---

## Key Fields

```text
id

tenant_id

document_type

document_id

validation_date

validation_status

budget_validation

supplier_validation

threshold_validation

policy_validation

method_validation

conflict_validation

validated_by

remarks
```

---

## Validation Status

```text
Passed

Failed

Warning

Override Required

Override Approved
```

---

# 61. proc_validation_results

## Purpose

Stores detailed validation results.

---

## Key Fields

```text
id

tenant_id

validation_id

rule_id

rule_name

severity

result

message

requires_override

override_status
```

---

# 62. proc_conflict_declarations

## Purpose

Stores conflict of interest declarations.

---

## Key Fields

```text
id

tenant_id

document_type

document_id

supplier_id

declared_by

relationship_type

relationship_description

declaration_date

review_status

reviewed_by

reviewed_at
```

---

## Business Rules

- Declarations are immutable.
- Workflow approval may be required.
- Conflicts are auditable.

---

# 63. proc_policy_exceptions

## Purpose

Stores approved procurement policy exceptions.

---

## Examples

```text
Emergency Procurement

Tender Waiver

Supplier Exception

Budget Override

Policy Override

Competition Waiver
```

---

## Key Fields

```text
id

tenant_id

exception_number

exception_type

document_type

document_id

justification

workflow_instance_id

approval_status

approved_by

approved_at

remarks
```

---

# 64. proc_emergency_procurements

## Purpose

Stores emergency procurement transactions.

Emergency Procurement bypasses normal sourcing but never bypasses governance.

---

## Key Fields

```text
id

tenant_id

emergency_number

procurement_request_id

urgency_level

emergency_reason

risk_assessment

approved_by

approved_at

regularization_required

regularization_date

status
```

---

## Business Rules

- Emergency procurement requires additional approvals.
- Regularization may be mandatory.
- Complete audit history is maintained.

---

# 65. Procurement Compliance Schema Summary

The Procurement Compliance schema provides the governance framework for enterprise procurement.

Rather than relying on hardcoded logic, it enables configurable procurement policies, threshold rules, supplier validation, procurement method determination, conflict management, emergency procurement, and policy exceptions.

This schema works alongside the Workflow Engine to ensure that every procurement transaction complies with organizational rules before progressing into Strategic Sourcing and Purchasing.

By separating **business policy** from **workflow execution**, the Business Suite achieves a highly configurable procurement model capable of supporting SMEs, large enterprises, NGOs, donor-funded projects, and government procurement regulations without requiring application code changes.

---

# 66. Strategic Sourcing Schema

The Strategic Sourcing schema manages the competitive procurement process from supplier invitation through supplier selection and contract award.

It enables organizations to procure goods and services through transparent, configurable, and auditable sourcing processes.

The schema supports:

- Requests for Information (RFI)
- Requests for Quotation (RFQ)
- Requests for Proposal (RFP)
- Open Tenders
- Restricted Tenders
- Framework Agreements
- Supplier Invitations
- Supplier Responses
- Supplier Quotations
- Technical Evaluations
- Commercial Evaluations
- Evaluation Committees
- Award Recommendations
- Negotiations

The Strategic Sourcing schema transforms approved Procurement Requests into approved Purchase Orders.

---

# 67. Strategic Sourcing Aggregate

The Strategic Sourcing aggregate is organized as follows.

```text
Strategic Sourcing

│

├── RFIs

├── RFQs

├── RFPs

├── Tenders

├── Supplier Invitations

├── Supplier Responses

├── Quotations

├── Evaluations

├── Negotiations

├── Award Recommendations

└── Procurement Awards
```

The sourcing event is the aggregate root.

---

# 68. Strategic Sourcing Ownership

The Procurement Module owns:

- RFIs
- RFQs
- RFPs
- Tenders
- Supplier Invitations
- Supplier Responses
- Quotations
- Evaluations
- Negotiations
- Award Recommendations

Other owners include:

Supplier Management

- Supplier Master

Workflow Engine

- Evaluation Approvals

Document Management Engine

- Submitted Documents

Notification Engine

- Supplier Invitations

Activity & Audit Engine

- Evaluation Audit Trail

Reference Data Engine

- Procurement Methods
- Evaluation Templates

---

# 69. Strategic Sourcing Relationships

```text
Procurement Request

↓

Strategic Sourcing

│

├── RFQ

├── RFP

├── Tender

├── Supplier Invitations

├── Supplier Responses

├── Evaluations

├── Negotiations

├── Award

↓

Purchase Order
```

One Procurement Request may generate multiple sourcing events.

---

# 70. Strategic Sourcing Database Rules

The Strategic Sourcing schema shall enforce the following rules.

- Every sourcing process belongs to one tenant.
- Every RFQ shall reference an approved Procurement Request.
- Supplier invitations may only target approved suppliers unless configured otherwise.
- Every quotation remains immutable after submission.
- Evaluation scores are immutable once submitted.
- Award recommendations require workflow approval where configured.
- Every sourcing activity publishes business events.
- All sourcing records support Row Level Security (RLS).

---

# 71. proc_sourcing_events

## Purpose

Represents a complete sourcing process.

A sourcing event may be:

- RFQ
- RFP
- Tender
- Framework Procurement
- Sole Source Procurement

---

## Key Fields

```text
id

tenant_id

event_number

procurement_request_id

procurement_method_id

title

description

opening_date

closing_date

evaluation_start_date

evaluation_end_date

status

workflow_status

approval_status

created_at

created_by

updated_at

updated_by
```

---

## Events Published

```text
SourcingEventCreated

SourcingEventPublished

SourcingEventClosed

SourcingEventCancelled
```

---

# 72. proc_supplier_invitations

## Purpose

Stores invitations sent to suppliers.

---

## Key Fields

```text
id

tenant_id

sourcing_event_id

supplier_id

invitation_date

response_deadline

delivery_method

status

opened_at

responded_at
```

---

## Delivery Methods

```text
Email

Supplier Portal

Manual

Government Portal

API
```

---

# 73. proc_supplier_responses

## Purpose

Stores supplier responses.

---

## Key Fields

```text
id

tenant_id

sourcing_event_id

supplier_id

response_number

submission_date

response_status

submitted_by

remarks
```

---

## Response Status

```text
Draft

Submitted

Withdrawn

Accepted

Rejected
```

---

# 74. proc_supplier_quotations

## Purpose

Stores supplier commercial quotations.

---

## Key Fields

```text
id

tenant_id

supplier_response_id

quotation_number

currency_id

subtotal

discount_amount

tax_amount

total_amount

delivery_period

payment_terms

quotation_valid_until

status
```

---

## Business Rules

- Multiple quotation revisions may exist before submission.
- Submitted quotations become immutable.
- Quotations support multiple currencies.

---

# 75. proc_supplier_quotation_lines

## Purpose

Stores individual quotation line items.

---

## Key Fields

```text
id

tenant_id

quotation_id

item_id

description

quantity

unit_price

discount

tax

line_total

delivery_date
```

---

# 76. proc_supplier_evaluations

## Purpose

Stores supplier evaluation results.

Supports:

- Technical Evaluation
- Commercial Evaluation
- Combined Evaluation

---

## Key Fields

```text
id

tenant_id

sourcing_event_id

supplier_id

evaluation_template_id

technical_score

commercial_score

overall_score

recommendation

evaluation_date

evaluator_id
```

---

# 77. proc_supplier_negotiations

## Purpose

Stores supplier negotiation history.

---

## Key Fields

```text
id

tenant_id

supplier_id

sourcing_event_id

negotiation_date

subject

discussion_summary

agreed_changes

next_action

status
```

---

# 78. proc_procurement_awards

## Purpose

Stores procurement award decisions.

---

## Key Fields

```text
id

tenant_id

award_number

sourcing_event_id

supplier_id

award_date

award_amount

currency_id

approval_status

approved_by

approved_at

status
```

---

## Business Rules

- Award Number generated by Document Numbering Engine.
- One sourcing event may produce one or multiple awards.
- Awards become the basis for Purchase Orders.

---

# 79. Strategic Sourcing Schema Summary

The Strategic Sourcing schema provides a complete enterprise sourcing framework that supports competitive procurement, supplier engagement, transparent evaluations, negotiations, and award management.

By separating sourcing from purchasing, the Business Suite enables organizations to optimize supplier selection, improve procurement transparency, and comply with organizational and regulatory procurement requirements.

This schema establishes the controlled transition from approved Procurement Requests to awarded suppliers and ultimately to Purchase Orders while maintaining a complete audit trail and full integration with Supplier Management, Workflow, Finance, and the Platform Event Bus.

---

# 80. Purchasing Schema

The Purchasing schema manages the creation, execution, amendment, fulfilment, and closure of Purchase Orders.

The Purchasing schema transforms approved sourcing decisions into legally binding purchasing commitments between the organization and suppliers.

Unlike Strategic Sourcing, which focuses on supplier selection, Purchasing focuses on commercial execution.

The schema supports:

- Purchase Orders
- Local Purchase Orders (LPOs)
- Service Purchase Orders
- Blanket Purchase Orders
- Framework Call-Off Orders
- Standing Purchase Orders
- Purchase Order Lines
- Delivery Schedules
- Purchase Order Amendments
- Purchase Order Allocations
- Purchase Order Taxes
- Purchase Order Attachments

---

# 81. Purchasing Aggregate

The Purchasing aggregate follows the standard Business Suite document architecture.

```text
Purchase Order

│

├── Lines

├── Delivery Schedule

├── Allocations

├── Taxes

├── Attachments

├── Amendments

├── Workflow

├── Timeline

└── Audit History
```

Purchase Order is the aggregate root.

---

# 82. Purchasing Ownership

The Procurement Module owns:

- Purchase Orders
- Purchase Order Lines
- Delivery Schedules
- Amendments
- Allocations

Other modules own:

Inventory Engine

- Stock Transactions
- Warehouses
- Batches
- Serials

Finance Engine

- Accounts Payable
- Payments
- Taxes
- Journal Entries

Workflow Engine

- Purchase Order Approval

Document Management Engine

- Attachments

---

# 83. Purchasing Relationships

```text
Procurement Award

↓

Purchase Order

│

├── Goods Receipt

├── Supplier Invoice

├── Contract

├── Inventory

└── Finance
```

One Purchase Order may generate:

- Multiple Goods Receipts
- Multiple Supplier Invoices
- Multiple Payments

---

# 84. Purchasing Database Rules

The Purchasing schema shall enforce the following rules.

- Every Purchase Order belongs to one Tenant.
- Purchase Order Number is unique.
- Purchase Orders reference approved Procurement Awards or approved Procurement Requests where applicable.
- Approved Purchase Orders are immutable.
- Amendments create new document versions.
- Partial deliveries are supported.
- Purchase Orders publish business events.
- All Purchasing tables support Row Level Security (RLS).

---

# 85. proc_purchase_orders

## Purpose

Stores Purchase Order headers.

---

## Key Fields

```text
id

tenant_id

company_id

branch_id

department_id

purchase_order_number

supplier_id

contract_id

procurement_request_id

procurement_award_id

purchase_order_type

purchase_order_date

required_delivery_date

currency_id

exchange_rate

payment_terms_id

delivery_terms_id

incoterm_id

subtotal

discount_amount

tax_amount

total_amount

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

## Purchase Order Types

```text
Standard

Local Purchase Order

Service Purchase Order

Blanket Purchase Order

Standing Purchase Order

Framework Call-Off

Contract Purchase Order
```

---

## Business Rules

- Purchase Order Number generated by Document Numbering Engine.
- One Purchase Order references one Supplier.
- One Purchase Order may reference one Contract.
- Purchase Orders become immutable after approval.

---

## Events

```text
PurchaseOrderCreated

PurchaseOrderSubmitted

PurchaseOrderApproved

PurchaseOrderIssued

PurchaseOrderCancelled

PurchaseOrderClosed
```

---

# 86. proc_purchase_order_lines

## Purpose

Stores Purchase Order line items.

---

## Key Fields

```text
id

tenant_id

purchase_order_id

line_number

item_id

service_id

asset_id

description

quantity

unit_of_measure_id

unit_price

discount

tax

line_total

warehouse_id

required_delivery_date

status
```

---

## Business Rules

- Every Purchase Order must contain one or more lines.
- Line may represent Inventory, Service or Asset.
- Warehouse references Inventory Engine.

---

# 87. proc_purchase_order_delivery_schedules

## Purpose

Supports scheduled and partial deliveries.

---

## Key Fields

```text
id

tenant_id

purchase_order_line_id

delivery_sequence

planned_delivery_date

planned_quantity

received_quantity

remaining_quantity

delivery_status
```

---

## Delivery Status

```text
Scheduled

Delivered

Partially Delivered

Delayed

Cancelled
```

---

# 88. proc_purchase_order_allocations

## Purpose

Allocates Purchase Orders across organizational dimensions.

Supports:

- Cost Centres
- Projects
- Grants
- Departments
- Branches

---

## Key Fields

```text
id

tenant_id

purchase_order_line_id

allocation_type

reference_id

allocation_percentage

allocated_amount
```

---

# 89. proc_purchase_order_taxes

## Purpose

Stores tax breakdowns for Purchase Orders.

Tax calculations originate from the Finance Engine.

---

## Key Fields

```text
id

tenant_id

purchase_order_id

tax_type_id

tax_rate

taxable_amount

tax_amount
```

---

## Supported Taxes

Examples:

- VAT
- Withholding Tax
- Import Duty
- Excise Duty

---

# 90. proc_purchase_order_attachments

## Purpose

Stores references to Purchase Order attachments.

Examples:

- Supplier Quotations
- Award Letters
- Technical Specifications
- Signed Purchase Orders

---

## Key Fields

```text
id

tenant_id

purchase_order_id

document_type

document_reference_id

uploaded_by

uploaded_at
```

Files remain owned by the Document Management Engine.

---

# 91. proc_purchase_order_amendments

## Purpose

Stores Purchase Order amendments.

Approved Purchase Orders are never edited directly.

---

## Amendment Types

```text
Quantity Change

Price Change

Supplier Change

Delivery Date Change

Cancellation

Extension

Commercial Terms
```

---

## Key Fields

```text
id

tenant_id

purchase_order_id

amendment_number

amendment_type

reason

previous_version

new_version

approved_by

approved_at
```

---

# 92. Purchasing Relationships

```text
proc_purchase_orders

        │

        ├── proc_purchase_order_lines

        ├── proc_purchase_order_delivery_schedules

        ├── proc_purchase_order_allocations

        ├── proc_purchase_order_taxes

        ├── proc_purchase_order_attachments

        └── proc_purchase_order_amendments
```

---

# 93. Purchasing Summary

The Purchasing schema provides the transactional foundation for procurement execution.

It supports enterprise purchasing through standardized Purchase Orders, configurable delivery schedules, organizational allocations, taxation, amendments, and document management while integrating seamlessly with Strategic Sourcing, Inventory, Finance, Workflow, and the Platform Event Bus.

This schema represents the legally binding commercial commitment between the organization and its suppliers and serves as the authoritative source for Receiving and Invoice Matching.

---

# 94. Receiving Schema

The Receiving schema manages the verification, inspection, acceptance, and recording of goods and services delivered by suppliers.

The Receiving process confirms that suppliers have fulfilled their contractual obligations before inventory is updated and supplier invoices are processed.

The Receiving schema supports:

- Goods Receipt Notes (GRNs)
- Service Receipt Notes (SRNs)
- Asset Receipt Notes
- Delivery Verification
- Quantity Verification
- Quality Inspection
- Batch Verification
- Serial Number Verification
- Expiry Verification
- Supplier Returns
- Receiving Exceptions

The Procurement Module owns the receiving documents.

The Inventory Engine owns inventory transactions and inventory balances.

---

# 95. Receiving Aggregate

The Receiving aggregate follows the standard Business Suite document pattern.

```text
Goods Receipt

│

├── Receipt Lines

├── Batch Details

├── Serial Numbers

├── Quality Inspections

├── Delivery Verification

├── Attachments

├── Returns

├── Workflow

└── Audit History
```

Goods Receipt is the aggregate root.

---

# 96. Receiving Ownership

The Procurement Module owns:

- Goods Receipts
- Service Receipts
- Receipt Lines
- Delivery Verification
- Inspection Results
- Supplier Returns
- Receiving Exceptions

The Inventory Engine owns:

- Inventory Transactions
- Stock Balances
- Warehouse Quantities
- Bin Quantities
- Batch Inventory
- Lot Inventory
- Serial Inventory

The Finance Engine owns:

- Inventory Valuation
- Accounts Payable
- Financial Posting

---

# 97. Receiving Relationships

```text
Purchase Order

↓

Goods Receipt

│

├── Inspection

├── Inventory Transaction

├── Supplier Return

├── Invoice Matching

└── Accounts Payable
```

A Purchase Order may generate multiple Goods Receipts.

Each Goods Receipt may generate one or more Inventory Transactions.

---

# 98. Receiving Database Rules

The Receiving schema shall enforce the following rules.

- Every Goods Receipt belongs to one Tenant.
- Every Goods Receipt references an approved Purchase Order.
- Goods Receipts support partial deliveries.
- Accepted quantities create Inventory Transactions.
- Rejected quantities never update inventory.
- Batch-controlled items require Batch Numbers.
- Serial-controlled items require Serial Numbers.
- Expiry-controlled items require Expiry Dates.
- Every Goods Receipt publishes business events.
- All Receiving tables support Row Level Security (RLS).

---

# 99. proc_goods_receipts

## Purpose

Stores Goods Receipt Note (GRN) headers.

---

## Key Fields

```text
id

tenant_id

company_id

branch_id

department_id

goods_receipt_number

purchase_order_id

supplier_id

warehouse_id

receipt_date

delivery_note_number

supplier_delivery_reference

vehicle_registration

received_by

received_from

subtotal

tax_amount

total_amount

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

- Goods Receipt Number generated by Document Numbering Engine.
- One Goods Receipt references one Purchase Order.
- One Purchase Order may have multiple Goods Receipts.
- Goods Receipt becomes immutable after approval.

---

## Events

```text
GoodsReceiptCreated

GoodsReceiptSubmitted

GoodsReceiptApproved

GoodsReceiptCompleted

GoodsReceiptCancelled
```

---

# 100. proc_goods_receipt_lines

## Purpose

Stores received inventory, services, or assets.

---

## Key Fields

```text
id

tenant_id

goods_receipt_id

purchase_order_line_id

line_number

item_id

description

ordered_quantity

received_quantity

accepted_quantity

rejected_quantity

damaged_quantity

unit_of_measure_id

warehouse_id

inspection_required

inspection_status

status
```

---

## Business Rules

- Accepted Quantity updates Inventory.
- Rejected Quantity remains outside inventory.
- Damaged Quantity may generate Supplier Returns.

---

# 101. proc_receipt_batches

## Purpose

Stores batch information captured during receiving.

Applicable only for batch-controlled inventory.

---

## Key Fields

```text
id

tenant_id

goods_receipt_line_id

batch_number

manufacturer_batch

manufacturing_date

expiry_date

received_quantity

accepted_quantity
```

Inventory Engine validates and owns batch inventory.

---

# 102. proc_receipt_serial_numbers

## Purpose

Stores serial numbers captured during receiving.

Applicable only for serialized inventory.

---

## Key Fields

```text
id

tenant_id

goods_receipt_line_id

serial_number

manufacturer_serial

status
```

Inventory Engine validates serial uniqueness.

---

# 103. proc_quality_inspections

## Purpose

Stores inspection results.

---

## Inspection Types

```text
Visual

Functional

Laboratory

Technical

Safety

Packaging
```

---

## Key Fields

```text
id

tenant_id

goods_receipt_line_id

inspection_type

inspection_date

inspector_id

result

accepted_quantity

rejected_quantity

remarks
```

---

## Inspection Results

```text
Passed

Failed

Conditional

Pending
```

---

# 104. proc_supplier_returns

## Purpose

Stores supplier return documents.

Returns are created from rejected or damaged goods.

---

## Key Fields

```text
id

tenant_id

return_number

goods_receipt_id

supplier_id

return_reason

return_date

workflow_status

approval_status

document_status
```

---

## Return Reasons

```text
Damaged

Expired

Wrong Item

Wrong Quantity

Failed Inspection

Warranty Claim
```

---

# 105. proc_supplier_return_lines

## Purpose

Stores returned line items.

---

## Key Fields

```text
id

tenant_id

supplier_return_id

goods_receipt_line_id

item_id

returned_quantity

reason

remarks
```

---

# 106. proc_receiving_exceptions

## Purpose

Stores receiving exceptions.

---

## Examples

```text
Missing Items

Over Delivery

Short Delivery

Damaged Goods

Incorrect Batch

Incorrect Serial

Expired Goods

Wrong Product
```

---

## Key Fields

```text
id

tenant_id

goods_receipt_id

exception_type

severity

description

resolution_status

resolved_by

resolved_at
```

---

# 107. Receiving Relationships

```text
proc_goods_receipts

        │

        ├── proc_goods_receipt_lines

        ├── proc_receipt_batches

        ├── proc_receipt_serial_numbers

        ├── proc_quality_inspections

        ├── proc_supplier_returns

        │        └── proc_supplier_return_lines

        └── proc_receiving_exceptions
```

---

# 108. Receiving Schema Summary

The Receiving schema manages the operational acceptance of supplier deliveries while maintaining strict separation of responsibilities across the Business Suite.

It records Goods Receipt Notes, delivery verification, inspections, batch information, serial numbers, supplier returns, and receiving exceptions before requesting inventory updates from the Inventory Engine.

By ensuring that only accepted quantities generate inventory transactions, the schema preserves inventory accuracy, supports financial integrity, and provides complete traceability from Purchase Order through Goods Receipt, Inventory, Invoice Matching, and Accounts Payable.

The Receiving schema forms the operational bridge between Procurement and Inventory while maintaining a clean engine ownership model consistent with the overall Business Suite architecture.

---

# 109. Invoice Matching Schema

The Invoice Matching schema validates supplier invoices against procurement transactions before they are released to the Finance Engine for Accounts Payable processing.

The objective is to ensure suppliers are only paid for:

- Approved Procurement
- Approved Purchase Orders
- Accepted Goods
- Accepted Services
- Agreed Prices
- Approved Taxes

The Procurement Module owns the matching process.

The Finance Engine owns:

- Supplier Invoice Posting
- Accounts Payable
- Vendor Ledger
- Payment Processing
- General Ledger
- Tax Accounting

---

# 110. Invoice Matching Aggregate

The Invoice Matching aggregate follows the Business Suite document architecture.

```text
Invoice Match

│

├── Invoice

├── Match Lines

├── Variances

├── Holds

├── Exceptions

├── Workflow

├── Timeline

└── Audit
```

Invoice Match is the aggregate root.

---

# 111. Invoice Matching Ownership

The Procurement Module owns:

- Invoice Matching
- Match Results
- Variances
- Holds
- Exceptions

The Finance Engine owns:

- Supplier Invoice
- Accounts Payable
- Vendor Ledger
- Vendor Payments

The Workflow Engine owns:

- Exception Approval
- Override Approval

---

# 112. Invoice Matching Relationships

```text
Purchase Order

↓

Goods Receipt

↓

Invoice Matching

↓

Finance Engine

↓

Accounts Payable

↓

Vendor Payment
```

One Purchase Order may have multiple invoices.

One Goods Receipt may appear on multiple invoices where partial billing is supported.

---

# 113. Invoice Matching Database Rules

The Invoice Matching schema shall enforce the following rules.

- Every Invoice Match belongs to one Tenant.
- Every Invoice Match references one Supplier.
- Every Invoice Match references one or more Purchase Orders or Goods Receipts.
- Duplicate supplier invoices are not permitted.
- Matching results are immutable after approval.
- Invoice Holds prevent Finance posting.
- Variances beyond configured tolerances require approval.
- Every Invoice Match publishes business events.
- All Invoice Matching tables support Row Level Security (RLS).

---

# 114. proc_invoice_matches

## Purpose

Stores Invoice Match headers.

---

## Key Fields

```text
id

tenant_id

company_id

branch_id

invoice_match_number

supplier_id

supplier_invoice_number

supplier_invoice_date

invoice_received_date

currency_id

exchange_rate

subtotal

discount_amount

tax_amount

total_amount

matching_method

matching_status

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

## Matching Methods

```text
Two-Way Match

Three-Way Match

Four-Way Match
```

---

## Events

```text
InvoiceMatchCreated

InvoiceMatched

InvoiceHeld

InvoiceReleasedToFinance

InvoiceRejected
```

---

# 115. proc_invoice_match_lines

## Purpose

Stores invoice matching detail lines.

---

## Key Fields

```text
id

tenant_id

invoice_match_id

purchase_order_line_id

goods_receipt_line_id

item_id

description

ordered_quantity

received_quantity

invoiced_quantity

matched_quantity

unit_price

invoice_price

line_total

status
```

---

## Business Rules

- Line quantities must not exceed accepted quantities unless approved.
- Price validation uses Purchase Order pricing.
- Goods Receipt validation uses accepted quantities.

---

# 116. proc_invoice_variances

## Purpose

Stores detected matching variances.

---

## Variance Types

```text
Price Variance

Quantity Variance

Tax Variance

Currency Variance

Delivery Variance

Contract Variance
```

---

## Key Fields

```text
id

tenant_id

invoice_match_line_id

variance_type

expected_value

actual_value

variance_amount

variance_percentage

within_tolerance

requires_approval

status
```

---

# 117. proc_invoice_holds

## Purpose

Stores Invoice Holds.

Invoice Holds prevent invoices from reaching the Finance Engine.

---

## Hold Reasons

```text
Missing Goods Receipt

Duplicate Invoice

Price Difference

Quantity Difference

Missing Approval

Missing Contract

Missing Inspection

Compliance Issue
```

---

## Key Fields

```text
id

tenant_id

invoice_match_id

hold_reason

hold_date

released_by

released_at

status
```

---

# 118. proc_invoice_matching_exceptions

## Purpose

Stores invoice matching exceptions.

---

## Examples

```text
Duplicate Invoice

Supplier Mismatch

Invalid Purchase Order

Currency Difference

Tax Difference

Over Billing

Under Billing
```

---

## Key Fields

```text
id

tenant_id

invoice_match_id

exception_type

severity

description

resolution_status

resolved_by

resolved_at
```

---

# 119. Invoice Matching Relationships

```text
proc_invoice_matches

        │

        ├── proc_invoice_match_lines

        ├── proc_invoice_variances

        ├── proc_invoice_holds

        └── proc_invoice_matching_exceptions
```

---

# 120. Invoice Matching Schema Summary

The Invoice Matching schema provides the financial validation layer between Procurement and the Finance Engine.

It verifies supplier invoices against Purchase Orders, Goods Receipts, pricing agreements, and procurement policies before releasing approved invoices to Accounts Payable.

By supporting configurable matching methods, variance detection, invoice holds, and exception management, the schema protects the organization against duplicate payments, overbilling, incorrect pricing, and unauthorized supplier claims.

This schema forms the final operational checkpoint before financial liability is recognized by the Finance Engine.

---

# 121. Finance Integration References

The Procurement Module integrates tightly with the Finance Engine while maintaining strict ownership boundaries.

The Procurement database stores only financial references required to execute procurement processes.

The Finance Engine remains the authoritative owner of:

- Budgets
- Budget Lines
- Budget Commitments
- Accounts Payable
- Vendor Ledger
- Vendor Payments
- Taxes
- Bank Accounts
- Cash Management
- General Ledger
- Financial Reporting

The Procurement Module must never duplicate Finance data.

---

# 122. Finance Integration Architecture

```text
Procurement

↓

Budget Validation

↓

Budget Commitment

↓

Purchase Order

↓

Goods Receipt

↓

Invoice Matching

↓

Finance Engine

↓

Accounts Payable

↓

Vendor Payment

↓

General Ledger
```

The Procurement Module exchanges business events with the Finance Engine through the Platform Event Bus.

---

# 123. Financial Reference Model

Rather than storing financial records, Procurement stores references.

Examples include:

```text
budget_id

budget_line_id

cost_center_id

project_id

grant_id

currency_id

exchange_rate

payment_terms_id

tax_profile_id

tax_category_id

vendor_account_reference

accounts_payable_reference

payment_reference

journal_reference
```

These references enable traceability without violating ownership.

---

# 124. Budget Integration

Procurement references Finance Budgets throughout the procurement lifecycle.

Budget validation occurs before:

- Procurement Request Approval
- Purchase Order Approval
- Contract Approval (where applicable)

Budget commitment occurs when:

```text
Approved Procurement

↓

Purchase Order

↓

Finance Engine

↓

Budget Reserved
```

Budget consumption occurs when:

```text
Invoice Matched

↓

Finance Engine

↓

Expense Recorded

OR

Inventory Capitalized
```

---

# 125. Accounts Payable Integration

Invoice Matching releases approved supplier invoices to the Finance Engine.

The Procurement Module stores:

```text
accounts_payable_reference

invoice_reference

supplier_reference

invoice_match_reference
```

The Finance Engine creates:

- Accounts Payable Entries
- Due Dates
- Payment Schedules
- Vendor Ledger Entries

---

# 126. Vendor Payment References

Supplier payments remain owned by the Finance Engine.

Procurement stores payment references for operational visibility.

Examples:

```text
payment_reference

payment_status

payment_date

payment_amount

currency_id

payment_method
```

Procurement users may view payment progress but cannot modify financial records.

---

# 127. Tax References

The Procurement Module stores procurement tax references.

Examples:

```text
tax_category_id

tax_profile_id

tax_code

vat_registration_number

withholding_tax_reference
```

Tax calculations and postings remain the responsibility of the Finance Engine.

---

# 128. Multi-Currency References

Procurement supports purchasing in multiple currencies.

Reference fields include:

```text
currency_id

exchange_rate

exchange_rate_date

base_currency_amount

transaction_currency_amount
```

Exchange rate maintenance belongs to the Finance Engine.

---

# 129. Financial Events

The Procurement Module publishes financial integration events.

Examples:

```text
BudgetValidationRequested

BudgetValidated

BudgetReservationRequested

BudgetReserved

BudgetReleased

InvoiceReleasedToFinance

AccountsPayableReferenceCreated

VendorPaymentCompleted

RetentionReleased
```

The Finance Engine subscribes to these events and performs the accounting operations.

---

# 130. Finance Integration Summary

The Finance Integration layer provides controlled communication between Procurement and the Finance Engine.

By storing only financial references while delegating accounting responsibilities to the Finance Engine, the Business Suite maintains clear domain ownership, prevents data duplication, and preserves the integrity of the event-driven architecture.

Every procurement transaction can be traced to its corresponding financial records without compromising the Finance Engine as the single source of truth for accounting, taxation, budgeting, banking, and vendor payments.

---

# 131. Contract Management Schema

The Contract Management schema manages the complete lifecycle of supplier contracts and commercial agreements.

Unlike Purchase Orders, which represent individual purchasing transactions, Contracts represent long-term legal agreements that govern future procurement activities.

The schema supports:

- Supplier Contracts
- Framework Agreements
- Service Agreements
- Supply Agreements
- Consultancy Contracts
- Maintenance Contracts
- Construction Contracts
- Contract Pricing
- Contract Milestones
- Contract Amendments
- Contract Renewals
- Call-Off Orders
- Contract Performance
- Contract Utilization
- Contract Expiry Monitoring

The Contract Management schema becomes the authoritative repository for procurement contracts throughout the Business Suite.

---

# 132. Contract Management Aggregate

The Contract aggregate follows the standard Business Suite document architecture.

```text
Contract

│

├── Contract Lines

├── Pricing

├── Milestones

├── Call-Off Orders

├── Amendments

├── Renewals

├── Performance

├── Attachments

├── Workflow

└── Audit History
```

The Contract is the aggregate root.

---

# 133. Contract Management Ownership

The Procurement Module owns:

- Contracts
- Contract Lines
- Contract Pricing
- Contract Milestones
- Contract Amendments
- Contract Renewals
- Call-Off Orders
- Contract Performance
- Contract Utilization

Other modules own:

Finance Engine

- Contract Accounting
- Retentions
- Accounts Payable

Document Management Engine

- Signed Contracts
- Contract Attachments

Workflow Engine

- Contract Approval

Notification Engine

- Renewal Notifications
- Expiry Alerts

Activity & Audit Engine

- Contract Audit History

---

# 134. Contract Relationships

```text
Supplier

↓

Contract

│

├── Purchase Orders

├── Call-Off Orders

├── Goods Receipts

├── Invoice Matching

├── Vendor Payments

└── Performance Reviews
```

One Contract may generate many Purchase Orders and many Call-Off Orders.

---

# 135. Contract Database Rules

The Contract schema shall enforce the following rules.

- Every Contract belongs to one Tenant.
- Every Contract has a unique document number.
- Approved Contracts are immutable.
- Amendments create new versions.
- Renewals create new contract periods.
- Contract pricing becomes available to Purchase Orders.
- Contract utilization is calculated automatically.
- Contract expiry generates notifications.
- Every Contract publishes business events.
- All Contract tables support Row Level Security (RLS).

---

# 136. proc_contracts

## Purpose

Stores Contract headers.

---

## Key Fields

```text
id

tenant_id

company_id

branch_id

department_id

contract_number

supplier_id

contract_type

contract_title

contract_description

contract_start_date

contract_end_date

contract_value

currency_id

payment_terms_id

delivery_terms_id

incoterm_id

retention_percentage

workflow_status

approval_status

document_status

status

remarks

created_at

created_by

updated_at

updated_by
```

---

## Contract Types

```text
Framework Agreement

Supply Agreement

Service Agreement

Maintenance Agreement

Consultancy Agreement

Construction Contract

Standing Agreement
```

---

## Events

```text
ContractCreated

ContractApproved

ContractActivated

ContractRenewed

ContractExpired

ContractTerminated
```

---

# 137. proc_contract_lines

## Purpose

Stores items, services, or deliverables covered by the contract.

---

## Key Fields

```text
id

tenant_id

contract_id

line_number

item_id

service_id

description

quantity_limit

unit_of_measure_id

contract_unit_price

maximum_value

status
```

---

# 138. proc_contract_pricing

## Purpose

Stores contract pricing rules.

Supports:

- Fixed Price
- Tiered Pricing
- Volume Pricing
- Framework Pricing
- Time & Materials
- Milestone Pricing

---

## Key Fields

```text
id

tenant_id

contract_line_id

pricing_method

minimum_quantity

maximum_quantity

unit_price

effective_from

effective_to

status
```

---

# 139. proc_contract_milestones

## Purpose

Stores contract milestones.

---

## Milestone Types

```text
Delivery

Implementation

Inspection

Payment

Warranty

Project Completion
```

---

## Key Fields

```text
id

tenant_id

contract_id

milestone_name

milestone_type

planned_date

actual_date

completion_percentage

status
```

---

# 140. proc_call_off_orders

## Purpose

Stores Purchase Releases created from Framework Agreements.

---

## Key Fields

```text
id

tenant_id

contract_id

purchase_order_id

call_off_number

call_off_date

call_off_value

currency_id

status
```

One Framework Agreement may generate unlimited Call-Off Orders.

---

# 141. proc_contract_amendments

## Purpose

Stores Contract amendments.

Approved Contracts are never edited directly.

---

## Amendment Types

```text
Price Revision

Scope Change

Extension

Supplier Change

Quantity Revision

Commercial Terms
```

---

## Key Fields

```text
id

tenant_id

contract_id

amendment_number

amendment_type

reason

previous_version

new_version

approved_by

approved_at
```

---

# 142. proc_contract_renewals

## Purpose

Stores Contract renewals.

---

## Key Fields

```text
id

tenant_id

contract_id

renewal_number

renewal_date

new_start_date

new_end_date

renewal_value

workflow_status

approval_status

status
```

---

# 143. proc_contract_performance

## Purpose

Stores contract performance metrics.

---

## Performance Metrics

- Delivery Performance
- SLA Compliance
- Milestone Completion
- Defect Rate
- Warranty Claims
- Supplier Responsiveness

---

## Key Fields

```text
id

tenant_id

contract_id

evaluation_period

delivery_score

sla_score

quality_score

responsiveness_score

overall_score

calculated_at
```

---

# 144. proc_contract_utilization

## Purpose

Tracks utilization of Contract value.

---

## Key Fields

```text
id

tenant_id

contract_id

original_contract_value

purchase_order_value

remaining_value

utilization_percentage

last_calculated
```

Utilization updates automatically as Purchase Orders are issued.

---

# 145. Contract Management Summary

The Contract Management schema provides the enterprise foundation for long-term supplier agreements.

It supports contract lifecycle management, pricing, milestones, framework purchasing, renewals, amendments, utilization tracking, and supplier performance while integrating seamlessly with Purchasing, Receiving, Finance, Workflow, and the Platform Event Bus.

This schema enables organizations to manage contracts as strategic business assets rather than static documents, providing complete visibility into commercial obligations, contract consumption, and supplier commitments throughout the procurement lifecycle.

---

# 146. Procurement Analytics Schema

The Procurement Analytics schema stores procurement metrics, KPI snapshots, supplier performance summaries, and analytical data produced by the Procurement Module.

Unlike operational tables, analytics tables are optimized for reporting, dashboards, trend analysis, and executive decision support.

The Reporting Engine consumes these datasets to generate dashboards, scheduled reports, visualizations, and exports.

The Procurement Module owns the procurement metrics.

The Reporting Engine owns report rendering.

---

# 147. Procurement Analytics Aggregate

```text
Analytics

│

├── KPI Snapshots

├── Procurement Trends

├── Spend Analysis

├── Supplier Analytics

├── Contract Analytics

├── Compliance Analytics

├── Operational Metrics

└── Executive Dashboards
```

Analytics are generated from operational procurement transactions.

---

# 148. Procurement Analytics Ownership

The Procurement Module owns:

- Procurement KPIs
- Procurement Trend Snapshots
- Supplier Performance Snapshots
- Contract Utilization Metrics
- Compliance Metrics
- Procurement Cycle Metrics

The Reporting Engine owns:

- Reports
- Dashboards
- Charts
- Scheduled Reports
- Exports

The Finance Engine owns:

- Financial Statements
- Actual Spend
- Accounts Payable
- Budget Reporting

---

# 149. Analytics Relationships

```text
Procurement

↓

Operational Tables

↓

Analytics Snapshot

↓

Reporting Engine

↓

Dashboards

↓

Business Intelligence
```

Analytics never modify operational procurement records.

---

# 150. Analytics Database Rules

The Procurement Analytics schema shall enforce the following rules.

- Analytics are read-only.
- Analytics are generated from operational data.
- Financial values originate from the Finance Engine.
- Supplier metrics originate from Supplier Management.
- KPI calculations are version controlled.
- Historical analytics are retained.
- Analytics support Row Level Security (RLS).
- Analytics publish update events.

---

# 151. proc_procurement_kpis

## Purpose

Stores procurement KPI snapshots.

---

## Example KPIs

- Procurement Cycle Time
- Average Approval Time
- Average Supplier Lead Time
- Procurement Savings
- Purchase Order Cycle Time
- Invoice Match Rate
- Supplier Delivery Rate
- Procurement Compliance Rate

---

## Key Fields

```text
id

tenant_id

snapshot_date

kpi_code

kpi_name

kpi_value

target_value

variance

measurement_period

generated_at
```

---

# 152. proc_supplier_analytics

## Purpose

Stores summarized supplier analytical information.

---

## Example Metrics

- Total Spend
- Total Purchase Orders
- Total Contracts
- Delivery Performance
- Supplier Rating
- Average Lead Time
- Quality Score
- Risk Rating

---

## Key Fields

```text
id

tenant_id

supplier_id

analysis_period

total_purchase_orders

total_contracts

total_procurement_value

delivery_score

quality_score

risk_score

overall_rating

generated_at
```

---

# 153. proc_procurement_trends

## Purpose

Stores procurement trend snapshots.

---

## Examples

- Monthly Procurement Spend
- Purchase Orders by Month
- Procurement by Category
- Procurement by Department
- Procurement by Branch
- Procurement by Project
- Procurement by Grant

---

## Key Fields

```text
id

tenant_id

analysis_period

metric_name

metric_value

comparison_value

growth_percentage

generated_at
```

---

# 154. proc_contract_analytics

## Purpose

Stores contract utilization and performance summaries.

---

## Example Metrics

- Active Contracts
- Contract Value
- Contract Utilization
- Expiring Contracts
- Renewal Rate
- Framework Usage

---

## Key Fields

```text
id

tenant_id

contract_id

analysis_period

utilization_percentage

remaining_value

performance_score

renewal_probability

generated_at
```

---

# 155. proc_compliance_analytics

## Purpose

Stores procurement compliance statistics.

---

## Example Metrics

- Policy Compliance
- Procurement Exceptions
- Emergency Procurement
- Sole Source Procurement
- Tender Compliance
- Conflict Declarations
- Approval Delays

---

## Key Fields

```text
id

tenant_id

analysis_period

compliance_rate

exception_count

policy_override_count

emergency_procurements

conflict_declarations

average_resolution_time

generated_at
```

---

# 156. Procurement Analytics Summary

The Procurement Analytics schema provides the analytical foundation for procurement intelligence across the Business Suite.

It captures KPI snapshots, supplier analytics, procurement trends, contract performance, and compliance metrics while preserving the separation between operational procurement data and business intelligence.

By integrating with the Reporting Engine, Finance Engine, Supplier Management, and Contract Management, the schema enables real-time dashboards, historical analysis, executive reporting, and strategic decision-making without impacting transactional performance.

This completes the business data model for the Procurement & Supplier Management Module and establishes the analytical layer required for enterprise procurement governance and continuous operational improvement.

---

# 157. Database Relationships

The Procurement database is designed around aggregate ownership and domain boundaries.

The following relationship model illustrates how Procurement interacts internally and with other Business Suite modules.

```text
Procurement Plan

        │

        ▼

Procurement Request

        │

        ▼

Compliance Validation

        │

        ▼

Strategic Sourcing

        │

        ▼

Supplier Evaluation

        │

        ▼

Procurement Award

        │

        ▼

Purchase Order

        │

        ▼

Goods Receipt

        │

        ▼

Invoice Matching

        │

        ▼

Finance Engine

        │

        ▼

Vendor Payment
```

Supporting relationships include:

```text
Supplier

│

├── RFQs

├── Quotations

├── Purchase Orders

├── Contracts

├── Performance

├── Risk

└── Payments (Finance)
```

The Inventory Engine is referenced during:

- Purchase Orders
- Goods Receipts
- Supplier Returns

The Finance Engine is referenced during:

- Budget Validation
- Budget Commitments
- Accounts Payable
- Vendor Payments

The Workflow Engine is referenced by every approval-based document.

---

# 158. Referential Integrity

All Procurement relationships shall enforce referential integrity.

Examples include:

```text
Purchase Order

↓

Supplier

Purchase Order

↓

Procurement Award

Goods Receipt

↓

Purchase Order

Invoice Match

↓

Goods Receipt

Contract

↓

Supplier
```

Foreign Keys shall prevent orphaned business records.

Cascade deletes shall not be permitted for transactional documents.

Soft deletion shall be used where required.

---

# 159. Database Constraints

The Procurement database shall enforce:

## Primary Keys

Every table uses:

```text
UUID
```

---

## Foreign Keys

Examples:

```text
supplier_id

purchase_order_id

contract_id

goods_receipt_id

invoice_match_id

warehouse_id
```

---

## Unique Constraints

Examples:

```text
tenant_id + supplier_number

tenant_id + purchase_order_number

tenant_id + goods_receipt_number

tenant_id + contract_number

tenant_id + invoice_match_number
```

---

## Check Constraints

Examples:

```text
quantity >= 0

accepted_quantity <= received_quantity

expiry_date >= manufacturing_date

contract_end_date >= contract_start_date

maximum_amount >= minimum_amount
```

---

## Status Constraints

Document statuses shall only allow configured values.

Examples:

```text
Draft

Submitted

Approved

Rejected

Cancelled

Closed
```

Reference Data Engine manages status values.

---

# 160. Indexing Strategy

The Procurement Module shall implement enterprise indexing standards.

Indexes should exist for:

```text
tenant_id

company_id

branch_id

department_id

supplier_id

document_status

workflow_status

approval_status

created_at

document_date
```

Composite indexes:

```text
tenant_id + supplier_id

tenant_id + purchase_order_number

tenant_id + document_status

tenant_id + branch_id + document_date

tenant_id + supplier_id + document_date
```

Search-heavy tables should include covering indexes for dashboard queries.

---

# 161. Partitioning Strategy

Large transactional tables should support PostgreSQL partitioning.

Recommended partition candidates:

```text
proc_purchase_orders

proc_purchase_order_lines

proc_goods_receipts

proc_goods_receipt_lines

proc_invoice_matches

proc_procurement_requests

proc_supplier_performance
```

Recommended partition strategies:

- Financial Year
- Tenant
- Company
- Document Date

Partitioning should remain transparent to the application layer.

---

# 162. Security & Row Level Security (RLS)

Every Procurement table shall support PostgreSQL Row Level Security.

Security boundaries:

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

Authorization is enforced using:

- Authorization Engine
- Platform Core
- PostgreSQL RLS

The Procurement Module shall never implement its own authorization logic.

---

# 163. Audit Integration

The Procurement Module integrates with the Platform Activity & Audit Engine.

The Procurement database stores business data only.

Audit records are maintained externally.

Examples of audited events:

- Supplier Created
- Supplier Approved
- Procurement Request Submitted
- Procurement Request Approved
- RFQ Published
- Supplier Response Submitted
- Purchase Order Approved
- Goods Received
- Supplier Return Created
- Invoice Released to Finance
- Contract Renewed

Every business event shall include:

```text
Correlation ID

Tenant

Company

Branch

User

Timestamp

Business Entity

Action
```

---

# 164. Event Integration

The Procurement Module publishes events through the Platform Event Bus.

Examples include:

```text
SupplierApproved

ProcurementPlanApproved

ProcurementRequestApproved

ComplianceValidationPassed

RFQPublished

QuotationSubmitted

AwardApproved

PurchaseOrderIssued

GoodsReceiptApproved

InvoiceReleasedToFinance

ContractActivated

SupplierPerformanceUpdated
```

The Platform Event Bus guarantees reliable event delivery.

---

# 165. Document Integration

The Procurement Module consumes:

- Document Numbering Engine
- Document Management Engine

Document Numbering provides:

```text
Supplier Number

Procurement Plan Number

Request Number

RFQ Number

Purchase Order Number

Goods Receipt Number

Invoice Match Number

Contract Number

Supplier Return Number
```

Document Management stores:

```text
Contracts

Supplier Documents

Purchase Orders

Inspection Reports

Delivery Notes

Supplier Quotations

Evaluation Reports

Invoices

Attachments
```

The Procurement Module stores document references only.

---

# 166. Search Integration

The Procurement Module integrates with the Search & Indexing Engine.

Indexed entities include:

```text
Suppliers

Procurement Plans

Procurement Requests

RFQs

Purchase Orders

Goods Receipts

Invoice Matches

Contracts

Supplier Returns
```

Search respects:

- Authorization Engine
- Tenant Isolation
- RLS Policies

---

# 167. Reporting Integration

The Procurement Module publishes procurement datasets to the Reporting Engine.

The Reporting Engine generates:

- Dashboards
- KPIs
- Scheduled Reports
- Executive Reports
- Operational Reports
- Drill-down Analytics

Common reporting datasets include:

```text
Supplier Summary

Procurement Pipeline

Purchase Orders

Goods Receipts

Invoice Matching

Contract Utilization

Supplier Performance

Procurement Compliance

Procurement Spend
```

The Procurement Module supplies business metrics.

The Reporting Engine owns visualization.

---

# 168. DATABASE.md Summary

The Procurement & Supplier Management database defines the complete enterprise data architecture supporting the entire Source-to-Pay and Procure-to-Pay lifecycle.

The schema is organized into the following business domains:

- Procurement Planning
- Supplier Management
- Procurement Requests
- Procurement Compliance
- Strategic Sourcing
- Purchasing
- Receiving
- Invoice Matching
- Finance Integration
- Contract Management
- Procurement Analytics

The database follows the Business Suite architectural principles:

- Domain-Driven Design (DDD)
- Aggregate Ownership
- Multi-Tenant Isolation
- PostgreSQL Best Practices
- Event-Driven Architecture
- API-First Integration
- Document-Based Transactions
- Immutable Business History
- Platform Engine Reuse

By maintaining clear ownership boundaries between Procurement, Finance, Inventory, Workflow, Reporting, Search, and the other Platform Engines, the Procurement Module remains the authoritative source for procurement business data while seamlessly integrating with the rest of the Business Suite Enterprise Platform.

This design provides a scalable, auditable, and enterprise-grade procurement foundation capable of supporting SMEs, large enterprises, NGOs, government institutions, and multinational organizations.
