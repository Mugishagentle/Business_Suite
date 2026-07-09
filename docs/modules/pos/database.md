# DATABASE.md

# Point of Sale (POS) Module Database Design

---

# 1. Overview

The Point of Sale (POS) Module database is designed to support high-volume retail transactions while maintaining strict tenant isolation, branch separation, offline synchronization, and full integration with the Business Suite platform.

The database stores only POS-owned entities. It does **not** duplicate data owned by the CRM Module, Inventory Engine, Sales Module, or Finance Engine. Instead, it references those entities through foreign keys and integration identifiers.

All tables must support:

- Multi-Tenancy
- Multi-Company
- Multi-Branch
- Soft Deletes (where appropriate)
- Audit Metadata
- Row Level Security (RLS)
- Offline Synchronization
- Optimistic Concurrency

---

# 2. Entity Relationship Overview

```text
POS Store
    │
    ├──────── Registers
    │              │
    │              ├──────── Cash Drawers
    │              │
    │              └──────── POS Terminals
    │
    └──────── Cashier Shifts
                     │
                     ├──────── POS Sales
                     │          │
                     │          ├──────── Sale Lines
                     │          ├──────── Payments
                     │          ├──────── Discounts
                     │          ├──────── Taxes
                     │          ├──────── Returns
                     │          └──────── Exchanges
                     │
                     └──────── Cash Reconciliation

Offline Queue
```

---

# 3. Shared Columns

Unless otherwise stated, every table shall include the following standard columns.

| Column     | Type           |
| ---------- | -------------- |
| id         | UUID           |
| tenant_id  | UUID           |
| company_id | UUID           |
| branch_id  | UUID           |
| created_at | TIMESTAMP      |
| updated_at | TIMESTAMP      |
| created_by | UUID           |
| updated_by | UUID           |
| deleted_at | TIMESTAMP NULL |
| version    | INTEGER        |

---

# 4. POS Stores

## Table

```text
pos_stores
```

### Purpose

Represents a retail outlet operating one or more POS registers.

### Columns

| Column                 | Type         |
| ---------------------- | ------------ |
| code                   | VARCHAR(50)  |
| name                   | VARCHAR(150) |
| warehouse_id           | UUID         |
| default_price_list_id  | UUID         |
| receipt_template_id    | UUID         |
| default_tax_profile_id | UUID         |
| currency_code          | VARCHAR(10)  |
| status                 | VARCHAR(30)  |

---

# 5. POS Registers

## Table

```text
pos_registers
```

### Purpose

Represents an individual checkout register or till.

### Columns

| Column        | Type         |
| ------------- | ------------ |
| store_id      | UUID         |
| code          | VARCHAR(50)  |
| name          | VARCHAR(100) |
| terminal_id   | UUID         |
| drawer_id     | UUID         |
| status        | VARCHAR(30)  |
| allow_offline | BOOLEAN      |
| is_active     | BOOLEAN      |

---

# 6. POS Terminals

## Table

```text
pos_terminals
```

### Purpose

Represents the physical or virtual device used to process sales.

### Columns

| Column            | Type         |
| ----------------- | ------------ |
| register_id       | UUID         |
| device_name       | VARCHAR(150) |
| device_identifier | VARCHAR(150) |
| operating_system  | VARCHAR(100) |
| app_version       | VARCHAR(50)  |
| last_sync_at      | TIMESTAMP    |
| status            | VARCHAR(30)  |

---

# 7. Cash Drawers

## Table

```text
pos_cash_drawers
```

### Purpose

Represents a physical cash drawer assigned to a register.

### Columns

| Column           | Type          |
| ---------------- | ------------- |
| register_id      | UUID          |
| code             | VARCHAR(50)   |
| opening_balance  | DECIMAL(18,2) |
| current_balance  | DECIMAL(18,2) |
| expected_balance | DECIMAL(18,2) |
| status           | VARCHAR(30)   |

---

# 8. Cashier Shifts

## Table

```text
pos_shifts
```

### Purpose

Represents a cashier work session.

### Columns

| Column          | Type          |
| --------------- | ------------- |
| register_id     | UUID          |
| cashier_id      | UUID          |
| shift_number    | VARCHAR(50)   |
| opened_at       | TIMESTAMP     |
| closed_at       | TIMESTAMP     |
| opening_float   | DECIMAL(18,2) |
| closing_amount  | DECIMAL(18,2) |
| expected_amount | DECIMAL(18,2) |
| variance_amount | DECIMAL(18,2) |
| status          | VARCHAR(30)   |

---

# 9. POS Sales

## Table

```text
pos_sales
```

### Purpose

Represents a retail transaction.

### Columns

| Column            | Type           |
| ----------------- | -------------- |
| shift_id          | UUID           |
| register_id       | UUID           |
| terminal_id       | UUID           |
| customer_id       | UUID NULL      |
| sales_document_id | UUID NULL      |
| sale_number       | VARCHAR(50)    |
| transaction_type  | VARCHAR(30)    |
| sale_status       | VARCHAR(30)    |
| subtotal          | DECIMAL(18,2)  |
| discount_total    | DECIMAL(18,2)  |
| tax_total         | DECIMAL(18,2)  |
| grand_total       | DECIMAL(18,2)  |
| total_paid        | DECIMAL(18,2)  |
| change_given      | DECIMAL(18,2)  |
| is_offline        | BOOLEAN        |
| synced_at         | TIMESTAMP NULL |
| completed_at      | TIMESTAMP      |

---

# 10. POS Sale Lines

## Table

```text
pos_sale_lines
```

### Purpose

Stores individual products or services sold.

### Columns

| Column            | Type              |
| ----------------- | ----------------- |
| sale_id           | UUID              |
| inventory_item_id | UUID              |
| batch_id          | UUID NULL         |
| serial_number     | VARCHAR(150) NULL |
| quantity          | DECIMAL(18,4)     |
| unit_price        | DECIMAL(18,2)     |
| discount_amount   | DECIMAL(18,2)     |
| tax_amount        | DECIMAL(18,2)     |
| line_total        | DECIMAL(18,2)     |

---

# 11. POS Payments

## Table

```text
pos_payments
```

### Purpose

Stores payment details for a sale.

### Columns

| Column             | Type          |
| ------------------ | ------------- |
| sale_id            | UUID          |
| payment_method     | VARCHAR(50)   |
| amount             | DECIMAL(18,2) |
| reference_number   | VARCHAR(150)  |
| authorization_code | VARCHAR(150)  |
| payment_status     | VARCHAR(30)   |
| finance_event_id   | UUID NULL     |

---

# 12. POS Discounts

## Table

```text
pos_discounts
```

### Purpose

Tracks discounts applied during a sale.

### Columns

| Column        | Type          |
| ------------- | ------------- |
| sale_line_id  | UUID          |
| discount_type | VARCHAR(50)   |
| discount_name | VARCHAR(150)  |
| percentage    | DECIMAL(8,4)  |
| amount        | DECIMAL(18,2) |
| approved_by   | UUID NULL     |

---

# 13. POS Taxes

## Table

```text
pos_taxes
```

### Purpose

Stores tax breakdowns for reporting and auditing.

### Columns

| Column         | Type          |
| -------------- | ------------- |
| sale_line_id   | UUID          |
| tax_code       | VARCHAR(50)   |
| tax_name       | VARCHAR(150)  |
| tax_rate       | DECIMAL(8,4)  |
| taxable_amount | DECIMAL(18,2) |
| tax_amount     | DECIMAL(18,2) |

---

# 14. Suspended Sales

## Table

```text
pos_suspended_sales
```

### Purpose

Stores suspended or parked transactions.

### Columns

| Column       | Type           |
| ------------ | -------------- |
| sale_id      | UUID           |
| suspended_by | UUID           |
| suspended_at | TIMESTAMP      |
| reason       | TEXT           |
| expires_at   | TIMESTAMP NULL |

---

# 15. Returns

## Table

```text
pos_returns
```

### Purpose

Represents returned items.

### Columns

| Column                   | Type        |
| ------------------------ | ----------- |
| original_sale_id         | UUID        |
| return_sale_id           | UUID        |
| return_reason            | TEXT        |
| refund_method            | VARCHAR(50) |
| approved_by              | UUID NULL   |
| inventory_transaction_id | UUID NULL   |

---

# 16. Exchanges

## Table

```text
pos_exchanges
```

### Purpose

Represents product exchange transactions.

### Columns

| Column           | Type          |
| ---------------- | ------------- |
| original_sale_id | UUID          |
| exchange_sale_id | UUID          |
| price_difference | DECIMAL(18,2) |
| approved_by      | UUID NULL     |

---

# 17. Cash Reconciliation

## Table

```text
pos_cash_reconciliations
```

### Purpose

Stores end-of-shift cash reconciliation.

### Columns

| Column        | Type          |
| ------------- | ------------- |
| shift_id      | UUID          |
| counted_cash  | DECIMAL(18,2) |
| expected_cash | DECIMAL(18,2) |
| variance      | DECIMAL(18,2) |
| approved_by   | UUID NULL     |
| notes         | TEXT          |

---

# 18. POS Device Sessions

## Table

```text
pos_device_sessions
```

### Purpose

Tracks active terminal sessions.

### Columns

| Column      | Type           |
| ----------- | -------------- |
| terminal_id | UUID           |
| cashier_id  | UUID           |
| login_at    | TIMESTAMP      |
| logout_at   | TIMESTAMP NULL |
| ip_address  | VARCHAR(100)   |
| status      | VARCHAR(30)    |

---

# 19. Offline Synchronization Queue

## Table

```text
pos_sync_queue
```

### Purpose

Stores transactions waiting to synchronize.

### Columns

| Column               | Type           |
| -------------------- | -------------- |
| local_transaction_id | VARCHAR(150)   |
| sale_id              | UUID           |
| payload              | JSONB          |
| sync_status          | VARCHAR(30)    |
| retry_count          | INTEGER        |
| last_attempt_at      | TIMESTAMP NULL |
| synced_at            | TIMESTAMP NULL |
| error_message        | TEXT NULL      |

---

# 20. Gift Cards

## Table

```text
pos_gift_cards
```

### Purpose

Tracks gift cards issued and redeemed through POS.

### Columns

| Column          | Type          |
| --------------- | ------------- |
| card_number     | VARCHAR(100)  |
| customer_id     | UUID NULL     |
| original_amount | DECIMAL(18,2) |
| current_balance | DECIMAL(18,2) |
| expiry_date     | DATE NULL     |
| status          | VARCHAR(30)   |

---

# 21. Gift Card Transactions

## Table

```text
pos_gift_card_transactions
```

### Columns

| Column           | Type          |
| ---------------- | ------------- |
| gift_card_id     | UUID          |
| sale_id          | UUID NULL     |
| transaction_type | VARCHAR(30)   |
| amount           | DECIMAL(18,2) |
| balance_after    | DECIMAL(18,2) |

---

# 22. POS Coupons

## Table

```text
pos_coupons
```

### Purpose

Tracks coupon usage within POS.

### Columns

| Column          | Type           |
| --------------- | -------------- |
| coupon_code     | VARCHAR(100)   |
| campaign_name   | VARCHAR(150)   |
| customer_id     | UUID NULL      |
| discount_amount | DECIMAL(18,2)  |
| redeemed_at     | TIMESTAMP NULL |
| sale_id         | UUID NULL      |
| status          | VARCHAR(30)    |

---

# 23. Indexing Strategy

High-performance indexes should be created on:

- tenant_id
- company_id
- branch_id
- store_id
- register_id
- terminal_id
- cashier_id
- customer_id
- sale_number
- completed_at
- sale_status
- payment_method
- sync_status
- shift_number

Composite indexes:

- (tenant_id, sale_number)
- (tenant_id, completed_at)
- (tenant_id, branch_id, completed_at)
- (tenant_id, register_id, completed_at)
- (tenant_id, customer_id)
- (tenant_id, cashier_id, completed_at)

---

# 24. Row Level Security (RLS)

Every POS table shall enforce Row Level Security.

Policies must ensure users can only access data for:

- Their Tenant
- Authorized Company
- Authorized Branch
- Authorized Store
- Authorized Register (where applicable)

Administrative users may receive broader access through the Authorization Engine.

---

# 25. Audit & Soft Deletes

All create, update, delete, void, refund, reconciliation, and synchronization operations shall be recorded by the Platform Activity & Audit Engine.

Business records such as sales, payments, returns, and reconciliations shall not be physically deleted. Where reversals are required, they shall be handled through status transitions and compensating transactions to preserve financial and operational traceability.

---

# 26. Database Constraints

The database shall enforce:

- A shift must belong to one register.
- A register must belong to one store.
- A sale must belong to one shift.
- A sale must contain at least one sale line.
- Payment totals must equal the sale total before completion (except where partial payment rules apply).
- Returned quantities cannot exceed original sold quantities.
- Exchanges must reference an original sale.
- Offline transactions must have unique local transaction identifiers.
- Gift card balances shall never become negative.
- Sale numbers shall be unique per tenant.

---

# 27. Database Summary

The POS database model is optimized for:

- High-volume transaction processing
- Fast cashier operations
- Multi-store and multi-branch deployments
- Offline-first synchronization
- Enterprise reporting
- Secure tenant isolation
- Complete auditability
- Seamless integration with the Sales Module, Inventory Engine, CRM Module, Finance Engine, and Platform Engines without duplicating ownership.
