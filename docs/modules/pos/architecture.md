# Point of Sale (POS) Module Architecture

---

# 1. Overview

The POS Module architecture defines how Business Suite supports fast, secure, multi-branch, and offline-capable retail sales operations while respecting enterprise engine ownership.

The POS Module is designed as a business module, not an accounting engine, inventory engine, or customer master engine.

It coordinates real-time retail transactions and delegates specialized responsibilities to the appropriate platform engines and business modules.

---

# 2. Architectural Role

The POS Module acts as a retail transaction orchestration layer.

It connects:

- Cashiers
- Registers
- Stores
- Customers
- Products
- Payments
- Receipts
- Inventory
- Sales Documents
- Finance Events
- Reports
- Notifications

The POS Module owns the retail transaction experience but does not duplicate Finance, Sales, CRM, or Inventory responsibilities.

---

# 3. High-Level Architecture

```text
┌───────────────────────────────────────────────┐
│                 POS Frontend                  │
│ React + TypeScript + shadcn/ui + Tailwind     │
└───────────────────────┬───────────────────────┘
                        │
                        ▼
┌───────────────────────────────────────────────┐
│              POS Application Layer             │
│ Sales Session, Checkout, Shift, Register APIs │
└───────────────────────┬───────────────────────┘
                        │
                        ▼
┌───────────────────────────────────────────────┐
│                POS Domain Layer                │
│ Sale, Shift, Register, Drawer, Payment, Return│
└───────────────────────┬───────────────────────┘
                        │
                        ▼
┌───────────────────────────────────────────────┐
│             POS Infrastructure Layer           │
│ Repositories, Sync Queue, Realtime, Storage    │
└───────────────────────┬───────────────────────┘
                        │
                        ▼
┌───────────────────────────────────────────────┐
│              Platform + Business Engines       │
│ CRM, Sales, Inventory, Finance, Audit, Reports│
└───────────────────────────────────────────────┘
```

---

# 4. Architectural Principles

The POS Module follows these principles:

- POS is a sales channel.
- POS does not post accounting entries.
- POS does not own product stock.
- POS does not own customer master data.
- POS does not own official sales documents.
- POS publishes events.
- POS consumes platform services.
- POS must remain fast and cashier-friendly.
- POS must support offline operation where enabled.
- POS must maintain full auditability.

---

# 5. Domain Boundaries

## 5.1 POS-Owned Domain

The POS Module owns:

- POS Store Configuration
- POS Register Configuration
- POS Terminal Configuration
- Cash Drawer Assignment
- Cashier Shift
- POS Sale Session
- POS Cart
- POS Payment Session
- Suspended Sale
- Voided Sale
- POS Return Session
- POS Exchange Session
- Cash Reconciliation
- Offline Sync Queue

---

## 5.2 External Domains

The POS Module consumes external domains as follows:

| Domain        | Owner                   | POS Usage                         |
| ------------- | ----------------------- | --------------------------------- |
| Customer      | CRM Module              | Lookup, loyalty, purchase history |
| Product       | Inventory Engine        | Product search, barcode lookup    |
| Stock         | Inventory Engine        | Availability, deduction, return   |
| Invoice       | Sales Module            | Instant invoice generation        |
| Receipt       | Sales Module            | Official receipt generation       |
| Accounting    | Finance Engine          | Event consumption and posting     |
| Reports       | Reporting Engine        | POS analytics                     |
| Notifications | Notification Engine     | Email/SMS receipts                |
| Audit         | Activity & Audit Engine | Operational tracking              |
| Permissions   | Authorization Engine    | Cashier permissions               |

---

# 6. Module Components

## 6.1 POS Store Service

Manages POS store setup.

Responsibilities:

- Store creation
- Store-to-branch mapping
- Store operating rules
- Store default warehouse
- Store tax settings
- Store receipt settings
- Store payment settings

---

## 6.2 Register Service

Manages registers and tills.

Responsibilities:

- Register creation
- Register assignment
- Register status
- Register opening
- Register locking
- Register closing
- Terminal binding

---

## 6.3 Cash Drawer Service

Manages cash drawers.

Responsibilities:

- Cash drawer creation
- Cash drawer assignment
- Cash float tracking
- Cash movement tracking
- Cash reconciliation
- Cash difference recording

---

## 6.4 Cashier Shift Service

Manages cashier work sessions.

Responsibilities:

- Shift opening
- Shift closing
- Cashier login
- Register assignment
- Shift totals
- Payment totals
- Shift reconciliation
- Supervisor approval where required

---

## 6.5 POS Sales Service

Manages retail sales.

Responsibilities:

- Cart creation
- Product adding
- Barcode scanning
- Customer selection
- Quantity updates
- Discounts
- Promotions
- Tax calculation
- Sale completion
- Sale suspension
- Sale voiding

---

## 6.6 POS Payment Service

Manages payments.

Responsibilities:

- Cash payments
- Card payments
- Mobile money payments
- Bank transfer payments
- Store credit payments
- Gift voucher payments
- Split payments
- Partial payments
- Payment validation

---

## 6.7 POS Return Service

Manages returns and exchanges.

Responsibilities:

- Receipt lookup
- Return validation
- Return line selection
- Refund processing
- Exchange handling
- Return-to-stock request
- Credit note request

---

## 6.8 POS Receipt Service

Manages receipt presentation.

Responsibilities:

- Receipt preview
- Receipt print
- Digital receipt request
- Email receipt request
- SMS receipt request
- QR code display
- Receipt reprint

Official receipt documents remain owned by the Sales Module.

---

## 6.9 POS Pricing Service

Coordinates pricing.

Responsibilities:

- Price list resolution
- Branch pricing
- Customer pricing
- Quantity pricing
- Promotion application
- Discount validation
- Tax-inclusive pricing
- Tax-exclusive pricing

---

## 6.10 Offline Sync Service

Manages offline POS operations.

Responsibilities:

- Offline transaction queue
- Local draft transactions
- Sync retries
- Sync status
- Conflict detection
- Conflict resolution
- Offline receipt references

---

# 7. Frontend Architecture

The POS frontend is optimized for speed and usability.

Recommended feature structure:

```text
src/
└── features/
    └── pos/
        ├── components/
        │   ├── cart/
        │   ├── cashier/
        │   ├── checkout/
        │   ├── customer/
        │   ├── products/
        │   ├── receipts/
        │   ├── registers/
        │   ├── shifts/
        │   └── returns/
        ├── hooks/
        ├── pages/
        ├── routes/
        ├── schemas/
        ├── services/
        ├── stores/
        ├── types/
        └── utils/
```

---

# 8. Backend Architecture

The backend uses Supabase PostgreSQL, RLS, Edge Functions, and Realtime.

Recommended backend structure:

```text
supabase/
├── functions/
│   └── pos/
│       ├── open-shift/
│       ├── close-shift/
│       ├── complete-sale/
│       ├── void-sale/
│       ├── process-return/
│       ├── sync-offline-sale/
│       └── reprint-receipt/
└── migrations/
    └── pos/
```

---

# 9. Core Data Flow

## 9.1 Sale Completion Flow

```text
Cashier
  ↓
POS Cart
  ↓
Price + Tax Resolution
  ↓
Payment Capture
  ↓
Sales Module Document Request
  ↓
Inventory Engine Stock Deduction Request
  ↓
Finance Event Published
  ↓
Receipt Generated
  ↓
Notification Sent
  ↓
Audit Recorded
  ↓
Reporting Updated
```

---

# 10. Event-Driven Architecture

The POS Module publishes events instead of directly performing external responsibilities.

Examples:

```text
pos.sale.started
pos.sale.completed
pos.sale.suspended
pos.sale.voided
pos.payment.received
pos.refund.processed
pos.exchange.completed
pos.shift.opened
pos.shift.closed
pos.cash.float.added
pos.cash.difference.recorded
pos.receipt.generated
pos.offline.sale.synced
```

---

# 11. Integration Architecture

## 11.1 CRM Integration

Used for:

- Customer search
- Loyalty customer selection
- Customer discount lookup
- Purchase history
- Customer receipt contacts

---

## 11.2 Inventory Integration

Used for:

- Barcode lookup
- Product search
- Stock availability
- Batch validation
- Serial validation
- Expiry validation
- Stock deduction
- Return to stock

---

## 11.3 Sales Integration

Used for:

- POS invoice creation
- Receipt creation
- Credit note creation
- Exchange document creation
- Customer statement updates

---

## 11.4 Finance Integration

Used through events for:

- Cash sales
- Card sales
- Mobile money sales
- Refunds
- Cash float
- Cash differences
- Daily cash closing

---

## 11.5 Reporting Integration

Used for:

- Daily sales report
- Shift summary
- Cashier performance
- Product sales
- Payment analysis
- Returns
- Discounts
- Taxes

---

# 12. Offline Architecture

Offline mode is optional and tenant-configurable.

When enabled, POS supports:

- Local cart creation
- Local sales capture
- Local receipt references
- Local queue storage
- Background synchronization
- Retry handling
- Conflict detection

Offline mode must not bypass:

- Tenant context
- Cashier permissions
- Register assignment
- Shift rules
- Audit requirements
- Numbering rules

---

# 13. Offline Sync Flow

```text
Offline Sale Captured
  ↓
Local Queue Created
  ↓
Temporary Receipt Issued
  ↓
Connection Restored
  ↓
Sync Service Sends Transaction
  ↓
Server Validates Tenant + Shift + Register
  ↓
Inventory Conflict Checked
  ↓
Sales Documents Generated
  ↓
Finance Events Published
  ↓
Official Receipt Issued
  ↓
Local Queue Marked Synced
```

---

# 14. Conflict Resolution

Possible conflicts include:

- Item no longer available
- Batch expired
- Serial already sold
- Price changed
- Tax changed
- Customer inactive
- Register closed
- Shift closed
- Duplicate transaction

Resolution options:

- Auto-accept
- Supervisor review
- Reverse transaction
- Adjust stock difference
- Create exception record
- Notify manager

---

# 15. Security Architecture

The POS Module relies on:

- Supabase Auth
- Row Level Security
- Tenant Context
- Authorization Engine
- Audit Engine
- Register Assignment
- Shift Validation
- Supervisor Overrides

Sensitive POS actions require explicit permission.

Examples:

```text
pos.sale.create
pos.sale.void
pos.refund.create
pos.discount.override
pos.price.override
pos.shift.open
pos.shift.close
pos.drawer.reconcile
pos.offline.sync
```

---

# 16. Performance Architecture

POS screens must be optimized for speed.

Performance considerations:

- Fast barcode search
- Cached product lookup
- Cached price lists
- Cached tax settings
- Minimal checkout latency
- Indexed transaction tables
- Realtime shift totals
- Batched offline sync
- Lazy loading of product catalogs
- Pagination for product search

---

# 17. Realtime Architecture

Supabase Realtime supports:

- Register status updates
- Shift status changes
- Supervisor override requests
- Payment confirmation
- Offline sync status
- Cash drawer alerts
- Stock warning notifications

---

# 18. Repository Pattern

All data access must go through repositories.

Example repositories:

```text
PosStoreRepository
PosRegisterRepository
PosShiftRepository
PosSaleRepository
PosPaymentRepository
PosReturnRepository
PosCashDrawerRepository
PosSyncQueueRepository
```

Repositories must enforce:

- Tenant filtering
- Branch filtering
- Company filtering
- Status validation
- Audit metadata
- Soft delete where applicable

---

# 19. Service Layer Pattern

Business operations must be handled through services.

Example services:

```text
OpenShiftService
CloseShiftService
CompleteSaleService
VoidSaleService
ProcessReturnService
ApplyDiscountService
ResolvePriceService
SyncOfflineSaleService
CashReconciliationService
```

---

# 20. Error Handling

The POS Module must handle:

- Network failure
- Payment failure
- Stock failure
- Pricing failure
- Duplicate receipt
- Register locked
- Shift already closed
- Permission denied
- Offline sync failure
- Receipt printing failure

Errors must be user-friendly for cashiers and fully auditable for administrators.

---

# 21. Scalability Considerations

The POS architecture supports:

- Multiple tenants
- Multiple companies
- Multiple branches
- Multiple stores
- Multiple registers
- Multiple terminals
- Concurrent cashiers
- High transaction volume
- Offline queue synchronization
- Realtime reporting

---

# 22. Deployment Considerations

POS can be deployed as:

- Web POS
- PWA POS
- Tablet POS
- Mobile POS
- Desktop browser POS
- Branch counter POS

Future integrations may include:

- Receipt printers
- Barcode scanners
- Cash drawers
- Fiscal devices
- Customer display screens
- Weighing scales
- Card terminals
- Mobile money gateways

---

# 23. Architectural Rules

The POS Module must follow these rules:

- Never post directly to the General Ledger.
- Never directly modify inventory balances.
- Never create customers outside CRM rules.
- Never generate document numbers manually.
- Never bypass Authorization Engine.
- Never bypass Audit Engine.
- Never duplicate Reporting Engine logic.
- Never trust offline data without server validation.
- Never allow cross-tenant POS access.
- Never complete sale without valid shift and register.

---

# 24. Summary

The POS Module architecture provides a secure, scalable, and enterprise-ready retail transaction layer for Business Suite.

It enables fast sales processing while integrating cleanly with Finance, CRM, Sales, Inventory, Reporting, Notifications, Audit, Workflow, Authorization, and the Event Bus.

The module is suitable for SMEs and enterprise retail environments while remaining consistent with the broader Business Suite engine-based platform architecture.
