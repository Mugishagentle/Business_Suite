---
# 79. Integration Tables

The Inventory Engine integrates with Platform Engines and Business Modules through lightweight reference tables and integration contracts.

The Inventory Engine **does not duplicate business data** owned by other modules.

Instead, it stores references to external business records where operational relationships are required.

```text
Inventory Engine

│

├── Finance References

├── Sales References

├── Procurement References

├── CRM References

├── Manufacturing References

├── Projects References

├── Rental References

├── Subscription References

└── Document References
```
---

# 80. inv_inventory_sales_links

## Purpose

Links Inventory records with Sales documents.

Inventory owns:

- Inventory Operations
- Warehouse Operations

Sales owns:

- Quotations
- Sales Orders
- Delivery Requests
- Sales Invoices

---

## Relationships

```text
Sales Order

↓

Inventory Reservation

↓

Allocation

↓

Picking

↓

Packing

↓

Dispatch

↓

Sales Updated
```

---

## Key Fields

```text
id

tenant_id

inventory_transaction_id

sales_order_id

sales_order_line_id

quotation_id

invoice_id

delivery_request_id

linked_at
```

---

## Business Rules

- Inventory never owns Sales documents.
- Inventory stores references only.
- Multiple Inventory Transactions may relate to one Sales Order.

---

# 81. inv_inventory_procurement_links

## Purpose

Links Inventory operations with Procurement documents.

Procurement owns:

- Suppliers
- Purchase Orders
- RFQs
- Goods Ordered

Inventory owns:

- Goods Receipt
- Warehouse Receipt
- Inventory Transactions

---

## Relationships

```text
Purchase Order

↓

Goods Receipt

↓

Inventory Transaction

↓

Inventory Balance
```

---

## Key Fields

```text
id

tenant_id

goods_receipt_id

purchase_order_id

purchase_order_line_id

supplier_id

linked_at
```

---

## Business Rules

Inventory references Procurement.

Procurement remains the owner of purchasing.

---

# 82. inv_inventory_finance_links

## Purpose

Provides the relationship between Inventory Transactions and Finance.

Finance owns:

- Inventory Asset Accounts
- Cost of Goods Sold
- Journal Entries
- General Ledger
- Inventory Valuation

Inventory owns:

- Operational Quantity
- Warehouse Movements
- Inventory Transactions

---

## Relationships

```text
Inventory Transaction

↓

Finance Event

↓

Journal Entry

↓

General Ledger
```

---

## Key Fields

```text
id

tenant_id

inventory_transaction_id

journal_entry_id

ledger_transaction_id

finance_reference

posted_at
```

---

## Business Rules

Inventory never posts accounting entries.

Finance determines accounting treatment.

Inventory publishes events only.

---

# 83. inv_inventory_crm_links

## Purpose

Links Inventory operations with CRM Accounts.

CRM owns:

- Customers
- Delivery Addresses
- Contacts

Inventory references these records during fulfilment.

---

## Key Fields

```text
id

tenant_id

customer_account_id

delivery_address_id

contact_id

inventory_reference
```

---

## Business Rules

Inventory stores references only.

CRM remains the Customer Master.

---

# 84. inv_inventory_project_links

## Purpose

Supports material allocation for Projects.

Projects own:

- Projects
- Tasks
- Work Packages

Inventory owns:

- Material Reservations
- Material Allocations
- Material Consumption

---

## Key Fields

```text
id

tenant_id

project_id

task_id

inventory_transaction_id
```

---

# 85. inv_inventory_manufacturing_links

## Purpose

Supports Manufacturing integration.

Manufacturing owns:

- Production Orders
- Bills of Materials
- Production Planning

Inventory owns:

- Raw Material Consumption
- Finished Goods Receipt

---

## Key Fields

```text
id

tenant_id

production_order_id

inventory_transaction_id

material_issue_reference

finished_goods_reference
```

---

# 86. inv_inventory_rental_links

## Purpose

Supports Rental Management.

Rental business processes consume inventory.

Inventory owns:

- Rental Item Availability
- Rental Check-Out
- Rental Check-In

Rental Module owns:

- Rental Agreements
- Rental Billing
- Rental Scheduling

---

## Key Fields

```text
id

tenant_id

rental_agreement_id

inventory_transaction_id

serial_number_id

check_out_date

check_in_date
```

---

# 87. inv_inventory_subscription_links

## Purpose

Supports Subscription Products.

Inventory manages:

- Subscription Product Definitions
- Digital Entitlements
- License Allocation

Finance manages:

- Billing

CRM manages:

- Customer Relationship

---

## Key Fields

```text
id

tenant_id

subscription_id

license_id

customer_account_id

activation_reference
```

---

# 88. inv_inventory_document_links

## Purpose

Maintains references to documents stored by the Document Management Engine.

Examples include:

- Product Images
- Technical Manuals
- Certificates
- Inspection Reports
- Packing Lists
- Shipping Labels
- Compliance Documents
- Warranty Documents

Inventory stores only document references.

---

## Key Fields

```text
id

tenant_id

inventory_record_type

inventory_record_id

document_reference_id

document_type
```

---

# 89. Reference Data Dependencies

The Inventory Engine consumes configurable values from the Reference Data Engine.

Examples include:

## Item Management

- Item Types
- Categories
- Brands
- Manufacturers

---

## Warehouse

- Warehouse Types
- Bin Types
- Zone Types
- Storage Types

---

## Inventory

- Transaction Types
- Reservation Statuses
- Allocation Statuses
- Adjustment Reasons
- Count Types
- Return Reasons
- Dispatch Statuses
- Receipt Statuses

---

## Planning

- Reorder Policies
- Valuation Methods
- Allocation Methods

No lookup values should be hardcoded.

---

# 90. Integration Philosophy

The Inventory Engine follows strict ownership boundaries.

```text
CRM

↓

Sales

↓

Inventory

↓

Finance

↓

Reporting
```

and

```text
Procurement

↓

Inventory

↓

Finance
```

Every Business Module owns its own domain.

Inventory exposes reusable inventory capabilities without duplicating responsibilities belonging to CRM, Sales, Procurement, Finance, Manufacturing, or future Business Modules.

All integrations occur through:

- Platform Event Bus
- Secure APIs
- Integration References
- Platform Engine Contracts

This architecture keeps the Inventory Engine modular, reusable, scalable, and aligned with the overall Business Suite enterprise architecture.

---

# 10. Permission Model

The Inventory Engine follows the Business Suite permission model.

Every action within the Inventory Engine requires an explicitly assigned permission.

Permissions are evaluated by the Authorization Engine before execution.

Permissions are grouped by functional area.

---

# 11. Item Master Permissions

## View

```text
inventory.item.view
```

Allows users to:

- View Item Master
- Search Items
- View Product Details
- View Item Availability

---

## Create

```text
inventory.item.create
```

Allows users to:

- Create Items
- Create Services
- Create Digital Products
- Create Subscription Products
- Create Rental Items

---

## Update

```text
inventory.item.update
```

Allows:

- Edit Item Information
- Update Product Details
- Modify Planning Information

---

## Delete

```text
inventory.item.delete
```

Allows logical deletion only.

Physical deletion is prohibited.

---

## Activate

```text
inventory.item.activate
```

---

## Deactivate

```text
inventory.item.deactivate
```

---

## Archive

```text
inventory.item.archive
```

---

## Import

```text
inventory.item.import
```

---

## Export

```text
inventory.item.export
```

---

# 12. Warehouse Permissions

```text
inventory.warehouse.view

inventory.warehouse.create

inventory.warehouse.update

inventory.warehouse.delete

inventory.warehouse.activate

inventory.warehouse.deactivate
```

---

## Warehouse Structure

```text
inventory.location.manage

inventory.zone.manage

inventory.aisle.manage

inventory.shelf.manage

inventory.bin.manage
```

---

# 13. Inventory Balance Permissions

```text
inventory.balance.view

inventory.availability.view

inventory.stock.lookup
```

Users with these permissions may:

- View Stock
- View Availability
- View Inventory Position

They cannot modify inventory.

---

# 14. Inventory Transaction Permissions

```text
inventory.transaction.view

inventory.transaction.create

inventory.transaction.reverse

inventory.transaction.cancel

inventory.transaction.export
```

Inventory Transactions are immutable.

Corrections require reversing transactions.

---

# 15. Goods Receipt Permissions

```text
inventory.goodsreceipt.view

inventory.goodsreceipt.create

inventory.goodsreceipt.update

inventory.goodsreceipt.submit

inventory.goodsreceipt.approve

inventory.goodsreceipt.cancel
```

---

# 16. Picking Permissions

```text
inventory.picklist.view

inventory.picklist.create

inventory.picklist.assign

inventory.picklist.pick

inventory.picklist.complete
```

---

# 17. Packing Permissions

```text
inventory.packing.view

inventory.packing.create

inventory.packing.complete
```

---

# 18. Dispatch Permissions

```text
inventory.dispatch.view

inventory.dispatch.create

inventory.dispatch.approve

inventory.dispatch.complete

inventory.dispatch.cancel
```

---

# 19. Reservation Permissions

```text
inventory.reservation.view

inventory.reservation.create

inventory.reservation.release

inventory.reservation.cancel
```

---

# 20. Allocation Permissions

```text
inventory.allocation.view

inventory.allocation.create

inventory.allocation.release
```

---

# 21. Transfer Permissions

```text
inventory.transfer.view

inventory.transfer.create

inventory.transfer.submit

inventory.transfer.approve

inventory.transfer.receive

inventory.transfer.cancel
```

---

# 22. Inventory Count Permissions

```text
inventory.count.view

inventory.count.create

inventory.count.assign

inventory.count.perform

inventory.count.review

inventory.count.approve

inventory.count.complete
```

---

# 23. Stock Adjustment Permissions

```text
inventory.adjustment.view

inventory.adjustment.create

inventory.adjustment.submit

inventory.adjustment.approve

inventory.adjustment.cancel
```

---

# 24. Returns Permissions

```text
inventory.return.view

inventory.return.create

inventory.return.approve

inventory.return.complete
```

---

# 25. Batch Permissions

```text
inventory.batch.view

inventory.batch.create

inventory.batch.update

inventory.batch.close
```

---

# 26. Lot Permissions

```text
inventory.lot.view

inventory.lot.create

inventory.lot.update

inventory.lot.close
```

---

# 27. Serial Number Permissions

```text
inventory.serial.view

inventory.serial.create

inventory.serial.update

inventory.serial.transfer

inventory.serial.dispose
```

---

# 28. Recall Permissions

```text
inventory.recall.view

inventory.recall.create

inventory.recall.approve

inventory.recall.complete
```

---

# 29. Rental Inventory Permissions

```text
inventory.rental.view

inventory.rental.checkout

inventory.rental.checkin

inventory.rental.damage

inventory.rental.maintenance
```

---

# 30. Digital Product Permissions

```text
inventory.digital.view

inventory.digital.activate

inventory.digital.revoke

inventory.digital.download
```

---

# 31. Subscription Product Permissions

```text
inventory.subscription.view

inventory.subscription.activate

inventory.subscription.suspend

inventory.subscription.cancel
```

---

# 32. Reporting Permissions

```text
inventory.report.view

inventory.report.export

inventory.dashboard.view
```

Reporting is generated by the Reporting Engine.

Inventory controls access to inventory data only.

---

# 33. Administrative Permissions

```text
inventory.settings.manage

inventory.configuration.manage

inventory.integration.manage
```

These permissions should normally be granted only to Inventory Administrators or System Administrators.

---

# 34. Permission Groups

The Inventory Engine permissions may be grouped into standard business roles.

Examples include:

### Inventory Administrator

- Full Inventory Access

---

### Warehouse Manager

- Warehouse Management
- Transfers
- Counts
- Adjustments
- Dispatch

---

### Warehouse Clerk

- Receiving
- Picking
- Packing
- Dispatch
- Inventory Lookup

---

### Inventory Controller

- Counts
- Adjustments
- Reservations
- Allocations
- Inventory Analysis

---

### Procurement Officer

- Goods Receipts
- Supplier Returns
- Stock Lookup

---

### Sales Officer

- Stock Availability
- Reservations
- Allocations

No warehouse maintenance.

---

### Finance Officer

- Inventory Valuation Reports
- Stock Value Reports

No warehouse execution permissions.

---

### Auditor

- Read-only access
- Reports
- Inventory History
- Audit Logs

No modification permissions.

---

# 35. Permission Summary

The Inventory Engine permission model provides fine-grained access control across every inventory capability.

Combined with the Authorization Engine, Workflow Engine, and Platform Core, it ensures that users receive only the permissions required for their responsibilities while maintaining strong segregation of duties and protecting inventory from unauthorized access or modification.

---

# 10. Permission Model

The Inventory Engine follows the Business Suite permission model.

Every action within the Inventory Engine requires an explicitly assigned permission.

Permissions are evaluated by the Authorization Engine before execution.

Permissions are grouped by functional area.

---

# 11. Item Master Permissions

## View

```text
inventory.item.view
```

Allows users to:

- View Item Master
- Search Items
- View Product Details
- View Item Availability

---

## Create

```text
inventory.item.create
```

Allows users to:

- Create Items
- Create Services
- Create Digital Products
- Create Subscription Products
- Create Rental Items

---

## Update

```text
inventory.item.update
```

Allows:

- Edit Item Information
- Update Product Details
- Modify Planning Information

---

## Delete

```text
inventory.item.delete
```

Allows logical deletion only.

Physical deletion is prohibited.

---

## Activate

```text
inventory.item.activate
```

---

## Deactivate

```text
inventory.item.deactivate
```

---

## Archive

```text
inventory.item.archive
```

---

## Import

```text
inventory.item.import
```

---

## Export

```text
inventory.item.export
```

---

# 12. Warehouse Permissions

```text
inventory.warehouse.view

inventory.warehouse.create

inventory.warehouse.update

inventory.warehouse.delete

inventory.warehouse.activate

inventory.warehouse.deactivate
```

---

## Warehouse Structure

```text
inventory.location.manage

inventory.zone.manage

inventory.aisle.manage

inventory.shelf.manage

inventory.bin.manage
```

---

# 13. Inventory Balance Permissions

```text
inventory.balance.view

inventory.availability.view

inventory.stock.lookup
```

Users with these permissions may:

- View Stock
- View Availability
- View Inventory Position

They cannot modify inventory.

---

# 14. Inventory Transaction Permissions

```text
inventory.transaction.view

inventory.transaction.create

inventory.transaction.reverse

inventory.transaction.cancel

inventory.transaction.export
```

Inventory Transactions are immutable.

Corrections require reversing transactions.

---

# 15. Goods Receipt Permissions

```text
inventory.goodsreceipt.view

inventory.goodsreceipt.create

inventory.goodsreceipt.update

inventory.goodsreceipt.submit

inventory.goodsreceipt.approve

inventory.goodsreceipt.cancel
```

---

# 16. Picking Permissions

```text
inventory.picklist.view

inventory.picklist.create

inventory.picklist.assign

inventory.picklist.pick

inventory.picklist.complete
```

---

# 17. Packing Permissions

```text
inventory.packing.view

inventory.packing.create

inventory.packing.complete
```

---

# 18. Dispatch Permissions

```text
inventory.dispatch.view

inventory.dispatch.create

inventory.dispatch.approve

inventory.dispatch.complete

inventory.dispatch.cancel
```

---

# 19. Reservation Permissions

```text
inventory.reservation.view

inventory.reservation.create

inventory.reservation.release

inventory.reservation.cancel
```

---

# 20. Allocation Permissions

```text
inventory.allocation.view

inventory.allocation.create

inventory.allocation.release
```

---

# 21. Transfer Permissions

```text
inventory.transfer.view

inventory.transfer.create

inventory.transfer.submit

inventory.transfer.approve

inventory.transfer.receive

inventory.transfer.cancel
```

---

# 22. Inventory Count Permissions

```text
inventory.count.view

inventory.count.create

inventory.count.assign

inventory.count.perform

inventory.count.review

inventory.count.approve

inventory.count.complete
```

---

# 23. Stock Adjustment Permissions

```text
inventory.adjustment.view

inventory.adjustment.create

inventory.adjustment.submit

inventory.adjustment.approve

inventory.adjustment.cancel
```

---

# 24. Returns Permissions

```text
inventory.return.view

inventory.return.create

inventory.return.approve

inventory.return.complete
```

---

# 25. Batch Permissions

```text
inventory.batch.view

inventory.batch.create

inventory.batch.update

inventory.batch.close
```

---

# 26. Lot Permissions

```text
inventory.lot.view

inventory.lot.create

inventory.lot.update

inventory.lot.close
```

---

# 27. Serial Number Permissions

```text
inventory.serial.view

inventory.serial.create

inventory.serial.update

inventory.serial.transfer

inventory.serial.dispose
```

---

# 28. Recall Permissions

```text
inventory.recall.view

inventory.recall.create

inventory.recall.approve

inventory.recall.complete
```

---

# 29. Rental Inventory Permissions

```text
inventory.rental.view

inventory.rental.checkout

inventory.rental.checkin

inventory.rental.damage

inventory.rental.maintenance
```

---

# 30. Digital Product Permissions

```text
inventory.digital.view

inventory.digital.activate

inventory.digital.revoke

inventory.digital.download
```

---

# 31. Subscription Product Permissions

```text
inventory.subscription.view

inventory.subscription.activate

inventory.subscription.suspend

inventory.subscription.cancel
```

---

# 32. Reporting Permissions

```text
inventory.report.view

inventory.report.export

inventory.dashboard.view
```

Reporting is generated by the Reporting Engine.

Inventory controls access to inventory data only.

---

# 33. Administrative Permissions

```text
inventory.settings.manage

inventory.configuration.manage

inventory.integration.manage
```

These permissions should normally be granted only to Inventory Administrators or System Administrators.

---

# 34. Permission Groups

The Inventory Engine permissions may be grouped into standard business roles.

Examples include:

### Inventory Administrator

- Full Inventory Access

---

### Warehouse Manager

- Warehouse Management
- Transfers
- Counts
- Adjustments
- Dispatch

---

### Warehouse Clerk

- Receiving
- Picking
- Packing
- Dispatch
- Inventory Lookup

---

### Inventory Controller

- Counts
- Adjustments
- Reservations
- Allocations
- Inventory Analysis

---

### Procurement Officer

- Goods Receipts
- Supplier Returns
- Stock Lookup

---

### Sales Officer

- Stock Availability
- Reservations
- Allocations

No warehouse maintenance.

---

### Finance Officer

- Inventory Valuation Reports
- Stock Value Reports

No warehouse execution permissions.

---

### Auditor

- Read-only access
- Reports
- Inventory History
- Audit Logs

No modification permissions.

---

# 35. Permission Summary

The Inventory Engine permission model provides fine-grained access control across every inventory capability.

Combined with the Authorization Engine, Workflow Engine, and Platform Core, it ensures that users receive only the permissions required for their responsibilities while maintaining strong segregation of duties and protecting inventory from unauthorized access or modification.

---

# 48. API Security

The Inventory Engine exposes its functionality through secure APIs.

All API endpoints must comply with the Business Suite API Security Standard.

The Inventory Engine does **not** implement independent API authentication.

Authentication is delegated to the Authorization Engine.

---

## API Security Objectives

API security should ensure:

- Authenticated Requests
- Authorized Operations
- Tenant Isolation
- Company Isolation
- Branch Isolation
- Warehouse Restrictions
- Audit Logging
- Rate Limiting
- Secure Event Publishing

---

## API Authentication

Supported authentication methods include:

- JWT Access Tokens
- OAuth 2.0
- OpenID Connect
- Service Accounts
- API Keys (System Integrations)
- Refresh Tokens

Authentication is validated before any Inventory Engine operation.

---

## API Authorization

Every endpoint requires one or more permissions.

Example:

```text
GET /inventory/items

↓

inventory.item.view
```

```text
POST /inventory/transfers

↓

inventory.transfer.create
```

```text
POST /inventory/adjustments/{id}/approve

↓

inventory.adjustment.approve
```

---

## API Tenant Validation

Every request must validate:

```text
Authenticated User

↓

Tenant

↓

Company

↓

Branch

↓

Warehouse Access

↓

Requested Resource
```

If validation fails:

```text
403 Forbidden
```

should be returned.

---

## API Rate Limiting

Rate limiting protects Inventory services against abuse.

Examples:

- User Requests
- API Keys
- Service Accounts
- Mobile Applications
- POS Devices

Limits should be configurable.

---

## API Idempotency

Inventory operations that modify stock should support idempotency.

Examples:

- Goods Receipt
- Inventory Adjustment
- Stock Transfer
- Dispatch
- Customer Return

Duplicate requests should never create duplicate inventory movements.

---

## API Validation

Every API request should validate:

- Required Fields
- Item Exists
- Warehouse Exists
- Quantity Rules
- Reservation Rules
- Allocation Rules
- Workflow Requirements

Business rule validation occurs before Inventory Transactions are created.

---

# 49. Event Security

The Inventory Engine publishes business events through the Platform Event Bus.

Events should never bypass security policies.

---

## Published Events

Examples:

```text
ItemCreated

StockReserved

StockAllocated

InventoryAdjusted

InventoryTransferred

InventoryDispatched

BatchExpired

RecallInitiated
```

---

## Event Authorization

Only authorized Inventory operations may publish events.

Example:

```text
Inventory Adjustment

↓

Approval

↓

Inventory Transaction

↓

Platform Event
```

Events must never be published for failed operations.

---

## Event Integrity

Published events should include:

- Event ID
- Timestamp
- Tenant ID
- Company ID
- Branch ID
- Correlation ID
- User ID
- Source Module
- Event Version

This ensures reliable event tracing across the platform.

---

# 50. Audit Security

Every inventory operation should be recorded by the Platform Activity & Audit Engine.

Audit records should include:

- User
- Date
- Time
- Device
- IP Address
- Warehouse
- Branch
- Operation
- Previous Value
- New Value
- Approval Information

Audit history must be immutable.

---

## Audited Operations

Examples include:

- Item Creation
- Item Update
- Warehouse Creation
- Reservation
- Allocation
- Goods Receipt
- Dispatch
- Transfer
- Adjustment
- Inventory Count
- Batch Creation
- Serial Assignment
- Product Recall

No critical inventory operation should occur without an audit record.

---

# 51. Security Notifications

The Notification Engine should notify authorized users of important security events.

Examples:

- Failed Login Attempts
- Unauthorized Warehouse Access
- High-Value Inventory Adjustment
- Large Inventory Write-Off
- Emergency Access Activation
- Product Recall Initiated
- Inventory Balance Correction
- Approval Escalation

Notification templates are managed by the Notification Engine.

---

# 52. Compliance

The Inventory Engine should support organizational and regulatory compliance requirements.

Examples include:

- ISO 9001
- ISO 27001
- FDA
- GMP
- GDP
- HACCP
- Pharmaceutical Regulations
- Food Safety Regulations
- Internal Audit Policies

Compliance support includes:

- Complete Traceability
- Immutable History
- Segregation of Duties
- Approval History
- Product Recall
- Batch Tracking
- Serial Tracking
- Inventory History

---

# 53. Data Protection

Sensitive inventory information should be protected.

Examples:

- Cost Prices
- Supplier Pricing
- Internal Valuation
- Warehouse Security Locations
- Controlled Inventory

Protection mechanisms include:

- Permission-Based Access
- Field-Level Security
- Database Encryption
- Secure APIs
- Audit Logging

---

# 54. Security Monitoring

The Inventory Engine should support continuous security monitoring.

Monitor events such as:

- Repeated Failed Access
- Unauthorized Warehouse Access
- Excessive Inventory Adjustments
- Suspicious Inventory Transfers
- Unusual Dispatch Volumes
- Repeated Inventory Corrections

Security events should be available to enterprise monitoring systems.

---

# 55. Disaster Recovery Security

During disaster recovery:

- Authentication remains mandatory.
- Authorization remains enforced.
- Audit logging continues where possible.
- Emergency access follows defined policies.
- Inventory integrity takes priority over availability.

Inventory balances must never be reconstructed from assumptions.

Recovery should always rely on:

- Inventory Transactions
- Verified Backups
- Audit History

---

# 56. Security Summary

The Inventory Engine security model is built upon the Business Suite Platform security architecture.

By leveraging the Platform Core, Authorization Engine, Workflow Engine, Platform Event Bus, Notification Engine, and Activity & Audit Engine, the Inventory Engine provides enterprise-grade protection for inventory assets while ensuring:

- Strong Authentication
- Fine-Grained Authorization
- Tenant Isolation
- Warehouse-Level Security
- Secure APIs
- Protected Business Events
- Comprehensive Audit Trails
- Regulatory Compliance
- Operational Integrity

This layered security model enables organizations of all sizes to manage inventory confidently while maintaining the highest standards of governance, accountability, and data protection.
