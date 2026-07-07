# Sales Documents Module - DATABASE.md

> Business Suite Enterprise Platform

---

# 1. Database Overview

The Sales Documents Module stores the business data required to manage the lifecycle of official commercial documents.

The module stores only business information and references.

Enterprise services such as document numbering, document storage, notifications, approvals, audit logs, reporting, and search indexing are owned by their respective Platform Engines.

---

# 2. Database Design Principles

The database is designed around the following principles:

- Multi-tenant
- Branch-aware
- Normalized
- Auditable
- Event-driven
- Engine-integrated
- Soft-delete capable
- Referential integrity
- Immutable issued documents
- High reporting performance

---

# 3. Data Ownership

The Sales Documents Module owns:

- Sales Quotations
- Sales Orders
- Delivery Notes
- Sales Invoices
- Customer Statements
- Document Line Items
- Document Relationships
- Document Lifecycle
- Customer Pricing Snapshots
- Tax Snapshots
- Discount Snapshots

The module stores references to external engines rather than duplicating their data.

---

# 4. External Engine References

The following information is referenced from Platform Engines.

## Platform Core

References:

- Tenant
- Organization
- Company
- Branch
- Department
- Business Unit
- User

---

## CRM Module

References:

- Customer
- Account
- Contact
- Opportunity
- Lead

---

## Finance Module

References:

- Customer Financial Account
- Receivable
- Payment
- Receipt
- Credit Note
- Debit Note
- Ledger Entry

---

## Document Numbering Engine

References:

- Document Number
- Numbering Sequence
- Serial Number

---

## Workflow Engine

References:

- Workflow
- Workflow Instance
- Approval Stage
- Approval Decision

---

## Document Management Engine

References:

- Document File
- PDF Version
- Preview File
- Storage Location

---

## Notification Engine

References:

- Notification
- Delivery History
- Email Reference
- SMS Reference

---

## Reference Data Engine

References:

- Document Status
- Payment Terms
- Currency
- Tax Type
- Discount Type
- Delivery Method
- Sales Channel
- Invoice Type
- Receipt Type

---

## Activity & Audit Engine

References:

- Audit Event
- Activity History
- Verification Log

---

# 5. Core Entities

The module consists of the following primary entities:

- SalesQuotation
- SalesQuotationItem
- SalesOrder
- SalesOrderItem
- DeliveryNote
- DeliveryNoteItem
- SalesInvoice
- SalesInvoiceItem
- CustomerStatement
- CustomerStatementItem
- DocumentRelationship
- DocumentLifecycle
- DocumentVerification
- DocumentVersion
- DocumentTerms
- DocumentCommunication
- DocumentPrintHistory

---

# 6. SalesQuotation

Represents an official customer quotation.

Contains:

- Tenant Reference
- Company Reference
- Branch Reference
- Customer Reference
- Opportunity Reference
- Currency
- Exchange Rate
- Payment Terms
- Valid Until
- Pricing Snapshot
- Tax Snapshot
- Discount Snapshot
- Workflow Reference
- Document Number Reference
- Current Status
- Document Totals
- Issue Information
- Approval Information
- Document Management Reference

---

# 7. SalesQuotationItem

Represents quotation line items.

Contains:

- Quotation Reference
- Item Reference
- Item Description Snapshot
- Unit of Measure
- Quantity
- Unit Price
- Discount
- Tax
- Line Total
- Display Sequence

---

# 8. SalesOrder

Represents confirmed customer orders.

Contains:

- Tenant Reference
- Branch Reference
- Customer Reference
- Source Quotation
- Currency
- Payment Terms
- Salesperson
- Approval Reference
- Workflow Reference
- Status
- Totals
- Issue Details
- Number Reference

---

# 9. SalesOrderItem

Contains:

- Sales Order Reference
- Item Reference
- Quantity Ordered
- Quantity Reserved
- Quantity Delivered
- Quantity Outstanding
- Unit Price
- Discount
- Tax
- Total

---

# 10. DeliveryNote

Represents goods or services delivered.

Contains:

- Tenant Reference
- Branch Reference
- Customer Reference
- Sales Order Reference
- Delivery Date
- Delivery Method
- Delivery Address Snapshot
- Delivery Status
- Approval Reference
- Number Reference
- Issue Information

---

# 11. DeliveryNoteItem

Contains:

- Delivery Note Reference
- Item Reference
- Quantity Ordered
- Quantity Delivered
- Quantity Remaining
- Warehouse Reference
- Store Reference

---

# 12. SalesInvoice

Represents billable customer invoices.

Contains:

- Tenant Reference
- Company Reference
- Branch Reference
- Customer Reference
- Sales Order Reference
- Delivery Note Reference
- Currency
- Exchange Rate
- Invoice Type
- Payment Terms
- Tax Summary
- Discount Summary
- Total Amount
- Outstanding Amount
- Workflow Reference
- Approval Reference
- Number Reference
- Finance Reference
- Document Management Reference
- Status

---

# 13. SalesInvoiceItem

Contains:

- Invoice Reference
- Item Reference
- Description Snapshot
- Quantity
- Unit Price
- Discount
- Tax
- Line Total

---

# 14. CustomerStatement

Represents generated customer account statements.

Contains:

- Tenant Reference
- Customer Reference
- Statement Date
- Statement Period
- Currency
- Opening Balance
- Closing Balance
- Outstanding Balance
- Generated By
- Generated Date
- PDF Reference

---

# 15. CustomerStatementItem

Contains:

- Statement Reference
- Transaction Type
- Transaction Date
- Document Reference
- Debit
- Credit
- Running Balance

---

# 16. DocumentRelationship

Stores relationships between documents.

Supported relationships include:

Quotation → Sales Order

Sales Order → Delivery Note

Delivery Note → Invoice

Invoice → Receipt

Invoice → Credit Note

Invoice → Debit Note

Invoice → Customer Statement

This allows complete document traceability.

---

# 17. DocumentLifecycle

Tracks lifecycle changes.

Stores:

- Previous Status
- Current Status
- Changed By
- Change Date
- Change Reason
- Workflow Stage
- Approval Decision

Lifecycle history is immutable.

---

# 18. DocumentVerification

Stores verification metadata.

Contains:

- Verification Code
- QR Reference
- Verification URL
- Verification Status
- Verification Timestamp
- Verification Count
- Last Verification Date
- Last Verification IP
- Last Verification Device

Verification history itself remains in the Activity & Audit Engine.

---

# 19. DocumentVersion

Tracks document revisions.

Contains:

- Version Number
- Parent Document
- Previous Version
- Revision Reason
- Effective Date
- Current Version Flag
- PDF Reference

Issued versions are immutable.

---

# 20. DocumentTerms

Stores document-specific terms and conditions.

Contains:

- Document Reference
- Terms Template Reference
- Rendered Terms Snapshot
- Language
- Effective Date

A snapshot is stored to preserve historical accuracy even if templates change.

---

# 21. DocumentCommunication

Tracks outbound document communication.

Contains:

- Document Reference
- Notification Reference
- Communication Type
- Recipient
- Delivery Status
- Sent Date
- Sent By

Message delivery details remain in the Notification Engine.

---

# 22. DocumentPrintHistory

Tracks printing events.

Contains:

- Document Reference
- Printed By
- Printed Date
- Print Number
- Print Reason

Detailed audit events remain in the Activity & Audit Engine.

---

# 23. Shared Header Structure

Every official document shares a common header model.

Includes:

- Tenant
- Company
- Branch
- Customer
- Currency
- Exchange Rate
- Document Number Reference
- Status
- Workflow Reference
- Approval Reference
- Issue Information
- Audit Reference
- Version Reference
- Document Reference

---

# 24. Shared Line Structure

All line-based documents share a common item model.

Includes:

- Item Reference
- Description Snapshot
- Quantity
- Unit of Measure
- Unit Price
- Discount
- Tax
- Line Total
- Display Order

---

# 25. Customer Snapshot Strategy

Customer information is stored as a historical snapshot.

Snapshot fields include:

- Customer Name
- Billing Address
- Shipping Address
- Contact Name
- Contact Email
- Contact Phone
- Tax Registration Number

This preserves historical accuracy even if the CRM customer record changes later.

---

# 26. Pricing Snapshot Strategy

Pricing information is stored as a snapshot.

Includes:

- Unit Price
- Currency
- Discount
- Tax
- Exchange Rate
- Pricing Date

Historical prices are never recalculated after issuance.

---

# 27. Tax Snapshot Strategy

Tax information is stored independently.

Includes:

- Tax Type
- Tax Rate
- Tax Amount
- Tax Authority Reference
- Inclusive/Exclusive Indicator

Future tax rule changes must not affect historical documents.

---

# 28. Discount Snapshot Strategy

Stores:

- Discount Type
- Discount Percentage
- Discount Amount
- Approval Reference
- Approval Level

Historical discounts remain unchanged.

---

# 29. Document Number References

Sales Documents never generate numbers.

Each document stores references to:

- Official Number
- Internal Serial Number
- Numbering Sequence
- Fiscal Period
- Number Allocation Record

The Document Numbering Engine owns sequence generation.

---

# 30. Workflow References

Each document stores:

- Workflow Definition
- Workflow Instance
- Current Approval Stage
- Final Approval Date
- Approval Status

Approval history remains in the Workflow Engine.

---

# 31. Document Management References

Each document stores references to:

- Official PDF
- Preview PDF
- File Identifier
- Storage Identifier
- Version Identifier

Binary files are never stored in Sales Documents.

---

# 32. Notification References

Stores references to:

- Email
- SMS
- Push Notification
- In-App Notification

Delivery metadata remains in the Notification Engine.

---

# 33. Search References

Documents expose searchable metadata.

Searchable fields include:

- Document Number
- Customer Number
- Customer Name
- Opportunity Number
- Invoice Number
- Receipt Number
- Amount
- Currency
- Date
- Status
- Salesperson

The Search & Indexing Engine maintains indexes.

---

# 34. Reporting References

Reporting dimensions include:

- Tenant
- Branch
- Customer
- Salesperson
- Currency
- Status
- Payment Terms
- Tax Type
- Sales Channel
- Document Type
- Approval Status

The Reporting Engine consumes these datasets.

---

# 35. Audit References

Every document stores references to:

- Creation Audit
- Approval Audit
- Issue Audit
- Verification Audit
- Cancellation Audit
- Void Audit
- Reversal Audit

Detailed logs remain in the Activity & Audit Engine.

---

# 36. Tenant Isolation

Every record is scoped by:

- Tenant
- Company
- Branch (where applicable)

All foreign references must belong to the same tenant.

Cross-tenant relationships are prohibited.

---

# 37. Branch Isolation

Branch-aware documents include:

- Quotation
- Sales Order
- Delivery Note
- Invoice
- Customer Statement

Branch settings may influence:

- Numbering
- Branding
- Warehouses
- Taxes
- Reporting
- User access

---

# 38. Referential Integrity Rules

The following relationships are mandatory:

- Quotation Items require a Quotation.
- Sales Order Items require a Sales Order.
- Delivery Note Items require a Delivery Note.
- Invoice Items require an Invoice.
- Statement Items require a Statement.
- Document Relationships require valid parent and child documents.

Deletion of issued parent documents is prohibited.

---

# 39. Immutability Rules

Once a document reaches the **Issued** status:

- Business values cannot be edited.
- Financial values cannot be changed.
- Item quantities cannot be altered.
- Taxes cannot be recalculated.
- Discounts cannot be modified.
- Customer snapshots cannot be replaced.
- Document numbers cannot be reused.
- Historical versions remain preserved.

Corrections must be performed using controlled business documents such as Credit Notes, Debit Notes, document reversals, or superseding versions, depending on business rules.

---

# 40. Archiving Strategy

Closed documents remain available for:

- Search
- Reporting
- Verification
- Auditing
- Customer statements
- Compliance reviews

Retention policies are enforced by the Document Management Engine.

---

# 41. Database Scalability

The data model supports:

- Millions of documents
- Multi-company organizations
- Multi-branch operations
- High-volume transactions
- Historical versioning
- Event-driven processing
- Distributed search
- Large reporting workloads

The design avoids duplication while maintaining high read performance through engine integrations.

---

# 42. Database Summary

The Sales Documents database is intentionally lightweight and business-focused.

It owns transactional document data while delegating shared enterprise responsibilities to the Platform Engines.

This separation ensures:

- Clear ownership boundaries
- Reduced duplication
- Strong tenant isolation
- Consistent document security
- High scalability
- Easier maintenance
- Enterprise-grade extensibility

---

# 43. Next Document

The next specification document is:

```text
SECURITY.md
```

This document will define:

- Business Document Security Model
- QR Code Security
- Verification Architecture
- Permission Matrix
- Document Integrity
- Access Control
- Fraud Prevention
- Digital Hash Strategy
- Watermark Standards
- Compliance and Audit Requirements

```


```
