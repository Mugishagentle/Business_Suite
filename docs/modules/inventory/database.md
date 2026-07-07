# Inventory Engine

## DATABASE.md

---

# 1. Database Overview

The **Inventory Engine** database is the authoritative repository for all inventory-related business data within the Business Suite Enterprise Platform.

It provides a centralized inventory domain responsible for managing:

- Item Master
- Product Master
- Service Master
- Digital Products
- Subscription Products
- Rental Items
- Warehouses
- Warehouse Locations
- Bins
- Inventory Balances
- Inventory Transactions
- Reservations
- Allocations
- Batch Tracking
- Lot Tracking
- Serial Numbers
- Inventory Transfers
- Inventory Counts
- Warehouse Operations

The Inventory Engine serves as the **Inventory Master** for the entire Business Suite.

All Business Modules consume inventory information from the Inventory Engine rather than maintaining independent inventory records.

---

# 2. Database Objectives

The Inventory database has been designed to:

- Maintain a single source of truth for inventory.
- Eliminate duplicate inventory records.
- Support physical and digital inventory.
- Support rental inventory.
- Support subscription products.
- Support enterprise warehouse operations.
- Enable complete inventory traceability.
- Support real-time inventory visibility.
- Support multi-tenant architecture.
- Support multi-branch operations.
- Support future manufacturing.
- Support enterprise scalability.
- Support AI-ready inventory analytics.

---

# 3. Database Design Principles

The Inventory Engine follows the Business Suite database standards.

## Inventory-Centric

Inventory is the central business entity.

Every inventory operation references an Item.

---

## Single Source of Truth

Only the Inventory Engine owns inventory data.

Other Business Modules reference inventory rather than duplicating it.

---

## Aggregate-Based Design

The database is organized around business aggregates.

Primary aggregates include:

- Item
- Warehouse
- Inventory Transaction
- Reservation
- Transfer
- Inventory Count

---

## Platform Engine Reuse

The Inventory Engine consumes Platform Engine services instead of duplicating functionality.

Examples include:

- Document Numbering
- Document Management
- Authorization
- Workflows
- Notifications
- Reporting
- Search
- Reference Data
- Audit Logging

---

## Modular Ownership

Every business capability has one owner.

Examples:

| Capability      | Owner            |
| --------------- | ---------------- |
| Items           | Inventory Engine |
| Warehouses      | Inventory Engine |
| Customers       | CRM              |
| Suppliers       | Procurement      |
| Sales Orders    | Sales            |
| Purchase Orders | Procurement      |
| Accounting      | Finance Engine   |

---

## Event-Driven Integration

Inventory publishes business events.

Other modules consume those events.

Inventory also consumes business events published by:

- Sales
- Procurement
- Manufacturing
- POS
- Finance

---

## Multi-Tenant Isolation

Every inventory record belongs to a single tenant.

Isolation is enforced through:

- Platform Core
- PostgreSQL Row Level Security
- Authorization Engine

---

## Extensibility

The schema must support future expansion without structural redesign.

---

# 4. Module Ownership

The following ownership boundaries apply.

| Business Capability       | Owner                      |
| ------------------------- | -------------------------- |
| Item Master               | Inventory Engine           |
| Product Master            | Inventory Engine           |
| Service Master            | Inventory Engine           |
| Digital Products          | Inventory Engine           |
| Subscription Products     | Inventory Engine           |
| Rental Items              | Inventory Engine           |
| Warehouses                | Inventory Engine           |
| Warehouse Locations       | Inventory Engine           |
| Bins                      | Inventory Engine           |
| Inventory Transactions    | Inventory Engine           |
| Reservations              | Inventory Engine           |
| Allocations               | Inventory Engine           |
| Inventory Counts          | Inventory Engine           |
| Stock Transfers           | Inventory Engine           |
| Inventory Valuation Rules | Finance Engine             |
| Accounting Entries        | Finance Engine             |
| Sales Orders              | Sales Module               |
| Purchase Orders           | Procurement Module         |
| Customer Master           | CRM Module                 |
| Supplier Master           | Procurement Module         |
| Document Numbers          | Document Numbering Engine  |
| Attachments               | Document Management Engine |
| Notifications             | Notification Engine        |
| Audit History             | Activity & Audit Engine    |

Inventory owns inventory.

Nothing more.

---

# 5. Aggregate Roots

The Inventory Engine is organized around the following aggregate roots.

## Item

Represents anything that can be:

- Purchased
- Sold
- Stored
- Consumed
- Reserved
- Rented
- Licensed
- Subscribed

---

## Warehouse

Represents a physical or virtual inventory location.

---

## Inventory Transaction

Represents every inventory movement.

No stock balance should ever change without an Inventory Transaction.

---

## Reservation

Represents committed inventory.

---

## Transfer

Represents inventory movement between warehouses or locations.

---

## Inventory Count

Represents physical verification of inventory.

---

# 6. Core Database Concepts

The Inventory Engine is built around the following concepts.

## Item

The master inventory entity.

---

## Warehouse

Storage facility.

---

## Warehouse Location

Logical storage hierarchy.

---

## Bin

Lowest physical storage location.

---

## Batch

Group of produced or received inventory.

---

## Lot

Traceability grouping.

---

## Serial Number

Unique item identity.

---

## Inventory Balance

Current stock position.

---

## Inventory Transaction

Source of all stock movements.

---

## Reservation

Committed inventory.

---

## Allocation

Assigned inventory awaiting fulfilment.

---

## Inventory Count

Verification process.

---

## Transfer

Movement between warehouses.

---

## Fulfilment

Operational warehouse execution.

---

# 7. Database Architecture

The Inventory database follows a layered architecture.

```text
                 Inventory Engine Database

                          │

                  Aggregate Roots

                          │

 ┌──────────────┬──────────────┬──────────────┐
 │              │              │              │
 Item      Warehouse     Inventory      Fulfilment
 Master     Structure    Control        Operations
 │              │              │              │
 ├──────────────┼──────────────┼──────────────┤
 │              │              │              │
 Batches     Bins        Transactions    Picking
 Lots        Zones       Reservations    Packing
 Serials     Shelves     Allocations     Dispatch
```

The architecture separates master data from operational transactions while preserving traceability.

---

# 8. Database Schema Overview

The Inventory Engine database is divided into four logical layers.

## Master Data

Stores inventory master records.

Examples:

- Items
- Warehouses
- Locations
- Bins

---

## Operational Data

Stores day-to-day inventory operations.

Examples:

- Inventory Transactions
- Reservations
- Allocations
- Transfers
- Inventory Counts

---

## Traceability Data

Supports complete inventory traceability.

Examples:

- Batches
- Lots
- Serials
- Expiry Records

---

## Integration Layer

Stores references to external modules.

Examples:

- Sales References
- Procurement References
- Finance References
- Project References
- Rental References
- Subscription References

Inventory stores references only.

Ownership remains with originating modules.

---

# 9. Platform Engine Dependencies

The Inventory database depends on the following Platform Engines.

| Platform Engine            | Database Responsibility             |
| -------------------------- | ----------------------------------- |
| Platform Core              | Tenants, Companies, Branches, Users |
| Authorization Engine       | Security Policies                   |
| Workflow Engine            | Workflow References                 |
| Document Numbering Engine  | Inventory Business Numbers          |
| Document Management Engine | Attachment References               |
| Notification Engine        | Notification References             |
| Reference Data Engine      | Lookup Values                       |
| Reporting Engine           | Reporting Views                     |
| Search & Indexing Engine   | Search Metadata                     |
| Activity & Audit Engine    | Audit References                    |
| Platform Event Bus         | Business Event Integration          |

Inventory stores references where appropriate.

---

# 10. Business Module Dependencies

The Inventory Engine integrates with multiple Business Modules.

| Business Module  | Database Integration              |
| ---------------- | --------------------------------- |
| CRM              | Customer Delivery References      |
| Sales            | Sales Order Fulfilment References |
| Procurement      | Purchase Receipt References       |
| Finance Engine   | Costing & Valuation References    |
| POS              | Retail Sales References           |
| Projects         | Material Allocation References    |
| Manufacturing    | Production References             |
| Customer Support | Warranty & Return References      |

Inventory never duplicates business data owned by these modules.

---

# 11. Database Summary

The Inventory Engine database establishes the Inventory Master domain for the Business Suite Enterprise Platform.

It maintains complete ownership of inventory, warehouse structures, inventory movements, reservations, fulfilment, and traceability while integrating seamlessly with Platform Engines and Business Modules.

The following sections define the individual database tables, relationships, business rules, integration points, and implementation requirements.

---

# 12. Core Tables

The Inventory Engine is built around a set of core business tables.

These tables represent the primary inventory domain and are fully owned by the Inventory Engine.

```text
inv_items
│
├── inv_item_variants
├── inv_item_images
├── inv_item_documents
├── inv_item_suppliers
├── inv_item_prices
├── inv_item_barcodes
├── inv_item_units
├── inv_item_categories
├── inv_item_brands
├── inv_item_attributes
└── inv_item_attribute_values
```

The Item Master serves as the inventory foundation for the entire Business Suite.

---

# 13. inv_items

## Purpose

The **inv_items** table is the master catalogue of every item managed within the Business Suite.

It supports multiple inventory business models from a single unified architecture.

Unlike traditional ERP systems that separate products, services, rentals, and subscriptions into different tables, Business Suite maintains a **single Item Master** with configurable item types.

---

## Supported Item Types

- Inventory Items
- Non-Inventory Items
- Services
- Digital Products
- Subscription Products
- Rental Items
- Raw Materials
- Finished Goods
- Semi-Finished Goods
- Consumables
- Spare Parts
- Kits
- Bundles
- Assemblies
- Asset Items

---

## Ownership

Owned By:

```text
Inventory Engine
```

Referenced By:

- Sales Module
- Procurement Module
- Finance Engine
- POS Module
- Projects Module
- Manufacturing Module
- Customer Support
- Reporting Engine

---

## Platform Engine Dependencies

| Platform Engine            | Purpose                            |
| -------------------------- | ---------------------------------- |
| Platform Core              | Tenant, Company, Branch references |
| Document Numbering Engine  | Item Number generation             |
| Document Management Engine | Product attachments                |
| Reference Data Engine      | Categories, Brands, Units          |
| Search & Indexing Engine   | Global Item Search                 |
| Activity & Audit Engine    | Item History                       |

---

## Relationships

```text
inv_items

│

├── inv_item_variants

├── inv_item_prices

├── inv_item_units

├── inv_item_suppliers

├── inv_item_images

├── inv_item_documents

├── inv_inventory_balances

├── inv_batches

├── inv_lots

├── inv_serial_numbers

└── inv_inventory_transactions
```

---

## Key Fields

### Identity

```text
id

tenant_id

company_id

item_number

sku

barcode

qr_code
```

---

### General Information

```text
item_name

short_description

description

item_type

status
```

---

### Classification

```text
category_id

subcategory_id

brand_id

manufacturer

model

collection
```

---

### Units

```text
base_unit

purchase_unit

sales_unit

inventory_unit
```

---

### Physical Characteristics

```text
weight

length

width

height

volume
```

---

### Inventory Control

```text
track_inventory

track_serials

track_batches

track_lots

track_expiry

allow_negative_stock
```

---

### Procurement

```text
preferred_supplier_id

lead_time

minimum_order_quantity
```

---

### Planning

```text
reorder_level

reorder_quantity

maximum_stock

minimum_stock

safety_stock
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

- Item Number is generated by the Document Numbering Engine.
- SKU must be unique within a tenant.
- One Item may have multiple Barcodes.
- One Item may have multiple Variants.
- One Item may have multiple Suppliers.
- One Item may exist in multiple Warehouses.
- Soft deletion is supported.

---

## Suggested Indexes

```text
tenant_id

item_number

sku

barcode

item_name

category_id

brand_id

status
```

---

## Events Published

```text
ItemCreated

ItemUpdated

ItemArchived

ItemActivated

ItemDiscontinued
```

---

## Events Consumed

```text
PurchaseReceiptCompleted

SalesOrderCreated

InventoryAdjusted
```

---

## Security Considerations

- Tenant isolation mandatory.
- Item visibility controlled through Authorization Engine.
- Sensitive costing fields permission-controlled.

---

## Future Expansion

Future enhancements include:

- AI Product Classification
- Product Recommendations
- Product Lifecycle Management
- Sustainability Metrics
- Carbon Footprint Tracking

---

# 14. inv_item_variants

## Purpose

Supports configurable product variants without duplicating the parent Item.

Examples include:

```text
Laptop

├── 8GB / 256GB

├── 16GB / 512GB

└── 32GB / 1TB
```

or

```text
T-Shirt

├── Small / Black

├── Medium / Black

├── Large / Blue
```

---

## Ownership

Owned By:

```text
Inventory Engine
```

---

## Relationships

```text
inv_items

│

└── inv_item_variants
```

---

## Key Fields

```text
id

tenant_id

item_id

variant_code

variant_name

sku

barcode

status
```

---

## Business Rules

- Each Variant belongs to one Item.
- Variants may override SKU and Barcode.
- Variants maintain independent inventory balances.

---

## Events Published

```text
VariantCreated

VariantUpdated
```

---

# 15. inv_item_units

## Purpose

Stores all units of measure associated with an Item.

Supports purchasing, sales, inventory, and warehouse operations.

---

## Relationships

```text
inv_items

│

└── inv_item_units
```

---

## Examples

```text
Base Unit

Piece

Purchase Unit

Box

Sales Unit

Piece

Conversion

1 Box = 24 Pieces
```

---

## Key Fields

```text
id

tenant_id

item_id

unit_id

conversion_factor

is_base_unit

is_purchase_unit

is_sales_unit

is_inventory_unit
```

---

## Business Rules

- Every Item must have one Base Unit.
- Conversion factors must be positive.
- Units are supplied by the Reference Data Engine.

---

# 16. inv_item_prices

## Purpose

Stores pricing information for Items.

Supports multiple pricing strategies.

---

## Price Types

Examples:

- Purchase Price
- Cost Price
- Standard Cost
- Selling Price
- Wholesale Price
- Retail Price
- Rental Price
- Subscription Price
- Promotional Price

---

## Key Fields

```text
id

tenant_id

item_id

price_type

currency_id

price

effective_from

effective_to
```

---

## Business Rules

- Multiple prices supported.
- Historical pricing retained.
- Finance owns accounting valuation.
- Sales consumes selling prices.

---

# 17. inv_item_suppliers

## Purpose

Associates Items with Suppliers.

Supports multiple suppliers per Item.

---

## Relationships

```text
inv_items

│

└── inv_item_suppliers
```

---

## Key Fields

```text
id

tenant_id

item_id

supplier_id

supplier_item_code

preferred_supplier

lead_time

minimum_order_quantity
```

---

## Business Rules

- Supplier Master remains owned by Procurement.
- One Item may have many Suppliers.
- One preferred Supplier may be designated.

---

# 18. inv_item_barcodes

## Purpose

Supports multiple barcodes per Item or Variant.

Examples:

- Manufacturer Barcode
- Internal Barcode
- Supplier Barcode
- GS1 Barcode

---

## Key Fields

```text
id

tenant_id

item_id

variant_id

barcode

barcode_type

is_primary
```

---

## Business Rules

- Multiple barcodes permitted.
- One primary barcode.
- Barcode uniqueness enforced within a tenant.

---

# 19. inv_item_images

## Purpose

Maintains references to Item images.

Images are stored in the Document Management Engine.

Inventory stores references only.

---

## Key Fields

```text
id

tenant_id

item_id

document_reference_id

display_order

is_primary
```

---

# 20. inv_item_documents

## Purpose

Maintains references to Item documents.

Examples:

- Product Manuals
- Safety Data Sheets
- Warranty Documents
- Technical Specifications
- Certificates
- Compliance Documents

---

## Ownership

Document Management Engine owns:

- File Storage
- Versioning
- Metadata

Inventory stores references only.

---

## Key Fields

```text
id

tenant_id

item_id

document_reference_id

document_type

status
```

---

# 21. Item Master Summary

The Item Master is the foundation of the Inventory Engine.

It provides a unified architecture capable of supporting physical inventory, services, rentals, digital products, subscriptions, manufacturing, and future business models from a single extensible data model while leveraging existing Platform Engines for numbering, document management, auditing, reporting, and security.

---

# 22. Warehouse Management Tables

The Warehouse Management domain is responsible for defining where inventory is stored and how it is organized.

The Inventory Engine supports enterprise warehouse structures ranging from a single store to global distribution networks.

```text
inv_warehouses
│
├── inv_warehouse_locations
├── inv_warehouse_zones
├── inv_warehouse_aisles
├── inv_warehouse_shelves
├── inv_bins
├── inv_bin_types
└── inv_storage_rules
```

Warehouse structures are reusable across all inventory operations.

---

# 23. inv_warehouses

## Purpose

The **inv_warehouses** table stores all physical and logical inventory storage facilities.

A Warehouse represents the highest-level inventory storage location.

Warehouses may include:

- Main Warehouse
- Retail Store
- Branch Store
- Distribution Centre
- Regional Warehouse
- Transit Warehouse
- Returns Warehouse
- Quarantine Warehouse
- Virtual Warehouse
- Rental Warehouse

---

## Ownership

Owned By:

```text
Inventory Engine
```

Referenced By:

- Sales
- Procurement
- POS
- Manufacturing
- Projects
- Customer Support
- Finance

---

## Platform Engine Dependencies

| Platform Engine           | Purpose                 |
| ------------------------- | ----------------------- |
| Platform Core             | Tenant, Company, Branch |
| Document Numbering Engine | Warehouse Number        |
| Reference Data Engine     | Warehouse Types         |
| Activity & Audit Engine   | Warehouse Audit         |

---

## Relationships

```text
inv_warehouses

│

├── inv_warehouse_locations

├── inv_inventory_balances

├── inv_inventory_transactions

├── inv_stock_transfers

├── inv_inventory_counts

└── inv_dispatches
```

---

## Key Fields

### Identity

```text
id

tenant_id

company_id

branch_id

warehouse_number
```

---

### Warehouse Information

```text
warehouse_name

warehouse_code

warehouse_type

status
```

---

### Address

```text
address_id

contact_person

phone

email
```

---

### Operations

```text
allow_sales

allow_procurement

allow_transfers

allow_manufacturing

allow_rentals

allow_returns
```

---

### Audit

```text
created_by

updated_by

created_at

updated_at
```

---

## Business Rules

- Warehouse Number generated by Document Numbering Engine.
- Warehouse belongs to one Company.
- Warehouse belongs to one Branch.
- Warehouses may be active or inactive.
- Warehouse deletion is not permitted while inventory exists.

---

## Suggested Indexes

```text
tenant_id

warehouse_number

warehouse_code

branch_id

status
```

---

## Events Published

```text
WarehouseCreated

WarehouseUpdated

WarehouseActivated

WarehouseDeactivated
```

---

# 24. inv_warehouse_locations

## Purpose

Represents logical storage locations within a warehouse.

Locations allow inventory to be organized efficiently.

---

## Relationships

```text
Warehouse

↓

Location

↓

Zone

↓

Aisle

↓

Shelf

↓

Bin
```

---

## Key Fields

```text
id

tenant_id

warehouse_id

location_code

location_name

status
```

---

## Business Rules

- Every Location belongs to one Warehouse.
- Locations may contain multiple Zones.
- Locations support independent operational status.

---

# 25. inv_warehouse_zones

## Purpose

Divides warehouse locations into operational zones.

Examples:

- Receiving
- Picking
- Packing
- Dispatch
- Returns
- Quarantine
- Cold Storage
- High Value
- Rental Holding

---

## Key Fields

```text
id

tenant_id

warehouse_location_id

zone_code

zone_name

zone_type
```

---

# 26. inv_warehouse_aisles

## Purpose

Represents aisles inside warehouse zones.

Supports warehouse navigation and optimized picking.

---

## Key Fields

```text
id

tenant_id

zone_id

aisle_code

aisle_name
```

---

# 27. inv_warehouse_shelves

## Purpose

Represents shelves within warehouse aisles.

Supports structured inventory storage.

---

## Key Fields

```text
id

tenant_id

aisle_id

shelf_code

shelf_name

maximum_capacity
```

---

# 28. inv_bins

## Purpose

The Bin is the lowest physical storage location.

Inventory is ultimately stored inside a Bin.

---

## Relationships

```text
Warehouse

↓

Location

↓

Zone

↓

Aisle

↓

Shelf

↓

Bin
```

---

## Key Fields

```text
id

tenant_id

warehouse_id

location_id

zone_id

aisle_id

shelf_id

bin_code

bin_type

capacity

status
```

---

## Business Rules

- Bin belongs to one Shelf.
- Bin capacity should be configurable.
- Bin status controls inventory availability.

---

## Events Published

```text
BinCreated

BinUpdated

BinDisabled
```

---

# 29. inv_bin_types

## Purpose

Defines reusable bin classifications.

Examples include:

- Standard
- Bulk
- Cold Storage
- Hazardous
- Secure
- Oversized
- Returns
- Quarantine

Values are supplied through the Reference Data Engine.

---

# 30. inv_storage_rules

## Purpose

Defines inventory storage rules.

Examples:

- Refrigerated Items
- Hazardous Materials
- Heavy Items
- Flammable Products
- Controlled Medicines
- Rental Equipment
- High Value Inventory

Storage rules guide warehouse operations without hardcoding logic.

---

# 31. Warehouse Hierarchy Summary

The Inventory Engine supports a hierarchical warehouse model.

```text
Warehouse

↓

Location

↓

Zone

↓

Aisle

↓

Shelf

↓

Bin
```

This hierarchy enables:

- Efficient picking
- Warehouse optimization
- Bin-level inventory
- Capacity planning
- Route optimization
- Future warehouse automation

---

# 32. Warehouse Management Summary

The Warehouse Management domain provides the physical structure upon which all inventory operations are performed.

It supports:

- Unlimited Warehouses
- Multi-Branch Operations
- Multi-Company Readiness
- Structured Storage
- Warehouse Optimization
- Inventory Traceability
- Warehouse Automation Readiness

All warehouse entities integrate seamlessly with the Platform Core, Reference Data Engine, Document Numbering Engine, Activity & Audit Engine, and other Business Modules while preserving clear ownership boundaries.

---

# 33. Inventory Control Tables

The Inventory Control domain manages the operational stock position of the business.

It determines:

- What stock exists
- Where stock exists
- What stock is available
- What stock is reserved
- What stock is allocated
- What stock is in transit
- What stock is unavailable

Inventory Control tables include:

```text
inv_inventory_balances
inv_inventory_reservations
inv_inventory_allocations
inv_inventory_availability
inv_inventory_commitments
```

The Inventory Transaction table remains the immutable source of truth, while Inventory Control tables provide fast operational views for availability, fulfilment, POS, sales, warehouse operations, and reporting.

---

# 34. inv_inventory_balances

## Purpose

The **inv_inventory_balances** table stores the current stock position for each item, warehouse, location, bin, batch, lot, or serial number.

It acts as the operational stock snapshot.

It should be updated only through valid Inventory Transactions.

---

## Ownership

Owned By:

```text
Inventory Engine
```

---

## Relationships

```text
inv_items
    │
    └── inv_inventory_balances
            │
            ├── inv_warehouses
            ├── inv_bins
            ├── inv_batches
            ├── inv_lots
            └── inv_serial_numbers
```

---

## Key Fields

```text
id

tenant_id

company_id

branch_id

item_id

variant_id

warehouse_id

location_id

zone_id

aisle_id

shelf_id

bin_id

batch_id

lot_id

serial_number_id

quantity_on_hand

quantity_available

quantity_reserved

quantity_allocated

quantity_in_transit

quantity_quarantined

quantity_damaged

quantity_expired

quantity_returned

quantity_on_order

last_transaction_id

last_movement_at

created_at

updated_at
```

---

## Business Rules

- Stock balances are never manually edited.
- Stock balances are updated only through inventory transactions.
- Quantity available must be calculated from stock state.
- Negative stock is only allowed if item policy permits it.
- Balance records must be tenant scoped.
- Balance records should support warehouse-level and bin-level visibility.
- Serialized items should have quantity of one per serial record.

---

## Suggested Indexes

```text
tenant_id

item_id

variant_id

warehouse_id

bin_id

batch_id

lot_id

serial_number_id
```

---

## Events Consumed

```text
InventoryTransactionPosted

StockReserved

StockAllocated

StockReleased

StockTransferred

StockAdjusted
```

---

## Security Considerations

- Stock visibility follows warehouse and branch permissions.
- Cost-related stock visibility may require Finance permissions.
- Users should not bypass Inventory Engine APIs to update balances.

---

# 35. inv_inventory_reservations

## Purpose

The **inv_inventory_reservations** table stores inventory reserved for a specific business requirement.

Reservations prevent overselling and ensure stock is committed before fulfilment.

Reservations may originate from:

- Sales Orders
- POS Transactions
- Rental Bookings
- Project Requests
- Manufacturing Orders
- Internal Requests

---

## Ownership

Owned By:

```text
Inventory Engine
```

Source Documents Owned By:

```text
Sales, POS, Projects, Manufacturing, Rentals
```

---

## Key Fields

```text
id

tenant_id

reservation_number

source_module

source_record_id

source_record_number

item_id

variant_id

warehouse_id

requested_quantity

reserved_quantity

reservation_status

reservation_type

expires_at

reserved_by

created_at

updated_at

released_at
```

---

## Reservation Statuses

```text
Requested

Reserved

Partially Reserved

Released

Expired

Cancelled

Fulfilled
```

Status values should come from the Reference Data Engine.

---

## Business Rules

- Reservation Number is generated by the Document Numbering Engine.
- A reservation must reference a valid source module.
- Reserved quantity cannot exceed available quantity unless backorder is allowed.
- Expired reservations should release stock.
- Released reservations increase available stock.
- Fulfilled reservations move into allocation and dispatch.

---

## Events Published

```text
StockReserved

StockReservationFailed

StockReservationReleased

StockReservationExpired
```

---

## Events Consumed

```text
SalesOrderConfirmed

RentalReservationCreated

ProjectMaterialRequested

ManufacturingMaterialRequested
```

---

# 36. inv_inventory_allocations

## Purpose

The **inv_inventory_allocations** table stores stock that has been specifically assigned to fulfil a reservation or warehouse operation.

Allocation is more specific than reservation.

Reservation commits stock generally.

Allocation identifies the exact stock to be used.

Example:

```text
Sales Order reserves 10 phones.

Allocation selects:

- Warehouse A
- Bin B-02
- Batch LOT-2026-04
- Serial Numbers 001–010
```

---

## Ownership

Owned By:

```text
Inventory Engine
```

---

## Key Fields

```text
id

tenant_id

allocation_number

reservation_id

source_module

source_record_id

item_id

variant_id

warehouse_id

bin_id

batch_id

lot_id

serial_number_id

allocated_quantity

allocation_status

allocated_by

allocated_at

released_at
```

---

## Allocation Statuses

```text
Allocated

Partially Allocated

Picked

Packed

Dispatched

Released

Cancelled
```

---

## Business Rules

- Allocation Number is generated by the Document Numbering Engine.
- Allocations must reference available inventory.
- Allocation reduces available stock.
- Allocation increases allocated quantity.
- Serialized inventory allocation must identify exact serial numbers.
- Batch-tracked inventory allocation must identify batch or lot.
- FEFO/FIFO allocation rules may apply.

---

## Events Published

```text
StockAllocated

StockAllocationReleased

StockPicked

StockPacked

StockDispatched
```

---

# 37. inv_inventory_availability

## Purpose

The **inv_inventory_availability** structure provides a read-optimized view of stock availability.

It may be implemented as:

- Database view
- Materialized view
- Cached read model
- API-calculated response

Availability supports fast checks by Sales, POS, Rentals, Projects, and Procurement.

---

## Availability Formula

```text
Quantity Available

=

Quantity On Hand

-

Quantity Reserved

-

Quantity Allocated

-

Quantity Quarantined

-

Quantity Damaged

-

Quantity Expired
```

Incoming stock may be displayed separately as:

```text
Quantity On Order

Quantity In Transit

Expected Receipt Date
```

---

## Availability Dimensions

Availability should be calculated by:

- Tenant
- Company
- Branch
- Warehouse
- Location
- Bin
- Item
- Variant
- Batch
- Lot
- Serial Number
- Expiry Date

---

## Business Rules

- Availability must respect tenant isolation.
- Availability must respect warehouse permissions.
- Availability must exclude expired or quarantined stock.
- Availability must support FEFO for expiry-controlled items.
- Availability must support real-time POS checks.
- Availability must support customer order reservations.

---

# 38. inv_inventory_commitments

## Purpose

The **inv_inventory_commitments** table stores future inventory demand.

Commitments represent expected inventory consumption that may not yet be reserved.

Examples:

- Draft Sales Orders
- Forecast Demand
- Project Planned Materials
- Manufacturing Planned Consumption
- Rental Future Bookings
- Subscription Device Assignments

Commitments help with planning without immediately reducing available stock.

---

## Key Fields

```text
id

tenant_id

commitment_number

source_module

source_record_id

item_id

variant_id

warehouse_id

committed_quantity

required_date

commitment_status

created_at

updated_at
```

---

## Business Rules

- Commitments do not reduce available stock until converted into reservations.
- Commitments support planning and forecasting.
- Commitments may expire or be cancelled.
- Commitments may be converted into reservations.

---

## Events Published

```text
InventoryCommitmentCreated

InventoryCommitmentConverted

InventoryCommitmentCancelled
```

---

# 39. Inventory Control Summary

The Inventory Control domain provides the operational stock intelligence required by the Business Suite.

It supports:

- Real-time stock balances
- Availability checks
- Reservations
- Allocations
- Commitments
- Fulfilment readiness
- POS stock validation
- Sales order fulfilment
- Rental availability
- Project material planning
- Manufacturing material planning

## This domain ensures that Inventory remains accurate, auditable, scalable, and reliable across all Business Modules.

# 40. Inventory Transaction Tables

The Inventory Transaction domain is the immutable operational ledger of the Inventory Engine.

Every inventory movement must be recorded as an Inventory Transaction.

**Inventory Transactions are the source of truth.**

Inventory Balances, Availability, Reservations, and Allocations are operational views derived from these transactions.

No inventory quantity should ever change without an Inventory Transaction.

---

## Inventory Transaction Principles

Every transaction must be:

- Immutable
- Auditable
- Tenant Aware
- Company Aware
- Branch Aware
- Warehouse Aware
- User Attributable
- Time Stamped
- Traceable

Every transaction must reference its originating business document.

---

## Inventory Transaction Types

The Inventory Engine should support the following transaction types.

### Procurement

- Goods Receipt
- Purchase Return

---

### Sales

- Reservation
- Allocation
- Pick
- Pack
- Dispatch
- Customer Return

---

### Warehouse

- Warehouse Transfer
- Bin Transfer
- Stock Adjustment
- Inventory Count Adjustment
- Opening Balance
- Closing Balance

---

### Manufacturing

- Material Consumption
- Finished Goods Receipt
- Production Return
- Scrap

---

### Rental

- Rental Check-Out
- Rental Check-In
- Rental Damage
- Rental Replacement

---

### Digital Products

- License Allocation
- License Revocation
- Download Activation

---

### Subscription Products

- Subscription Activation
- Subscription Upgrade
- Subscription Downgrade
- Subscription Cancellation

---

# 41. inv_inventory_transactions

## Purpose

The **inv_inventory_transactions** table records every inventory movement.

This table forms the operational ledger of inventory.

Inventory balances should always be reproducible from Inventory Transactions.

---

## Ownership

Owned By:

```text
Inventory Engine
```

Referenced By:

- Finance Engine
- Sales Module
- Procurement Module
- Manufacturing Module
- POS Module
- Projects Module
- Customer Support

---

## Platform Engine Dependencies

| Platform Engine           | Purpose                 |
| ------------------------- | ----------------------- |
| Platform Core             | Tenant, Company, Branch |
| Document Numbering Engine | Transaction Number      |
| Workflow Engine           | Approval References     |
| Activity & Audit Engine   | Audit Trail             |
| Platform Event Bus        | Business Events         |

---

## Relationships

```text
inv_items

        │

        ├── inv_inventory_transactions

        │          │

        │          ├── inv_inventory_balances

        │          ├── inv_batches

        │          ├── inv_lots

        │          ├── inv_serial_numbers

        │          └── Finance References

        │

        └── Source Business Documents
```

---

## Key Fields

### Identity

```text
id

tenant_id

company_id

branch_id

transaction_number

transaction_type

transaction_date
```

---

### Source Information

```text
source_module

source_record_type

source_record_id

source_document_number
```

Examples:

```text
Sales Order

Purchase Order

Transfer

Adjustment

Rental

Project

Manufacturing
```

---

### Item Information

```text
item_id

variant_id

warehouse_id

location_id

zone_id

aisle_id

shelf_id

bin_id

batch_id

lot_id

serial_number_id
```

---

### Quantity

```text
quantity

unit_of_measure

movement_direction

inventory_effect
```

Movement examples:

```text
IN

OUT

TRANSFER

RESERVE

RELEASE

ALLOCATE
```

---

### Cost Information

```text
unit_cost

extended_cost

currency_id
```

Finance determines accounting impact.

Inventory records operational costs only.

---

### Workflow

```text
workflow_instance_id

approval_status
```

---

### Audit

```text
performed_by

approved_by

transaction_timestamp

created_at
```

---

## Business Rules

- Every inventory movement creates one or more Inventory Transactions.
- Transactions are immutable.
- Transactions cannot be edited.
- Corrections require reversing transactions.
- Transaction Number generated by Document Numbering Engine.
- Inventory balances update automatically after transaction posting.
- Every transaction must reference a valid source.

---

## Suggested Indexes

```text
tenant_id

transaction_number

transaction_type

item_id

warehouse_id

transaction_date

source_module

source_record_id
```

---

## Events Published

```text
InventoryTransactionPosted

InventoryTransactionReversed

InventoryMovementCompleted
```

---

## Security Considerations

- Transactions cannot be physically deleted.
- Reversals require approval.
- Tenant isolation mandatory.

---

# 42. Inventory Movement Flow

Every inventory movement follows the same architecture.

```text
Business Event

↓

Inventory Transaction

↓

Inventory Balances Updated

↓

Availability Updated

↓

Platform Event Published

↓

Finance Integration

↓

Audit Recorded
```

This guarantees consistency across every module.

---

# 43. Source Document References

Every Inventory Transaction must reference its originating document.

Examples:

| Module        | Source Document      |
| ------------- | -------------------- |
| Procurement   | Goods Receipt        |
| Procurement   | Purchase Return      |
| Sales         | Sales Order          |
| Sales         | Delivery             |
| Sales         | Customer Return      |
| Inventory     | Adjustment           |
| Inventory     | Transfer             |
| Inventory     | Stock Count          |
| Manufacturing | Production Order     |
| Manufacturing | Material Consumption |
| Rental        | Rental Agreement     |
| Projects      | Material Request     |
| POS           | POS Sale             |

Inventory never owns these business documents.

It only references them.

---

# 44. Transaction Processing Rules

Inventory Transactions should always be processed in the following order.

```text
Validate

↓

Workflow Approval (If Required)

↓

Inventory Transaction

↓

Inventory Balances

↓

Availability

↓

Reservations

↓

Allocations

↓

Platform Events

↓

Finance Integration

↓

Audit
```

This processing order must remain consistent throughout the Business Suite.

---

# 45. Financial Integration

Inventory Transactions provide operational data.

Finance performs accounting.

Example:

```text
Goods Receipt

↓

Inventory Transaction

↓

Inventory Event

↓

Finance Engine

↓

Inventory Asset

↓

Accounts Payable

↓

General Ledger
```

Sales Example:

```text
Dispatch

↓

Inventory Transaction

↓

Finance Engine

↓

Inventory Asset Credit

↓

Cost of Goods Sold Debit

↓

General Ledger
```

Inventory must never post journals.

---

# 46. Inventory Ledger Concept

The Inventory Transaction table represents the operational inventory ledger.

Characteristics:

- Complete History
- Immutable
- Chronological
- Fully Auditable
- Traceable
- Event Driven

Every inventory movement can be reconstructed from this ledger.

---

# 47. Inventory Transaction Summary

The Inventory Transaction domain is the operational backbone of the Inventory Engine.

It guarantees:

- Complete inventory history
- Full traceability
- Audit compliance
- Financial integration
- High-performance operational balances
- Reliable warehouse operations

By separating immutable transactions from operational balance tables, the Inventory Engine achieves both enterprise-grade auditability and high-performance day-to-day inventory operations.

---

# 48. Inventory Transaction Document Model

The Inventory Engine follows the Business Suite document architecture.

Inventory business documents consist of:

- Document Header
- Document Lines

This pattern is consistent with:

- Sales Quotations
- Sales Orders
- Purchase Orders
- Goods Receipt Notes
- Invoices
- Journal Entries

Using a Header/Lines architecture provides:

- Better scalability
- Improved reporting
- Consistent document processing
- Simpler integrations
- Better auditability

---

# 49. inv_inventory_transaction_lines

## Purpose

The **inv_inventory_transaction_lines** table stores the individual inventory movement lines belonging to an Inventory Transaction document.

The transaction header represents the inventory operation.

The transaction lines represent the individual items affected.

Example:

```text
Stock Transfer ST-000025

Header

↓

Lines

Laptop x 5

Monitor x 10

Keyboard x 20

Mouse x 20
```

---

## Ownership

Owned By:

```text
Inventory Engine
```

---

## Relationships

```text
inv_inventory_transactions

        │

        └── inv_inventory_transaction_lines

                │

                ├── inv_items

                ├── inv_batches

                ├── inv_lots

                ├── inv_serial_numbers

                ├── inv_bins

                └── inv_inventory_balances
```

---

## Key Fields

### Identity

```text
id

tenant_id

transaction_id

line_number
```

---

### Item Information

```text
item_id

variant_id

description
```

---

### Warehouse Structure

```text
warehouse_id

location_id

zone_id

aisle_id

shelf_id

bin_id
```

---

### Traceability

```text
batch_id

lot_id

serial_number_id

expiry_date
```

---

### Quantities

```text
quantity

unit_of_measure

base_quantity

conversion_factor
```

---

### Cost

```text
unit_cost

extended_cost

currency_id
```

---

### Audit

```text
created_at

updated_at
```

---

## Business Rules

- Every line belongs to one Inventory Transaction.
- One transaction may contain multiple lines.
- Each line references exactly one Item.
- Batch, Lot, and Serial references are optional depending on the Item configuration.
- Unit conversions must use the Item Master conversion definitions.
- Line totals contribute to inventory balances.

---

## Suggested Indexes

```text
tenant_id

transaction_id

item_id

warehouse_id

batch_id

lot_id

serial_number_id
```

---

## Events Published

Transaction line processing is part of the parent Inventory Transaction.

No independent business events are published from transaction lines.

---

# 50. Batch Management Tables

Batch Management enables groups of inventory to be tracked together throughout their lifecycle.

Batch tracking is commonly used for:

- Pharmaceuticals
- Food & Beverage
- Chemicals
- Agriculture
- Manufacturing
- Medical Supplies

Batch tracking supports:

- Manufacturing Date
- Expiry Date
- Supplier Batch
- Internal Batch
- Quantity Tracking
- Batch Status
- Batch Traceability

---

# 51. inv_batches

## Purpose

The **inv_batches** table stores inventory batches.

A Batch represents a quantity of inventory received or produced together.

---

## Ownership

Owned By:

```text
Inventory Engine
```

---

## Relationships

```text
inv_items

│

└── inv_batches

        │

        ├── inv_inventory_balances

        ├── inv_inventory_transaction_lines

        └── inv_lots
```

---

## Key Fields

```text
id

tenant_id

batch_number

supplier_batch_number

item_id

warehouse_id

manufacturing_date

expiry_date

received_date

batch_status

quantity_received

quantity_available

quantity_reserved

quantity_allocated
```

---

## Business Rules

- Batch Number generated by the Document Numbering Engine.
- Batch tracking is enabled only for configured Items.
- Multiple batches may exist for the same Item.
- Batch balances must remain synchronized with inventory balances.
- Expired batches must not be allocated unless explicitly permitted.

---

## Events Published

```text
BatchCreated

BatchReceived

BatchExpired

BatchClosed
```

---

# 52. Batch Allocation Rules

The Inventory Engine should support configurable allocation strategies.

Examples:

```text
FIFO

FEFO

LIFO (Optional)

Manual

Priority Batch
```

Default strategy should be configurable per tenant and per Item Category.

---

# 53. Batch Summary

Batch Management enables complete operational traceability for grouped inventory while supporting expiry management, quality control, regulatory compliance, and warehouse optimization.

Batch records integrate directly with Inventory Transactions, Inventory Balances, Reservations, Allocations, and Fulfilment processes.

---

# 54. Lot Management

Lot Management provides an additional level of traceability beyond inventory batches.

Although some organizations use the terms **Batch** and **Lot** interchangeably, the Inventory Engine treats them as separate business concepts to support a wider range of industries.

## Batch vs Lot

| Batch                                 | Lot                               |
| ------------------------------------- | --------------------------------- |
| Manufacturing or receiving grouping   | Traceability grouping             |
| Usually supplier or production driven | Business operational grouping     |
| Tracks quantities                     | Tracks lineage and genealogy      |
| Supports expiry                       | Supports recalls and traceability |

An organization may:

- Use Batches only
- Use Lots only
- Use both Batches and Lots
- Use neither

The Inventory Engine should support all four scenarios.

---

# 55. inv_lots

## Purpose

The **inv_lots** table stores inventory lots.

Lots provide operational traceability across the inventory lifecycle.

Examples include:

- Manufacturing Lot
- Production Lot
- Supplier Lot
- Import Lot
- Distribution Lot

---

## Ownership

Owned By:

```text
Inventory Engine
```

---

## Relationships

```text
inv_items

│

├── inv_batches

│

└── inv_lots

        │

        ├── inv_inventory_transaction_lines

        ├── inv_inventory_balances

        ├── inv_serial_numbers

        └── Customer Deliveries
```

---

## Key Fields

### Identity

```text
id

tenant_id

lot_number

lot_type
```

---

### References

```text
item_id

batch_id

warehouse_id
```

---

### Dates

```text
manufacturing_date

expiry_date

received_date
```

---

### Quantities

```text
quantity_received

quantity_available

quantity_reserved

quantity_allocated
```

---

### Status

```text
status
```

---

## Business Rules

- Lot Number generated by the Document Numbering Engine.
- One Batch may contain multiple Lots.
- Lots may exist without Batches.
- Lots participate in inventory allocation.
- Lots participate in traceability.
- Lots support recalls.

---

## Suggested Indexes

```text
tenant_id

lot_number

item_id

batch_id

warehouse_id
```

---

## Events Published

```text
LotCreated

LotUpdated

LotClosed

LotRecalled
```

---

# 56. Serial Number Management

Serial Number Management provides individual item traceability.

Each serialized item has a unique identity throughout its lifecycle.

Industries include:

- Electronics
- Medical Equipment
- Vehicles
- Machinery
- Firearms (where legally applicable)
- High-Value Assets
- Rental Equipment

---

# 57. inv_serial_numbers

## Purpose

Stores every serialized inventory item.

Each record represents one physical item.

---

## Ownership

Owned By:

```text
Inventory Engine
```

---

## Relationships

```text
inv_items

│

├── inv_serial_numbers

│

├── inv_inventory_transaction_lines

│

├── inv_inventory_balances

│

└── Warranty References
```

---

## Key Fields

### Identity

```text
id

tenant_id

serial_number
```

---

### Item

```text
item_id

variant_id

batch_id

lot_id
```

---

### Warehouse

```text
warehouse_id

location_id

bin_id
```

---

### Lifecycle

```text
status

received_date

sold_date

returned_date

disposed_date
```

---

### Customer

```text
customer_id

sales_document_reference
```

CRM owns the Customer.

Inventory stores only references.

---

### Warranty

```text
warranty_start

warranty_end
```

---

## Business Rules

- Every Serial Number is unique within a tenant.
- Serialized Items always have quantity = 1.
- One Serial belongs to one Item.
- One Serial may belong to one Batch.
- One Serial may belong to one Lot.
- Serial movement must be fully traceable.
- Serials participate in allocations and dispatch.

---

## Suggested Indexes

```text
tenant_id

serial_number

item_id

batch_id

lot_id

warehouse_id
```

---

## Events Published

```text
SerialCreated

SerialAssigned

SerialTransferred

SerialSold

SerialReturned

SerialDisposed
```

---

# 58. Expiry Management

Expiry Management enables proactive control of inventory nearing or exceeding its usable life.

Supported inventory includes:

- Food
- Medicine
- Chemicals
- Cosmetics
- Agriculture
- Laboratory Supplies

Expiry management supports:

- Manufacturing Date
- Expiry Date
- Best Before
- Use By
- Shelf Life
- Remaining Shelf Life

---

# 59. inv_expiry_tracking

## Purpose

Provides operational tracking of expiry-controlled inventory.

Expiry information may originate from:

- Batch
- Lot
- Individual Serial

depending on item configuration.

---

## Key Fields

```text
id

tenant_id

item_id

batch_id

lot_id

serial_number_id

warehouse_id

expiry_date

days_remaining

expiry_status

notification_sent
```

---

## Expiry Status

Examples:

```text
Valid

Near Expiry

Expired

Disposed

Returned
```

Status values should come from the Reference Data Engine.

---

## Business Rules

- Near-expiry thresholds are tenant configurable.
- Expired inventory should not be allocated unless explicitly permitted.
- Notification Engine should publish expiry alerts.
- FEFO allocation should prioritize earliest expiry.

---

## Events Published

```text
ItemNearExpiry

ItemExpired

ExpiryNotificationSent
```

---

# 60. Product Recall Management

The Inventory Engine should support complete product recall processes.

A recall may originate from:

- Supplier
- Manufacturer
- Internal Quality Inspection
- Regulatory Authority

Recall workflow:

```text
Recall Created

↓

Affected Item

↓

Affected Batch

↓

Affected Lot

↓

Affected Serials

↓

Affected Warehouses

↓

Affected Customers

↓

Notification

↓

Return

↓

Replacement

↓

Closure
```

---

# 61. inv_product_recalls

## Purpose

Tracks inventory recalls throughout their lifecycle.

---

## Key Fields

```text
id

tenant_id

recall_number

item_id

batch_id

lot_id

serial_number_id

reason

severity

recall_status

initiated_at

closed_at
```

---

## Business Rules

- Recall Number generated by the Document Numbering Engine.
- Recalls may apply at Item, Batch, Lot, or Serial level.
- Customer references come from CRM and Sales.
- Notification Engine informs affected customers.
- Customer Support manages customer communication.

---

## Events Published

```text
RecallInitiated

RecallCustomerNotified

RecallCompleted
```

---

# 62. Traceability Summary

The Traceability domain provides complete end-to-end visibility of inventory.

It enables organizations to answer questions such as:

- Which supplier supplied this item?
- Which batch was it received in?
- Which lot was it assigned to?
- Which serial number was sold?
- Which customer received it?
- Which warehouse currently holds it?
- Has it expired?
- Has it been recalled?
- Has it been returned?
- Has it been replaced?

This capability is essential for regulatory compliance, warranty management, quality assurance, healthcare, food safety, manufacturing, rentals, and enterprise inventory governance.

---

# 63. Warehouse Operations Tables

The Warehouse Operations domain manages the physical execution of inventory activities.

It is responsible for:

- Receiving
- Picking
- Packing
- Dispatch
- Internal Transfers
- Inventory Counts
- Adjustments
- Returns

Unlike Inventory Transactions, Warehouse Operations represent **business processes** that eventually generate Inventory Transactions.

```text
Warehouse Operation

↓

Approval (Optional)

↓

Execution

↓

Inventory Transaction(s)

↓

Inventory Balances Updated

↓

Business Events Published
```

Warehouse Operations focus on execution.

Inventory Transactions focus on recording.

---

# 64. inv_goods_receipts

## Purpose

Represents inventory received into the warehouse.

Goods Receipts may originate from:

- Purchase Orders
- Manufacturing Orders
- Customer Returns
- Rental Returns
- Stock Transfers
- Opening Balances

The Goods Receipt is a warehouse document.

The Inventory Transaction is the accounting event.

---

## Ownership

Owned By:

```text
Inventory Engine
```

Source documents remain owned by:

- Procurement
- Manufacturing
- Sales
- Rentals

---

## Relationships

```text
Purchase Order

↓

Goods Receipt

↓

Inventory Transactions

↓

Inventory Balances
```

---

## Key Fields

```text
id

tenant_id

receipt_number

warehouse_id

supplier_id

source_module

source_document_id

received_date

status

received_by

approved_by
```

---

## Business Rules

- Receipt Number generated by Document Numbering Engine.
- One Goods Receipt contains multiple lines.
- Receipt approval may be required.
- Inventory Transactions generated after posting.
- Finance receives inventory receipt events.

---

# 65. inv_goods_receipt_lines

Each Goods Receipt contains one or more receipt lines.

Each line references:

- Item
- Variant
- Batch
- Lot
- Serial Number
- Warehouse Location
- Quantity
- Unit Cost

---

# 66. inv_picking_lists

## Purpose

Represents warehouse picking operations.

Picking begins after inventory allocation.

---

## Workflow

```text
Reservation

↓

Allocation

↓

Pick List

↓

Picking

↓

Packing

↓

Dispatch
```

---

## Key Fields

```text
id

tenant_id

pick_list_number

warehouse_id

source_module

source_document_id

priority

status

picker_id

picked_at
```

---

## Business Rules

- Pick List Number generated by Document Numbering Engine.
- Picking may be manual or system optimized.
- Picking should support mobile devices.
- Picking confirms allocated inventory.

---

# 67. inv_picking_list_lines

Stores the individual items to be picked.

Each line includes:

```text
Item

Warehouse

Location

Zone

Aisle

Shelf

Bin

Batch

Lot

Serial

Quantity
```

---

# 68. inv_packing_lists

## Purpose

Represents packing operations after picking.

Packing prepares inventory for dispatch.

Support:

- Multiple Packages
- Box Tracking
- Package Weight
- Package Volume
- Shipping Labels

---

## Workflow

```text
Picked

↓

Packed

↓

Ready for Dispatch
```

---

## Key Fields

```text
id

tenant_id

packing_number

pick_list_id

warehouse_id

status

packed_by

packed_at
```

---

# 69. inv_dispatches

## Purpose

Represents inventory leaving the warehouse.

Dispatch may originate from:

- Sales Orders
- Transfers
- Rental Check-Out
- Project Allocations

---

## Workflow

```text
Packed

↓

Dispatch

↓

Delivery

↓

Inventory Transaction

↓

Finance Event
```

---

## Key Fields

```text
id

tenant_id

dispatch_number

warehouse_id

source_module

source_document

carrier

vehicle

driver

dispatch_date

status
```

---

## Business Rules

- Dispatch Number generated by Document Numbering Engine.
- Dispatch decreases inventory.
- Dispatch creates Inventory Transactions.
- Dispatch notifies Sales.

---

# 70. inv_stock_transfers

## Purpose

Represents movement between warehouses.

Supports:

- Warehouse Transfers
- Branch Transfers
- Bin Transfers
- Company Transfers (Future)

---

## Workflow

```text
Transfer Created

↓

Approval

↓

Picking

↓

Dispatch

↓

Transit

↓

Receipt

↓

Completed
```

---

## Key Fields

```text
id

tenant_id

transfer_number

source_warehouse

destination_warehouse

status

transfer_date
```

---

## Business Rules

- Transfer Number generated by Document Numbering Engine.
- Transfers generate two inventory movements:

Outbound

↓

Inbound

---

# 71. inv_stock_transfer_lines

Contains individual transfer items.

Supports:

- Batch
- Lot
- Serial
- Bin

---

# 72. inv_inventory_counts

## Purpose

Represents physical inventory verification.

Supports:

- Annual Counts
- Cycle Counts
- Spot Counts
- Blind Counts

---

## Workflow

```text
Count Created

↓

Assigned

↓

Count Performed

↓

Variance Review

↓

Approval

↓

Adjustment
```

---

## Key Fields

```text
id

tenant_id

count_number

warehouse

count_type

status

assigned_to

count_date
```

---

# 73. inv_inventory_count_lines

Stores counted quantities.

Each line includes:

- Expected Quantity
- Counted Quantity
- Variance
- Adjustment Recommendation

---

# 74. inv_stock_adjustments

## Purpose

Represents manual inventory corrections.

Examples:

- Damage
- Theft
- Expiry
- Shrinkage
- Correction
- Write-Off
- Write-On

---

## Workflow

```text
Adjustment Created

↓

Approval

↓

Inventory Transaction

↓

Inventory Updated
```

---

## Key Fields

```text
id

tenant_id

adjustment_number

adjustment_reason

warehouse

status

approved_by

adjustment_date
```

---

## Business Rules

- Adjustment Number generated by Document Numbering Engine.
- Approval required based on Workflow Engine configuration.
- Inventory Transactions generated after approval.

---

# 75. inv_stock_adjustment_lines

Stores individual adjustment lines.

Supports:

- Item
- Variant
- Batch
- Lot
- Serial
- Bin
- Quantity
- Reason

---

# 76. inv_inventory_returns

## Purpose

Represents inventory returned into stock.

Supports:

- Customer Returns
- Supplier Returns
- Rental Returns
- Project Returns
- Internal Returns

---

## Workflow

```text
Return Requested

↓

Inspection

↓

Approval

↓

Restock

↓

Inventory Transaction
```

---

## Key Fields

```text
id

tenant_id

return_number

return_type

warehouse

status

received_by

received_date
```

---

# 77. inv_inventory_return_lines

Contains returned inventory.

Supports:

- Batch
- Lot
- Serial
- Damage Status
- Restock Decision

---

# 78. Warehouse Operations Summary

The Warehouse Operations domain represents the execution layer of the Inventory Engine.

It manages:

- Receiving
- Picking
- Packing
- Dispatch
- Transfers
- Inventory Counts
- Adjustments
- Returns

These operational documents drive Inventory Transactions while maintaining complete traceability, auditability, and integration with Sales, Procurement, Finance, Manufacturing, Projects, Rentals, and Customer Support.

All operational documents follow the Business Suite standard:

- Header
- Lines
- Workflow
- Document Number
- Audit Trail
- Platform Event Bus Integration
- Document Management Engine Integration

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
