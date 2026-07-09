# UI.md

# Point of Sale (POS) Module User Interface Specification

---

# 1. Overview

The Point of Sale (POS) Module provides a modern, responsive, and high-performance retail interface designed for continuous daily operation.

Unlike administrative modules, the POS interface prioritizes:

- Speed
- Simplicity
- Touch-friendly interaction
- Keyboard efficiency
- Barcode scanner support
- Minimal clicks
- Offline capability
- Large, readable controls

The UI follows the Business Suite platform standards using:

- React
- TypeScript
- Tailwind CSS
- shadcn/ui
- React Hook Form
- Zod
- React Router

---

# 2. UI Design Principles

The POS user experience shall follow these principles:

- Touch-first design
- Responsive layouts
- Keyboard shortcuts
- Minimal navigation depth
- Fast product lookup
- Persistent shopping cart
- Real-time calculations
- Accessible color contrast
- Optimized for 13"–27" displays
- Tablet compatible
- PWA compatible

---

# 3. Navigation Structure

```text
Point of Sale
│
├── Dashboard
├── New Sale
├── Suspended Sales
├── Returns
├── Exchanges
├── Shift Management
├── Cash Drawer
├── Registers
├── Stores
├── Devices
├── Pricing
├── Promotions
├── Gift Cards
├── Coupons
├── Reports
└── Settings
```

Menu visibility is controlled by the Authorization Engine.

---

# 4. Screen Inventory

The POS Module includes the following primary screens:

| Screen              | Purpose                      |
| ------------------- | ---------------------------- |
| POS Dashboard       | Retail overview              |
| New Sale            | Checkout interface           |
| Product Search      | Product lookup               |
| Customer Lookup     | Customer search              |
| Payment             | Payment processing           |
| Receipt Preview     | Receipt display and printing |
| Suspended Sales     | Resume parked transactions   |
| Returns             | Process returns              |
| Exchanges           | Process exchanges            |
| Shift Opening       | Start cashier shift          |
| Shift Closing       | Close cashier shift          |
| Cash Reconciliation | Reconcile cash drawer        |
| Register Management | Manage registers             |
| Store Management    | Configure stores             |
| Terminal Management | Configure devices            |
| Promotions          | Retail promotions            |
| Pricing Rules       | Retail pricing               |
| Reports             | POS analytics                |
| Settings            | POS configuration            |

---

# 5. POS Dashboard

## Purpose

Provides a quick operational overview before trading begins.

### Widgets

- Current Shift
- Current Register
- Active Cashier
- Opening Float
- Sales Today
- Transactions Today
- Cash Drawer Balance
- Offline Queue Status
- Pending Suspended Sales
- Low Stock Alerts
- Sync Status

### Actions

- Open Shift
- Start Sale
- Resume Sale
- View Reports
- Reconcile Drawer

---

# 6. New Sale Screen

This is the primary cashier workspace.

## Layout

```text
┌──────────────────────────────────────────────────────────────────────────┐
│ Store | Register | Cashier | Shift | Time | Connection Status            │
├───────────────────────┬──────────────────────────────────────────────────┤
│ Product Search        │ Shopping Cart                                   │
│ Barcode Scanner       │--------------------------------------------------│
│ Categories            │ Qty  Product      Price     Total                │
│ Filters               │ Qty  Product      Price     Total                │
│                       │ Qty  Product      Price     Total                │
│                       │                                                  │
│                       │--------------------------------------------------│
│                       │ Subtotal                                         │
│                       │ Discounts                                        │
│                       │ Taxes                                            │
│                       │ Grand Total                                      │
├───────────────────────┴──────────────────────────────────────────────────┤
│ Customer │ Discount │ Suspend │ Void │ Payment │ Complete Sale           │
└──────────────────────────────────────────────────────────────────────────┘
```

---

# 7. Product Search

Supports:

- Barcode scanning
- SKU search
- Product name
- Category
- Brand
- Variant
- Batch
- Serial number

Each product card displays:

- Product image
- Product name
- SKU
- Price
- Available quantity
- Stock status
- Promotion badge

---

# 8. Shopping Cart

Each cart line displays:

- Product
- Variant
- Quantity
- Unit Price
- Discount
- Tax
- Line Total

Supported actions:

- Increase quantity
- Decrease quantity
- Edit quantity
- Remove line
- Apply discount
- Select batch
- Select serial number
- Add note

Real-time calculations update automatically.

---

# 9. Customer Selection

The customer panel supports:

- Walk-in customer
- Existing customer search
- Loyalty lookup
- Phone number search
- Membership number
- QR code lookup

Displayed information:

- Customer Name
- Loyalty Status
- Available Points
- Outstanding Balance
- Pricing Tier
- Recent Purchases

---

# 10. Payment Screen

## Layout

```text
┌────────────────────────────────────────────┐
│ Total Due                                  │
│                                            │
│ Payment Methods                            │
│ ○ Cash                                     │
│ ○ Card                                     │
│ ○ Mobile Money                             │
│ ○ Bank Transfer                            │
│ ○ Gift Card                                │
│ ○ Store Credit                             │
│                                            │
│ Amount Received                            │
│ Change                                     │
│                                            │
│ Complete Payment                           │
└────────────────────────────────────────────┘
```

Supports:

- Split payment
- Partial payment (tenant configurable)
- Multiple currencies (where enabled)

---

# 11. Receipt Preview

Displays:

- Company details
- Store details
- Receipt number
- QR code
- Cashier
- Customer
- Items
- Discounts
- Taxes
- Payment methods
- Change
- Thank-you message

Actions:

- Print
- Email
- SMS
- Download PDF
- Reprint

---

# 12. Suspended Sales

Displays:

| Column    |
| --------- |
| Reference |
| Customer  |
| Cashier   |
| Date      |
| Total     |
| Status    |

Actions:

- Resume
- Delete (permission-controlled)
- View Details

---

# 13. Returns Screen

Search methods:

- Receipt Number
- Invoice Number
- Customer
- Barcode
- Date

Displays:

- Original items
- Quantities sold
- Quantities returned
- Eligible return quantity

Actions:

- Select items
- Enter reason
- Choose refund method
- Process return

---

# 14. Exchanges Screen

Displays:

- Original sale
- Returned items
- Replacement items
- Price difference
- Refund amount
- Additional payment

Actions:

- Complete exchange
- Cancel

---

# 15. Shift Opening Screen

Fields:

- Store
- Register
- Cash Drawer
- Opening Float
- Notes

Buttons:

- Open Shift
- Cancel

Validation is performed using Zod and React Hook Form.

---

# 16. Shift Closing Screen

Displays:

- Shift Summary
- Cash Sales
- Card Sales
- Mobile Money Sales
- Refunds
- Discounts
- Expected Cash
- Counted Cash
- Variance

Actions:

- Save Draft
- Recount
- Close Shift

---

# 17. Cash Reconciliation Screen

Displays:

- Opening Float
- Cash Received
- Cash Paid Out
- Refunds
- Expected Cash
- Counted Cash
- Variance

Buttons:

- Submit
- Request Approval (if required)
- Print Summary

---

# 18. Register Management

Table Columns

- Register Code
- Register Name
- Store
- Assigned Terminal
- Assigned Drawer
- Status
- Active Shift

Actions:

- Add Register
- Edit
- Activate
- Deactivate
- View

Create and Edit actions open in a modal dialog.

---

# 19. Store Management

Displays:

- Store Name
- Branch
- Warehouse
- Default Price List
- Tax Profile
- Status

Actions:

- Create
- Edit
- View
- Activate
- Archive

Forms are displayed in modal dialogs.

---

# 20. Device Management

Displays:

- Device Name
- Device ID
- Register
- App Version
- Last Sync
- Status

Actions:

- Register Device
- Edit
- Disable
- View

---

# 21. Pricing Screen

Displays:

- Price Lists
- Branch Pricing
- Customer Pricing
- Quantity Pricing
- Tax Mode

Actions:

- Create Rule
- Edit Rule
- Disable Rule

---

# 22. Promotions Screen

Displays active promotions.

Columns:

- Promotion Name
- Type
- Start Date
- End Date
- Status

Actions:

- Create
- Edit
- Activate
- Deactivate

---

# 23. Gift Cards

Displays:

- Card Number
- Customer
- Balance
- Expiry
- Status

Actions:

- Issue
- Recharge
- Redeem
- Deactivate

---

# 24. Coupons

Displays:

- Coupon Code
- Campaign
- Value
- Status
- Redeemed

Actions:

- Create
- Disable
- View Usage

---

# 25. Reports Screen

Available reports:

- Daily Sales
- Sales by Store
- Sales by Register
- Sales by Cashier
- Product Sales
- Category Sales
- Payment Analysis
- Returns
- Discounts
- Taxes
- Shift Summary
- Cash Drawer Summary
- Offline Synchronization Status

All reports are rendered through the Reporting Engine.

---

# 26. Search & Filtering

All listing pages support:

- Global search
- Advanced filters
- Saved filters
- Sorting
- Pagination
- Column selection
- Export (permission-controlled)

---

# 27. Notifications

The UI displays non-blocking notifications for:

- Sale completed
- Payment accepted
- Shift opened
- Shift closed
- Synchronization successful
- Synchronization failed
- Low stock alerts
- Offline mode activated
- Printer unavailable
- Fiscal device warning (where applicable)

---

# 28. Loading States

Every action button must display a loading spinner while processing.

Examples:

- Complete Sale
- Process Payment
- Print Receipt
- Open Shift
- Close Shift
- Process Return
- Synchronize Offline Sales

---

# 29. Empty States

Each page shall display meaningful empty-state messages.

Examples:

- No suspended sales found.
- No returns available.
- No promotions configured.
- No gift cards issued.
- No reports available for the selected period.

---

# 30. Validation

All forms must use:

- React Hook Form
- Zod validation
- Inline validation messages
- Field-level validation
- Server-side validation before persistence

HTML-only validation must not be used.

---

# 31. Keyboard Shortcuts

The POS interface should support configurable keyboard shortcuts.

Suggested defaults:

| Shortcut | Action                                   |
| -------- | ---------------------------------------- |
| F2       | Product Search                           |
| F3       | Customer Lookup                          |
| F4       | Suspend Sale                             |
| F5       | Payment                                  |
| F6       | Complete Sale                            |
| F7       | Returns                                  |
| F8       | Reprint Receipt                          |
| F9       | Open Cash Drawer (permission-controlled) |
| Esc      | Cancel Current Action                    |
| Ctrl + F | Search                                   |
| Ctrl + P | Print Receipt                            |

---

# 32. Barcode Scanner Support

The UI shall support USB, Bluetooth, and integrated barcode scanners.

Supported behaviors:

- Auto-focus on scan field
- Instant lookup
- Continuous scanning
- Duplicate detection
- Unknown barcode notification

---

# 33. Responsive Design

The UI shall support:

- Desktop POS terminals
- Touchscreen kiosks
- Tablets
- Large monitors
- Progressive Web App (PWA)

Layouts adapt automatically while preserving checkout efficiency.

---

# 34. Accessibility

The POS UI shall comply with accessibility best practices.

Features include:

- Keyboard navigation
- Screen reader support
- High-contrast mode compatibility
- Clear focus indicators
- Accessible form labels
- Sufficient touch target sizes
- Color-independent status indicators

---

# 35. UI Standards

The POS Module shall follow Business Suite UI standards:

- Use React Router for navigation.
- Use modal dialogs for Create and Edit operations.
- Use shadcn/ui components consistently.
- Display loading indicators for all asynchronous actions.
- Maintain a consistent design language across all screens.
- Respect tenant branding where configured.
- Ensure fast rendering for high-volume retail environments.

---

# 36. Summary

The POS UI is optimized for speed, usability, and reliability while remaining consistent with the Business Suite design system. It provides an intuitive cashier experience, supports touch and keyboard workflows, integrates seamlessly with platform engines, and scales from single-store deployments to enterprise multi-branch retail operations.
