# DATABASE.md

# Expenses Management Engine Database Design

---

# 1. Overview

This document defines the database architecture for the Expenses Management Engine.

The database design supports:

- Expense Requisitions
- Requisition Lines
- Direct Expenses
- Staff Advances
- Advance Disbursements
- Accountabilities
- Accountability Lines
- Expense Claims
- Reimbursements
- Refunds
- Recoveries
- Petty Cash
- Travel Expenses
- Per Diem
- Mileage Claims
- Expense Policies
- Budget Validation
- Expense Allocations
- Workflow References
- Finance Integration
- Procurement Conversion
- Payroll Recovery
- Auditability
- Multi-Tenant Isolation

The database is implemented using PostgreSQL through Supabase.

The schema must enforce:

- Tenant isolation
- Referential integrity
- Valid lifecycle transitions
- Financial traceability
- Data immutability after completion
- Optimistic concurrency
- Row Level Security
- Audit metadata
- Idempotent integrations

---

# 2. Database Design Principles

The Expenses Management Engine database follows these principles:

- Every business record belongs to a tenant.
- Company and branch context must be captured where applicable.
- Financially relevant records must never be physically deleted.
- Monetary values must use fixed-precision decimal types.
- Currency must always be stored explicitly.
- Approval workflows must be referenced, not duplicated.
- Documents must be referenced through the Document Management Engine.
- Employee records must be referenced from the Human Resources Engine.
- Accounting records must remain owned by the Finance Engine.
- Budget balances must remain owned by the Finance Engine or Budget Service.
- Lifecycle transitions must be validated server-side.
- Integration events must use the transactional outbox pattern.
- Records must support optimistic concurrency.
- Every sensitive change must be auditable.

---

# 3. Schema Naming

Recommended schema:

```sql
expenses
```

Tables may use either:

```text
expenses.expense_requisitions
```

or the platform-wide prefix convention:

```text
expense_requisitions
```

The final approach must remain consistent across all Business Suite engines.

This document uses unqualified table names for readability.

---

# 4. Standard Columns

Most Expenses Management Engine tables should include the following columns where applicable.

```sql
id uuid primary key default gen_random_uuid(),

tenant_id uuid not null,
company_id uuid null,
branch_id uuid null,

created_at timestamptz not null default now(),
created_by uuid not null,

updated_at timestamptz not null default now(),
updated_by uuid null,

deleted_at timestamptz null,
deleted_by uuid null,

version integer not null default 1
```

Additional standard metadata may include:

```sql
workspace_id uuid null,
correlation_id uuid null,
source_system varchar(100) null,
external_reference varchar(150) null
```

---

# 5. Monetary Columns

All monetary values should use:

```sql
numeric(19,4)
```

Recommended columns include:

```sql
currency_code varchar(3) not null,
exchange_rate numeric(19,8) not null default 1,
amount numeric(19,4) not null,
base_currency_amount numeric(19,4) null
```

The Finance Engine remains responsible for official exchange-rate treatment and financial posting.

---

# 6. Status Storage

Operational statuses may be stored as:

```sql
status_code varchar(50) not null
```

Where tenant configurability is required, the status may reference the Reference Data Engine:

```sql
status_reference_id uuid null
```

System-critical lifecycle statuses should remain controlled by the Expenses Management Engine.

---

# 7. Core Entity Relationship Overview

```text
Expense Requisition
│
├── Requisition Lines
│   └── Expense Allocations
│
├── Budget Validation Results
├── Workflow References
├── Supporting Document References
├── Disbursement Records
├── Procurement Conversion
└── Staff Advance
        │
        ├── Advance Disbursements
        ├── Accountabilities
        │   ├── Accountability Lines
        │   ├── Expense Allocations
        │   ├── Refund Requirements
        │   ├── Reimbursement Requirements
        │   └── Recovery Requirements
        │
        └── Settlements
```

Direct expenses, claims, travel expenses, and petty cash transactions may also use the shared allocation, policy, document, workflow, and integration tables.

---

# 8. Expense Requisition Tables

## 8.1 expense_requisitions

Stores the main internal request for funds.

```sql
create table expense_requisitions (
    id uuid primary key default gen_random_uuid(),

    tenant_id uuid not null,
    company_id uuid not null,
    branch_id uuid null,
    workspace_id uuid null,

    requisition_number varchar(100) not null,

    requester_user_id uuid not null,
    requester_employee_id uuid null,
    beneficiary_employee_id uuid null,

    department_id uuid null,
    cost_center_id uuid null,
    project_id uuid null,
    grant_id uuid null,
    program_id uuid null,
    activity_id uuid null,
    funding_source_id uuid null,

    title varchar(255) not null,
    purpose text not null,
    justification text null,

    requisition_type varchar(50) not null,
    disbursement_method varchar(50) null,

    currency_code varchar(3) not null,
    exchange_rate numeric(19,8) not null default 1,

    requested_amount numeric(19,4) not null default 0,
    approved_amount numeric(19,4) not null default 0,
    disbursed_amount numeric(19,4) not null default 0,
    accounted_amount numeric(19,4) not null default 0,
    outstanding_amount numeric(19,4) not null default 0,

    required_date date null,
    accountability_due_date date null,

    budget_control_mode varchar(50) null,
    budget_validation_status varchar(50) null,

    procurement_required boolean not null default false,
    advance_required boolean not null default false,
    accountability_required boolean not null default true,

    status_code varchar(50) not null default 'draft',

    workflow_instance_id uuid null,
    document_number_reference_id uuid null,

    submitted_at timestamptz null,
    approved_at timestamptz null,
    rejected_at timestamptz null,
    cancelled_at timestamptz null,
    closed_at timestamptz null,

    rejection_reason text null,
    cancellation_reason text null,
    closure_notes text null,

    correlation_id uuid null,
    external_reference varchar(150) null,

    created_at timestamptz not null default now(),
    created_by uuid not null,
    updated_at timestamptz not null default now(),
    updated_by uuid null,

    deleted_at timestamptz null,
    deleted_by uuid null,
    version integer not null default 1,

    constraint expense_requisitions_amount_check
        check (
            requested_amount >= 0
            and approved_amount >= 0
            and disbursed_amount >= 0
            and accounted_amount >= 0
            and outstanding_amount >= 0
        )
);
```

### Requisition Types

Suggested values:

```text
cash_requisition
staff_advance
direct_payment
travel_advance
petty_cash_request
procurement_required
activity_funding
project_expense
emergency_expense
```

---

## 8.2 expense_requisition_lines

Stores individual expense items within a requisition.

```sql
create table expense_requisition_lines (
    id uuid primary key default gen_random_uuid(),

    tenant_id uuid not null,
    requisition_id uuid not null,

    line_number integer not null,

    expense_category_id uuid not null,
    expense_type_id uuid null,

    description text not null,

    quantity numeric(19,4) not null default 1,
    unit_of_measure_id uuid null,
    unit_cost numeric(19,4) not null default 0,

    requested_amount numeric(19,4) not null default 0,
    approved_amount numeric(19,4) not null default 0,
    disbursed_amount numeric(19,4) not null default 0,

    tax_code_id uuid null,
    tax_amount numeric(19,4) not null default 0,

    budget_line_id uuid null,
    ledger_account_reference_id uuid null,

    required_date date null,

    approval_status varchar(50) not null default 'pending',

    reviewer_comments text null,

    created_at timestamptz not null default now(),
    created_by uuid not null,
    updated_at timestamptz not null default now(),
    updated_by uuid null,

    deleted_at timestamptz null,
    deleted_by uuid null,
    version integer not null default 1,

    constraint fk_requisition_line_requisition
        foreign key (requisition_id)
        references expense_requisitions(id),

    constraint expense_requisition_line_amount_check
        check (
            quantity > 0
            and unit_cost >= 0
            and requested_amount >= 0
            and approved_amount >= 0
            and disbursed_amount >= 0
        ),

    constraint uq_requisition_line_number
        unique (tenant_id, requisition_id, line_number)
);
```

---

# 9. Direct Expense Tables

## 9.1 direct_expenses

Stores expenses recorded without a prior staff advance.

```sql
create table direct_expenses (
    id uuid primary key default gen_random_uuid(),

    tenant_id uuid not null,
    company_id uuid not null,
    branch_id uuid null,

    expense_number varchar(100) not null,

    employee_id uuid null,
    payee_type varchar(50) not null,
    payee_id uuid null,
    payee_name varchar(255) null,

    supplier_id uuid null,

    expense_date date not null,
    posting_requested_date date null,

    title varchar(255) not null,
    description text null,

    expense_category_id uuid not null,
    expense_type_id uuid null,

    invoice_number varchar(100) null,
    receipt_number varchar(100) null,
    payment_reference varchar(150) null,

    currency_code varchar(3) not null,
    exchange_rate numeric(19,8) not null default 1,

    gross_amount numeric(19,4) not null,
    tax_amount numeric(19,4) not null default 0,
    net_amount numeric(19,4) not null,

    payment_method varchar(50) null,
    personally_paid boolean not null default false,

    budget_validation_status varchar(50) null,
    policy_validation_status varchar(50) null,

    status_code varchar(50) not null default 'draft',

    workflow_instance_id uuid null,
    finance_transaction_reference_id uuid null,

    submitted_at timestamptz null,
    approved_at timestamptz null,
    paid_at timestamptz null,
    completed_at timestamptz null,

    created_at timestamptz not null default now(),
    created_by uuid not null,
    updated_at timestamptz not null default now(),
    updated_by uuid null,

    deleted_at timestamptz null,
    deleted_by uuid null,
    version integer not null default 1,

    constraint direct_expense_amount_check
        check (
            gross_amount >= 0
            and tax_amount >= 0
            and net_amount >= 0
        )
);
```

---

## 9.2 direct_expense_lines

Supports multi-line direct expenses.

```sql
create table direct_expense_lines (
    id uuid primary key default gen_random_uuid(),

    tenant_id uuid not null,
    direct_expense_id uuid not null,

    line_number integer not null,

    expense_category_id uuid not null,
    description text not null,

    quantity numeric(19,4) not null default 1,
    unit_cost numeric(19,4) not null default 0,

    gross_amount numeric(19,4) not null,
    tax_amount numeric(19,4) not null default 0,
    net_amount numeric(19,4) not null,

    tax_code_id uuid null,
    budget_line_id uuid null,

    created_at timestamptz not null default now(),
    created_by uuid not null,
    updated_at timestamptz not null default now(),
    updated_by uuid null,

    deleted_at timestamptz null,
    deleted_by uuid null,
    version integer not null default 1,

    constraint fk_direct_expense_line
        foreign key (direct_expense_id)
        references direct_expenses(id),

    constraint uq_direct_expense_line_number
        unique (tenant_id, direct_expense_id, line_number)
);
```

---

# 10. Staff Advance Tables

## 10.1 staff_advances

Stores approved funds issued to employees before expenditure.

```sql
create table staff_advances (
    id uuid primary key default gen_random_uuid(),

    tenant_id uuid not null,
    company_id uuid not null,
    branch_id uuid null,

    advance_number varchar(100) not null,

    requisition_id uuid null,

    employee_id uuid not null,
    department_id uuid null,
    supervisor_employee_id uuid null,

    advance_type varchar(50) not null,

    purpose text not null,

    currency_code varchar(3) not null,
    exchange_rate numeric(19,8) not null default 1,

    requested_amount numeric(19,4) not null,
    approved_amount numeric(19,4) not null default 0,
    disbursed_amount numeric(19,4) not null default 0,
    accounted_amount numeric(19,4) not null default 0,
    refunded_amount numeric(19,4) not null default 0,
    reimbursed_amount numeric(19,4) not null default 0,
    recovered_amount numeric(19,4) not null default 0,
    outstanding_amount numeric(19,4) not null default 0,

    request_date date not null,
    required_date date null,
    disbursement_date date null,
    accountability_due_date date null,

    is_overdue boolean not null default false,
    overdue_since date null,

    status_code varchar(50) not null default 'requested',

    workflow_instance_id uuid null,
    finance_transaction_reference_id uuid null,

    closed_at timestamptz null,
    closure_reason text null,

    created_at timestamptz not null default now(),
    created_by uuid not null,
    updated_at timestamptz not null default now(),
    updated_by uuid null,

    deleted_at timestamptz null,
    deleted_by uuid null,
    version integer not null default 1,

    constraint fk_staff_advance_requisition
        foreign key (requisition_id)
        references expense_requisitions(id),

    constraint staff_advance_amount_check
        check (
            requested_amount >= 0
            and approved_amount >= 0
            and disbursed_amount >= 0
            and accounted_amount >= 0
            and refunded_amount >= 0
            and reimbursed_amount >= 0
            and recovered_amount >= 0
            and outstanding_amount >= 0
        )
);
```

---

## 10.2 advance_disbursements

Stores each advance disbursement.

```sql
create table advance_disbursements (
    id uuid primary key default gen_random_uuid(),

    tenant_id uuid not null,
    company_id uuid not null,
    branch_id uuid null,

    advance_id uuid not null,

    disbursement_number varchar(100) not null,

    disbursement_date date not null,
    payment_method varchar(50) not null,

    currency_code varchar(3) not null,
    exchange_rate numeric(19,8) not null default 1,
    amount numeric(19,4) not null,

    bank_account_reference_id uuid null,
    cash_account_reference_id uuid null,
    mobile_money_reference varchar(150) null,
    payment_reference varchar(150) null,

    finance_request_id uuid null,
    finance_transaction_reference_id uuid null,

    idempotency_key varchar(150) not null,

    status_code varchar(50) not null default 'pending',

    processed_at timestamptz null,
    failure_reason text null,

    created_at timestamptz not null default now(),
    created_by uuid not null,
    updated_at timestamptz not null default now(),
    updated_by uuid null,

    version integer not null default 1,

    constraint fk_advance_disbursement
        foreign key (advance_id)
        references staff_advances(id),

    constraint advance_disbursement_amount_check
        check (amount > 0),

    constraint uq_advance_disbursement_idempotency
        unique (tenant_id, idempotency_key)
);
```

---

# 11. Accountability Tables

## 11.1 accountabilities

Stores submissions explaining how advance funds were used.

```sql
create table accountabilities (
    id uuid primary key default gen_random_uuid(),

    tenant_id uuid not null,
    company_id uuid not null,
    branch_id uuid null,

    accountability_number varchar(100) not null,

    advance_id uuid not null,
    employee_id uuid not null,

    submission_date date null,
    activity_start_date date null,
    activity_end_date date null,

    currency_code varchar(3) not null,
    exchange_rate numeric(19,8) not null default 1,

    advance_amount numeric(19,4) not null,
    submitted_amount numeric(19,4) not null default 0,
    approved_amount numeric(19,4) not null default 0,
    rejected_amount numeric(19,4) not null default 0,

    refund_required_amount numeric(19,4) not null default 0,
    reimbursement_required_amount numeric(19,4) not null default 0,
    recovery_required_amount numeric(19,4) not null default 0,

    settlement_status varchar(50) not null default 'pending',

    status_code varchar(50) not null default 'draft',

    workflow_instance_id uuid null,

    submitted_at timestamptz null,
    reviewed_at timestamptz null,
    approved_at timestamptz null,
    settled_at timestamptz null,
    closed_at timestamptz null,

    reviewer_comments text null,
    closure_notes text null,

    created_at timestamptz not null default now(),
    created_by uuid not null,
    updated_at timestamptz not null default now(),
    updated_by uuid null,

    deleted_at timestamptz null,
    deleted_by uuid null,
    version integer not null default 1,

    constraint fk_accountability_advance
        foreign key (advance_id)
        references staff_advances(id),

    constraint accountability_amount_check
        check (
            advance_amount >= 0
            and submitted_amount >= 0
            and approved_amount >= 0
            and rejected_amount >= 0
            and refund_required_amount >= 0
            and reimbursement_required_amount >= 0
            and recovery_required_amount >= 0
        )
);
```

---

## 11.2 accountability_lines

Stores individual actual expenditure lines.

```sql
create table accountability_lines (
    id uuid primary key default gen_random_uuid(),

    tenant_id uuid not null,
    accountability_id uuid not null,

    line_number integer not null,

    expense_date date not null,
    expense_category_id uuid not null,

    merchant_name varchar(255) null,
    payee_name varchar(255) null,

    description text not null,

    receipt_number varchar(100) null,
    invoice_number varchar(100) null,
    payment_reference varchar(150) null,

    currency_code varchar(3) not null,
    exchange_rate numeric(19,8) not null default 1,

    submitted_amount numeric(19,4) not null,
    approved_amount numeric(19,4) not null default 0,
    rejected_amount numeric(19,4) not null default 0,

    tax_code_id uuid null,
    tax_amount numeric(19,4) not null default 0,

    policy_validation_status varchar(50) null,
    duplicate_check_status varchar(50) null,

    receipt_required boolean not null default false,
    receipt_verified boolean not null default false,

    review_status varchar(50) not null default 'pending',
    reviewer_comments text null,

    created_at timestamptz not null default now(),
    created_by uuid not null,
    updated_at timestamptz not null default now(),
    updated_by uuid null,

    deleted_at timestamptz null,
    deleted_by uuid null,
    version integer not null default 1,

    constraint fk_accountability_line
        foreign key (accountability_id)
        references accountabilities(id),

    constraint accountability_line_amount_check
        check (
            submitted_amount >= 0
            and approved_amount >= 0
            and rejected_amount >= 0
        ),

    constraint uq_accountability_line_number
        unique (tenant_id, accountability_id, line_number)
);
```

---

# 12. Expense Claim Tables

## 12.1 expense_claims

Stores claims for expenses paid personally by employees.

```sql
create table expense_claims (
    id uuid primary key default gen_random_uuid(),

    tenant_id uuid not null,
    company_id uuid not null,
    branch_id uuid null,

    claim_number varchar(100) not null,

    employee_id uuid not null,
    department_id uuid null,

    title varchar(255) not null,
    description text null,

    claim_date date not null,

    currency_code varchar(3) not null,
    exchange_rate numeric(19,8) not null default 1,

    claimed_amount numeric(19,4) not null default 0,
    approved_amount numeric(19,4) not null default 0,
    rejected_amount numeric(19,4) not null default 0,
    reimbursed_amount numeric(19,4) not null default 0,

    policy_validation_status varchar(50) null,
    budget_validation_status varchar(50) null,

    status_code varchar(50) not null default 'draft',

    workflow_instance_id uuid null,
    reimbursement_id uuid null,

    submitted_at timestamptz null,
    approved_at timestamptz null,
    reimbursed_at timestamptz null,
    closed_at timestamptz null,

    created_at timestamptz not null default now(),
    created_by uuid not null,
    updated_at timestamptz not null default now(),
    updated_by uuid null,

    deleted_at timestamptz null,
    deleted_by uuid null,
    version integer not null default 1
);
```

---

## 12.2 expense_claim_lines

```sql
create table expense_claim_lines (
    id uuid primary key default gen_random_uuid(),

    tenant_id uuid not null,
    claim_id uuid not null,

    line_number integer not null,

    expense_date date not null,
    expense_category_id uuid not null,
    description text not null,

    merchant_name varchar(255) null,
    receipt_number varchar(100) null,

    claimed_amount numeric(19,4) not null,
    approved_amount numeric(19,4) not null default 0,
    rejected_amount numeric(19,4) not null default 0,

    tax_code_id uuid null,
    tax_amount numeric(19,4) not null default 0,

    policy_validation_status varchar(50) null,
    duplicate_check_status varchar(50) null,
    review_status varchar(50) not null default 'pending',

    reviewer_comments text null,

    created_at timestamptz not null default now(),
    created_by uuid not null,
    updated_at timestamptz not null default now(),
    updated_by uuid null,

    deleted_at timestamptz null,
    deleted_by uuid null,
    version integer not null default 1,

    constraint fk_expense_claim_line
        foreign key (claim_id)
        references expense_claims(id),

    constraint uq_expense_claim_line_number
        unique (tenant_id, claim_id, line_number)
);
```

---

# 13. Reimbursement Tables

## 13.1 expense_reimbursements

Stores reimbursement requests arising from claims or accountabilities.

```sql
create table expense_reimbursements (
    id uuid primary key default gen_random_uuid(),

    tenant_id uuid not null,
    company_id uuid not null,
    branch_id uuid null,

    reimbursement_number varchar(100) not null,

    employee_id uuid not null,

    source_type varchar(50) not null,
    source_id uuid not null,

    currency_code varchar(3) not null,
    exchange_rate numeric(19,8) not null default 1,
    amount numeric(19,4) not null,

    payment_method varchar(50) null,

    finance_request_id uuid null,
    finance_transaction_reference_id uuid null,

    idempotency_key varchar(150) not null,

    status_code varchar(50) not null default 'pending',

    requested_at timestamptz not null default now(),
    approved_at timestamptz null,
    paid_at timestamptz null,
    failed_at timestamptz null,

    failure_reason text null,

    created_at timestamptz not null default now(),
    created_by uuid not null,
    updated_at timestamptz not null default now(),
    updated_by uuid null,

    version integer not null default 1,

    constraint reimbursement_amount_check
        check (amount > 0),

    constraint uq_reimbursement_idempotency
        unique (tenant_id, idempotency_key)
);
```

---

# 14. Refund Tables

## 14.1 expense_refund_requirements

Stores employee or beneficiary refund obligations.

```sql
create table expense_refund_requirements (
    id uuid primary key default gen_random_uuid(),

    tenant_id uuid not null,
    company_id uuid not null,
    branch_id uuid null,

    refund_number varchar(100) not null,

    employee_id uuid null,
    beneficiary_id uuid null,

    source_type varchar(50) not null,
    source_id uuid not null,

    currency_code varchar(3) not null,
    required_amount numeric(19,4) not null,
    refunded_amount numeric(19,4) not null default 0,
    outstanding_amount numeric(19,4) not null,

    due_date date null,

    status_code varchar(50) not null default 'outstanding',

    created_at timestamptz not null default now(),
    created_by uuid not null,
    updated_at timestamptz not null default now(),
    updated_by uuid null,

    version integer not null default 1,

    constraint refund_requirement_amount_check
        check (
            required_amount > 0
            and refunded_amount >= 0
            and outstanding_amount >= 0
        )
);
```

---

## 14.2 expense_refund_receipts

Stores confirmed refund payments.

```sql
create table expense_refund_receipts (
    id uuid primary key default gen_random_uuid(),

    tenant_id uuid not null,
    company_id uuid not null,
    branch_id uuid null,

    refund_requirement_id uuid not null,

    receipt_number varchar(100) not null,
    receipt_date date not null,

    payment_method varchar(50) not null,
    payment_reference varchar(150) null,

    currency_code varchar(3) not null,
    amount numeric(19,4) not null,

    finance_transaction_reference_id uuid null,

    idempotency_key varchar(150) not null,

    status_code varchar(50) not null default 'confirmed',

    created_at timestamptz not null default now(),
    created_by uuid not null,

    version integer not null default 1,

    constraint fk_refund_receipt_requirement
        foreign key (refund_requirement_id)
        references expense_refund_requirements(id),

    constraint refund_receipt_amount_check
        check (amount > 0),

    constraint uq_refund_receipt_idempotency
        unique (tenant_id, idempotency_key)
);
```

---

# 15. Recovery Tables

## 15.1 expense_recovery_requests

Stores requests to recover unresolved amounts.

```sql
create table expense_recovery_requests (
    id uuid primary key default gen_random_uuid(),

    tenant_id uuid not null,
    company_id uuid not null,
    branch_id uuid null,

    recovery_number varchar(100) not null,

    employee_id uuid not null,

    source_type varchar(50) not null,
    source_id uuid not null,

    recovery_method varchar(50) not null,

    currency_code varchar(3) not null,
    required_amount numeric(19,4) not null,
    recovered_amount numeric(19,4) not null default 0,
    outstanding_amount numeric(19,4) not null,

    payroll_request_reference_id uuid null,
    finance_transaction_reference_id uuid null,

    status_code varchar(50) not null default 'requested',

    requested_at timestamptz not null default now(),
    accepted_at timestamptz null,
    completed_at timestamptz null,
    rejected_at timestamptz null,

    rejection_reason text null,

    created_at timestamptz not null default now(),
    created_by uuid not null,
    updated_at timestamptz not null default now(),
    updated_by uuid null,

    version integer not null default 1
);
```

### Recovery Methods

```text
payroll_deduction
cash_refund
bank_refund
advance_offset
receivable
write_off_request
```

---

# 16. Settlement Tables

## 16.1 expense_settlements

Stores the final resolution of advances, claims, and accountabilities.

```sql
create table expense_settlements (
    id uuid primary key default gen_random_uuid(),

    tenant_id uuid not null,
    company_id uuid not null,
    branch_id uuid null,

    settlement_number varchar(100) not null,

    source_type varchar(50) not null,
    source_id uuid not null,

    settlement_type varchar(50) not null,

    currency_code varchar(3) not null,

    source_amount numeric(19,4) not null,
    approved_expense_amount numeric(19,4) not null default 0,
    refund_amount numeric(19,4) not null default 0,
    reimbursement_amount numeric(19,4) not null default 0,
    recovery_amount numeric(19,4) not null default 0,
    written_off_amount numeric(19,4) not null default 0,

    status_code varchar(50) not null default 'pending',

    settled_at timestamptz null,
    closed_at timestamptz null,

    notes text null,

    created_at timestamptz not null default now(),
    created_by uuid not null,
    updated_at timestamptz not null default now(),
    updated_by uuid null,

    version integer not null default 1
);
```

---

# 17. Petty Cash Tables

## 17.1 petty_cash_funds

Stores petty cash fund configuration and balances.

```sql
create table petty_cash_funds (
    id uuid primary key default gen_random_uuid(),

    tenant_id uuid not null,
    company_id uuid not null,
    branch_id uuid null,

    fund_code varchar(50) not null,
    fund_name varchar(150) not null,

    custodian_employee_id uuid not null,

    currency_code varchar(3) not null,

    authorized_float numeric(19,4) not null,
    current_cash_balance numeric(19,4) not null default 0,
    unaccounted_voucher_amount numeric(19,4) not null default 0,
    available_balance numeric(19,4) not null default 0,

    minimum_balance numeric(19,4) null,
    maximum_transaction_amount numeric(19,4) null,

    cash_account_reference_id uuid null,

    status_code varchar(50) not null default 'active',

    opened_at timestamptz null,
    suspended_at timestamptz null,
    closed_at timestamptz null,

    created_at timestamptz not null default now(),
    created_by uuid not null,
    updated_at timestamptz not null default now(),
    updated_by uuid null,

    deleted_at timestamptz null,
    deleted_by uuid null,
    version integer not null default 1,

    constraint petty_cash_fund_amount_check
        check (
            authorized_float >= 0
            and current_cash_balance >= 0
            and unaccounted_voucher_amount >= 0
            and available_balance >= 0
        ),

    constraint uq_petty_cash_fund_code
        unique (tenant_id, company_id, fund_code)
);
```

---

## 17.2 petty_cash_transactions

Stores all fund movements.

```sql
create table petty_cash_transactions (
    id uuid primary key default gen_random_uuid(),

    tenant_id uuid not null,
    company_id uuid not null,
    branch_id uuid null,

    fund_id uuid not null,

    transaction_number varchar(100) not null,
    transaction_type varchar(50) not null,

    transaction_date date not null,

    source_type varchar(50) null,
    source_id uuid null,

    employee_id uuid null,
    payee_name varchar(255) null,

    expense_category_id uuid null,
    description text not null,

    currency_code varchar(3) not null,
    amount numeric(19,4) not null,

    opening_balance numeric(19,4) null,
    closing_balance numeric(19,4) null,

    receipt_number varchar(100) null,
    payment_reference varchar(150) null,

    status_code varchar(50) not null default 'completed',

    created_at timestamptz not null default now(),
    created_by uuid not null,

    version integer not null default 1,

    constraint fk_petty_cash_transaction_fund
        foreign key (fund_id)
        references petty_cash_funds(id),

    constraint petty_cash_transaction_amount_check
        check (amount > 0)
);
```

### Transaction Types

```text
opening_float
funding
payment
refund
return
replenishment
adjustment
variance
closure
```

---

## 17.3 petty_cash_vouchers

Stores petty cash payment vouchers.

```sql
create table petty_cash_vouchers (
    id uuid primary key default gen_random_uuid(),

    tenant_id uuid not null,
    company_id uuid not null,
    branch_id uuid null,

    fund_id uuid not null,

    voucher_number varchar(100) not null,

    employee_id uuid null,
    payee_name varchar(255) null,

    expense_category_id uuid not null,

    purpose text not null,

    currency_code varchar(3) not null,
    requested_amount numeric(19,4) not null,
    approved_amount numeric(19,4) not null default 0,
    paid_amount numeric(19,4) not null default 0,

    status_code varchar(50) not null default 'draft',

    workflow_instance_id uuid null,
    transaction_id uuid null,

    requested_at timestamptz null,
    approved_at timestamptz null,
    paid_at timestamptz null,

    created_at timestamptz not null default now(),
    created_by uuid not null,
    updated_at timestamptz not null default now(),
    updated_by uuid null,

    version integer not null default 1,

    constraint fk_petty_cash_voucher_fund
        foreign key (fund_id)
        references petty_cash_funds(id)
);
```

---

## 17.4 petty_cash_replenishments

```sql
create table petty_cash_replenishments (
    id uuid primary key default gen_random_uuid(),

    tenant_id uuid not null,
    company_id uuid not null,
    branch_id uuid null,

    fund_id uuid not null,

    replenishment_number varchar(100) not null,

    request_date date not null,

    currency_code varchar(3) not null,
    requested_amount numeric(19,4) not null,
    approved_amount numeric(19,4) not null default 0,
    funded_amount numeric(19,4) not null default 0,

    finance_request_id uuid null,
    finance_transaction_reference_id uuid null,

    workflow_instance_id uuid null,

    idempotency_key varchar(150) not null,

    status_code varchar(50) not null default 'draft',

    approved_at timestamptz null,
    funded_at timestamptz null,

    created_at timestamptz not null default now(),
    created_by uuid not null,
    updated_at timestamptz not null default now(),
    updated_by uuid null,

    version integer not null default 1,

    constraint fk_petty_cash_replenishment_fund
        foreign key (fund_id)
        references petty_cash_funds(id),

    constraint uq_petty_cash_replenishment_idempotency
        unique (tenant_id, idempotency_key)
);
```

---

## 17.5 petty_cash_reconciliations

```sql
create table petty_cash_reconciliations (
    id uuid primary key default gen_random_uuid(),

    tenant_id uuid not null,
    company_id uuid not null,
    branch_id uuid null,

    fund_id uuid not null,

    reconciliation_number varchar(100) not null,
    reconciliation_date date not null,

    currency_code varchar(3) not null,

    expected_cash numeric(19,4) not null,
    actual_cash numeric(19,4) not null,
    voucher_amount numeric(19,4) not null default 0,
    variance_amount numeric(19,4) not null default 0,

    variance_type varchar(50) null,
    variance_reason text null,

    counted_by uuid not null,
    reviewed_by uuid null,
    approved_by uuid null,

    workflow_instance_id uuid null,

    status_code varchar(50) not null default 'draft',

    submitted_at timestamptz null,
    approved_at timestamptz null,

    created_at timestamptz not null default now(),
    created_by uuid not null,
    updated_at timestamptz not null default now(),
    updated_by uuid null,

    version integer not null default 1,

    constraint fk_petty_cash_reconciliation_fund
        foreign key (fund_id)
        references petty_cash_funds(id)
);
```

---

# 18. Travel Expense Tables

## 18.1 travel_expense_requests

```sql
create table travel_expense_requests (
    id uuid primary key default gen_random_uuid(),

    tenant_id uuid not null,
    company_id uuid not null,
    branch_id uuid null,

    travel_request_number varchar(100) not null,

    employee_id uuid not null,
    department_id uuid null,

    travel_purpose text not null,
    destination_country_code varchar(3) null,
    destination_location varchar(255) not null,

    departure_date date not null,
    return_date date not null,

    travel_type varchar(50) not null,

    currency_code varchar(3) not null,

    estimated_transport_amount numeric(19,4) not null default 0,
    estimated_accommodation_amount numeric(19,4) not null default 0,
    estimated_per_diem_amount numeric(19,4) not null default 0,
    estimated_other_amount numeric(19,4) not null default 0,
    estimated_total_amount numeric(19,4) not null default 0,

    advance_required boolean not null default false,
    advance_id uuid null,

    status_code varchar(50) not null default 'draft',

    workflow_instance_id uuid null,

    submitted_at timestamptz null,
    approved_at timestamptz null,
    completed_at timestamptz null,

    created_at timestamptz not null default now(),
    created_by uuid not null,
    updated_at timestamptz not null default now(),
    updated_by uuid null,

    deleted_at timestamptz null,
    deleted_by uuid null,
    version integer not null default 1
);
```

---

## 18.2 travel_expense_lines

```sql
create table travel_expense_lines (
    id uuid primary key default gen_random_uuid(),

    tenant_id uuid not null,
    travel_request_id uuid not null,

    line_number integer not null,

    expense_type varchar(50) not null,
    description text null,

    quantity numeric(19,4) not null default 1,
    rate numeric(19,4) not null default 0,
    amount numeric(19,4) not null default 0,

    currency_code varchar(3) not null,

    start_date date null,
    end_date date null,

    created_at timestamptz not null default now(),
    created_by uuid not null,
    updated_at timestamptz not null default now(),
    updated_by uuid null,

    version integer not null default 1,

    constraint fk_travel_expense_line
        foreign key (travel_request_id)
        references travel_expense_requests(id),

    constraint uq_travel_expense_line_number
        unique (tenant_id, travel_request_id, line_number)
);
```

---

# 19. Per Diem Tables

## 19.1 per_diem_rates

```sql
create table per_diem_rates (
    id uuid primary key default gen_random_uuid(),

    tenant_id uuid not null,
    company_id uuid null,

    rate_code varchar(50) not null,
    rate_name varchar(150) not null,

    employee_grade_id uuid null,
    travel_grade_id uuid null,

    country_code varchar(3) null,
    location_category varchar(50) null,

    currency_code varchar(3) not null,

    daily_rate numeric(19,4) not null,
    accommodation_component numeric(19,4) not null default 0,
    meal_component numeric(19,4) not null default 0,
    incidental_component numeric(19,4) not null default 0,

    effective_from date not null,
    effective_to date null,

    status_code varchar(50) not null default 'active',

    created_at timestamptz not null default now(),
    created_by uuid not null,
    updated_at timestamptz not null default now(),
    updated_by uuid null,

    deleted_at timestamptz null,
    deleted_by uuid null,
    version integer not null default 1
);
```

---

## 19.2 per_diem_calculations

```sql
create table per_diem_calculations (
    id uuid primary key default gen_random_uuid(),

    tenant_id uuid not null,

    travel_request_id uuid not null,
    employee_id uuid not null,
    rate_id uuid not null,

    number_of_days numeric(10,2) not null,
    daily_rate numeric(19,4) not null,

    gross_amount numeric(19,4) not null,
    deductions_amount numeric(19,4) not null default 0,
    payable_amount numeric(19,4) not null,

    calculation_details jsonb null,

    created_at timestamptz not null default now(),
    created_by uuid not null,

    version integer not null default 1
);
```

---

# 20. Mileage Claim Tables

## 20.1 mileage_rates

```sql
create table mileage_rates (
    id uuid primary key default gen_random_uuid(),

    tenant_id uuid not null,
    company_id uuid null,

    vehicle_type varchar(50) not null,
    distance_unit varchar(20) not null,

    currency_code varchar(3) not null,
    rate_per_unit numeric(19,4) not null,

    effective_from date not null,
    effective_to date null,

    status_code varchar(50) not null default 'active',

    created_at timestamptz not null default now(),
    created_by uuid not null,
    updated_at timestamptz not null default now(),
    updated_by uuid null,

    version integer not null default 1
);
```

---

## 20.2 mileage_claims

```sql
create table mileage_claims (
    id uuid primary key default gen_random_uuid(),

    tenant_id uuid not null,
    company_id uuid not null,
    branch_id uuid null,

    claim_number varchar(100) not null,

    employee_id uuid not null,
    travel_request_id uuid null,

    journey_date date not null,
    origin varchar(255) not null,
    destination varchar(255) not null,

    distance numeric(19,4) not null,
    distance_unit varchar(20) not null,

    vehicle_type varchar(50) not null,
    rate_id uuid not null,

    rate_per_unit numeric(19,4) not null,
    claimed_amount numeric(19,4) not null,
    approved_amount numeric(19,4) not null default 0,

    purpose text not null,

    status_code varchar(50) not null default 'draft',

    workflow_instance_id uuid null,

    created_at timestamptz not null default now(),
    created_by uuid not null,
    updated_at timestamptz not null default now(),
    updated_by uuid null,

    version integer not null default 1
);
```

---

# 21. Expense Allocation Tables

## 21.1 expense_allocations

Provides a shared allocation model across multiple expense entities.

```sql
create table expense_allocations (
    id uuid primary key default gen_random_uuid(),

    tenant_id uuid not null,
    company_id uuid not null,
    branch_id uuid null,

    source_type varchar(50) not null,
    source_id uuid not null,
    source_line_id uuid null,

    allocation_line_number integer not null,

    department_id uuid null,
    cost_center_id uuid null,
    project_id uuid null,
    grant_id uuid null,
    program_id uuid null,
    activity_id uuid null,
    funding_source_id uuid null,
    asset_id uuid null,
    customer_engagement_id uuid null,

    custom_dimensions jsonb null,

    allocation_percentage numeric(9,6) null,
    allocation_amount numeric(19,4) not null,

    currency_code varchar(3) not null,

    created_at timestamptz not null default now(),
    created_by uuid not null,
    updated_at timestamptz not null default now(),
    updated_by uuid null,

    version integer not null default 1,

    constraint expense_allocation_amount_check
        check (
            allocation_amount >= 0
            and (
                allocation_percentage is null
                or allocation_percentage between 0 and 100
            )
        )
);
```

Allocation totals must be validated using database functions or server-side application logic.

---

# 22. Expense Policy Tables

## 22.1 expense_policies

```sql
create table expense_policies (
    id uuid primary key default gen_random_uuid(),

    tenant_id uuid not null,
    company_id uuid null,
    branch_id uuid null,

    policy_code varchar(50) not null,
    policy_name varchar(150) not null,

    description text null,

    policy_scope varchar(50) not null,
    expense_category_id uuid null,
    employee_grade_id uuid null,
    department_id uuid null,
    project_id uuid null,

    currency_code varchar(3) null,

    maximum_amount numeric(19,4) null,
    daily_limit numeric(19,4) null,
    monthly_limit numeric(19,4) null,

    receipt_required boolean not null default false,
    receipt_threshold numeric(19,4) null,

    approval_required boolean not null default true,
    override_allowed boolean not null default false,

    accountability_due_days integer null,
    maximum_outstanding_advances integer null,
    maximum_advance_amount numeric(19,4) null,

    rules jsonb null,

    priority integer not null default 100,

    effective_from date not null,
    effective_to date null,

    status_code varchar(50) not null default 'active',

    created_at timestamptz not null default now(),
    created_by uuid not null,
    updated_at timestamptz not null default now(),
    updated_by uuid null,

    deleted_at timestamptz null,
    deleted_by uuid null,
    version integer not null default 1,

    constraint uq_expense_policy_code
        unique (tenant_id, policy_code)
);
```

---

## 22.2 expense_policy_evaluations

Stores policy evaluation results for auditability.

```sql
create table expense_policy_evaluations (
    id uuid primary key default gen_random_uuid(),

    tenant_id uuid not null,

    source_type varchar(50) not null,
    source_id uuid not null,
    source_line_id uuid null,

    policy_id uuid not null,

    evaluation_stage varchar(50) not null,
    result_code varchar(50) not null,

    message text null,
    details jsonb null,

    evaluated_at timestamptz not null default now(),
    evaluated_by uuid null,

    override_required boolean not null default false,
    override_granted boolean not null default false,
    override_reason text null,
    override_by uuid null,
    override_at timestamptz null,

    version integer not null default 1
);
```

---

# 23. Budget Validation Tables

## 23.1 expense_budget_validations

```sql
create table expense_budget_validations (
    id uuid primary key default gen_random_uuid(),

    tenant_id uuid not null,
    company_id uuid not null,

    source_type varchar(50) not null,
    source_id uuid not null,
    source_line_id uuid null,

    budget_reference_id uuid null,
    budget_line_id uuid null,

    requested_amount numeric(19,4) not null,
    available_amount numeric(19,4) null,
    reserved_amount numeric(19,4) not null default 0,

    currency_code varchar(3) not null,

    control_mode varchar(50) not null,
    result_code varchar(50) not null,

    reservation_reference_id uuid null,

    override_required boolean not null default false,
    override_workflow_instance_id uuid null,

    validated_at timestamptz not null default now(),
    released_at timestamptz null,

    details jsonb null,

    created_at timestamptz not null default now(),
    created_by uuid not null,

    version integer not null default 1
);
```

---

# 24. Document Reference Tables

## 24.1 expense_document_references

Stores references to documents owned by the Document Management Engine.

```sql
create table expense_document_references (
    id uuid primary key default gen_random_uuid(),

    tenant_id uuid not null,

    source_type varchar(50) not null,
    source_id uuid not null,
    source_line_id uuid null,

    document_id uuid not null,
    document_type varchar(50) not null,

    is_required boolean not null default false,
    is_verified boolean not null default false,

    verification_status varchar(50) null,
    verified_by uuid null,
    verified_at timestamptz null,

    created_at timestamptz not null default now(),
    created_by uuid not null,

    version integer not null default 1,

    constraint uq_expense_document_reference
        unique (tenant_id, source_type, source_id, document_id)
);
```

---

# 25. Workflow Reference Tables

## 25.1 expense_workflow_references

```sql
create table expense_workflow_references (
    id uuid primary key default gen_random_uuid(),

    tenant_id uuid not null,

    source_type varchar(50) not null,
    source_id uuid not null,

    workflow_definition_id uuid null,
    workflow_instance_id uuid not null,

    workflow_status varchar(50) not null,

    current_step_id uuid null,
    current_step_name varchar(150) null,

    started_at timestamptz not null default now(),
    completed_at timestamptz null,

    created_at timestamptz not null default now(),
    created_by uuid not null,

    version integer not null default 1,

    constraint uq_expense_workflow_instance
        unique (tenant_id, workflow_instance_id)
);
```

---

# 26. Procurement Conversion Tables

## 26.1 expense_procurement_conversions

```sql
create table expense_procurement_conversions (
    id uuid primary key default gen_random_uuid(),

    tenant_id uuid not null,
    company_id uuid not null,
    branch_id uuid null,

    requisition_id uuid not null,

    procurement_request_id uuid null,
    procurement_request_number varchar(100) null,

    conversion_status varchar(50) not null default 'requested',

    requested_at timestamptz not null default now(),
    converted_at timestamptz null,
    failed_at timestamptz null,

    failure_reason text null,

    idempotency_key varchar(150) not null,

    created_at timestamptz not null default now(),
    created_by uuid not null,
    updated_at timestamptz not null default now(),
    updated_by uuid null,

    version integer not null default 1,

    constraint fk_expense_procurement_requisition
        foreign key (requisition_id)
        references expense_requisitions(id),

    constraint uq_procurement_conversion_requisition
        unique (tenant_id, requisition_id),

    constraint uq_procurement_conversion_idempotency
        unique (tenant_id, idempotency_key)
);
```

---

# 27. Integration Reference Tables

## 27.1 expense_integration_references

```sql
create table expense_integration_references (
    id uuid primary key default gen_random_uuid(),

    tenant_id uuid not null,

    source_type varchar(50) not null,
    source_id uuid not null,

    integration_type varchar(50) not null,
    target_system varchar(100) not null,

    request_reference varchar(150) null,
    response_reference varchar(150) null,

    idempotency_key varchar(150) null,

    integration_status varchar(50) not null default 'pending',

    request_payload jsonb null,
    response_payload jsonb null,

    requested_at timestamptz null,
    completed_at timestamptz null,
    failed_at timestamptz null,

    failure_reason text null,
    retry_count integer not null default 0,
    next_retry_at timestamptz null,

    created_at timestamptz not null default now(),
    created_by uuid not null,
    updated_at timestamptz not null default now(),
    updated_by uuid null,

    version integer not null default 1
);
```

---

# 28. Duplicate Expense Detection Tables

## 28.1 expense_duplicate_checks

```sql
create table expense_duplicate_checks (
    id uuid primary key default gen_random_uuid(),

    tenant_id uuid not null,

    source_type varchar(50) not null,
    source_id uuid not null,
    source_line_id uuid null,

    suspected_duplicate_type varchar(50) not null,
    suspected_duplicate_id uuid not null,

    similarity_score numeric(9,6) null,

    matched_fields jsonb null,

    result_code varchar(50) not null default 'suspected',

    reviewed_by uuid null,
    reviewed_at timestamptz null,
    resolution_notes text null,

    created_at timestamptz not null default now(),
    created_by uuid not null,

    version integer not null default 1
);
```

---

# 29. Expense Exception Tables

## 29.1 expense_exceptions

```sql
create table expense_exceptions (
    id uuid primary key default gen_random_uuid(),

    tenant_id uuid not null,
    company_id uuid null,
    branch_id uuid null,

    source_type varchar(50) not null,
    source_id uuid not null,
    source_line_id uuid null,

    exception_type varchar(50) not null,
    severity varchar(50) not null,

    title varchar(255) not null,
    description text null,

    resolution_status varchar(50) not null default 'open',

    workflow_instance_id uuid null,

    resolved_by uuid null,
    resolved_at timestamptz null,
    resolution_notes text null,

    created_at timestamptz not null default now(),
    created_by uuid not null,
    updated_at timestamptz not null default now(),
    updated_by uuid null,

    version integer not null default 1
);
```

---

# 30. Outbox Event Table

## 30.1 expense_outbox_events

```sql
create table expense_outbox_events (
    id uuid primary key default gen_random_uuid(),

    tenant_id uuid not null,
    company_id uuid null,
    branch_id uuid null,

    event_type varchar(150) not null,
    event_version integer not null default 1,

    aggregate_type varchar(100) not null,
    aggregate_id uuid not null,

    correlation_id uuid null,
    causation_id uuid null,
    actor_id uuid null,

    payload jsonb not null,

    occurred_at timestamptz not null default now(),

    publication_status varchar(50) not null default 'pending',
    publication_attempts integer not null default 0,
    published_at timestamptz null,
    next_attempt_at timestamptz null,
    last_error text null,

    created_at timestamptz not null default now()
);
```

---

# 31. Scheduled Job Tables

## 31.1 expense_reminder_jobs

```sql
create table expense_reminder_jobs (
    id uuid primary key default gen_random_uuid(),

    tenant_id uuid not null,

    reminder_type varchar(50) not null,

    source_type varchar(50) not null,
    source_id uuid not null,

    scheduled_for timestamptz not null,

    status_code varchar(50) not null default 'pending',

    attempt_count integer not null default 0,
    last_attempt_at timestamptz null,
    completed_at timestamptz null,

    failure_reason text null,

    created_at timestamptz not null default now()
);
```

---

# 32. Unique Constraints

Recommended unique constraints include:

```text
tenant_id + requisition_number
tenant_id + expense_number
tenant_id + advance_number
tenant_id + accountability_number
tenant_id + claim_number
tenant_id + reimbursement_number
tenant_id + refund_number
tenant_id + recovery_number
tenant_id + settlement_number
tenant_id + transaction_number
tenant_id + voucher_number
tenant_id + travel_request_number
tenant_id + workflow_instance_id
tenant_id + idempotency_key
```

Document numbers should be unique within the tenant or company scope defined by the Document Numbering Engine.

---

# 33. Foreign Key Strategy

Foreign keys should be enforced within the Expenses Management Engine domain.

Examples:

```text
Requisition → Requisition Lines
Advance → Disbursements
Advance → Accountabilities
Accountability → Accountability Lines
Petty Cash Fund → Transactions
Petty Cash Fund → Reconciliations
Claim → Claim Lines
Refund Requirement → Refund Receipts
```

External engine references may not always use physical foreign keys.

Examples include:

- employee_id
- workflow_instance_id
- document_id
- finance_transaction_reference_id
- procurement_request_id
- payroll_request_reference_id
- budget_reference_id

These references must be validated through application services and integration contracts.

---

# 34. Indexing Strategy

## 34.1 Tenant Indexes

Every major table should include:

```sql
create index idx_expense_requisitions_tenant
on expense_requisitions (tenant_id);
```

---

## 34.2 Tenant and Company Indexes

```sql
create index idx_expense_requisitions_tenant_company
on expense_requisitions (tenant_id, company_id);
```

---

## 34.3 Status Indexes

```sql
create index idx_expense_requisitions_status
on expense_requisitions (tenant_id, status_code);
```

---

## 34.4 Date Indexes

```sql
create index idx_staff_advances_due_date
on staff_advances (
    tenant_id,
    accountability_due_date
)
where status_code not in ('closed', 'cancelled');
```

---

## 34.5 Employee Indexes

```sql
create index idx_staff_advances_employee
on staff_advances (
    tenant_id,
    employee_id,
    status_code
);
```

---

## 34.6 Search Indexes

Potential searchable fields include:

- Requisition Number
- Advance Number
- Accountability Number
- Claim Number
- Title
- Purpose
- Payee Name
- Merchant Name
- Receipt Number
- Invoice Number
- Payment Reference

PostgreSQL full-text search or the Search & Indexing Engine may be used.

---

## 34.7 Partial Indexes

Examples:

```sql
create index idx_outstanding_advances
on staff_advances (
    tenant_id,
    employee_id,
    accountability_due_date
)
where status_code in (
    'disbursed',
    'outstanding',
    'accountability_due',
    'overdue'
);
```

```sql
create index idx_pending_outbox_events
on expense_outbox_events (
    publication_status,
    next_attempt_at
)
where publication_status in ('pending', 'failed');
```

---

# 35. Row Level Security

RLS must be enabled on all Expenses Management Engine tables.

Example:

```sql
alter table expense_requisitions enable row level security;
```

Basic tenant policy:

```sql
create policy expense_requisitions_tenant_select
on expense_requisitions
for select
using (
    tenant_id = platform_current_tenant_id()
);
```

Insert policy:

```sql
create policy expense_requisitions_tenant_insert
on expense_requisitions
for insert
with check (
    tenant_id = platform_current_tenant_id()
);
```

Update policy:

```sql
create policy expense_requisitions_tenant_update
on expense_requisitions
for update
using (
    tenant_id = platform_current_tenant_id()
)
with check (
    tenant_id = platform_current_tenant_id()
);
```

---

# 36. Company and Branch Security

RLS should also validate:

- Active company access
- Active branch access
- Employee self-service access
- Department access
- Approver assignment
- Finance team access
- Petty cash custodian access
- Auditor read-only access

Example logic:

```text
Tenant Match
AND
(
    User Has Company-Wide Access
    OR User Has Branch Access
    OR User Owns the Record
    OR User Is Assigned Approver
    OR User Has Audit Permission
)
```

Complex authorization should use secure database helper functions integrated with the Authorization Engine.

---

# 37. Employee Self-Service Policies

Employees may be allowed to view:

- Their own requisitions
- Their own advances
- Their own accountabilities
- Their own claims
- Their own reimbursements
- Their own refund and recovery obligations

Employees must not automatically view:

- Other employees’ claims
- Other employees’ advances
- Confidential finance comments
- Internal investigation notes
- Petty cash fund balances unless authorized

---

# 38. Optimistic Concurrency

Every mutable aggregate must include:

```sql
version integer not null default 1
```

Updates should follow:

```sql
update expense_requisitions
set
    status_code = 'submitted',
    version = version + 1,
    updated_at = now()
where id = :id
  and tenant_id = :tenant_id
  and version = :expected_version;
```

If no record is updated, the API must return a concurrency conflict.

---

# 39. Database Functions

Recommended server-side functions include:

```text
calculate_requisition_totals
calculate_advance_balance
calculate_accountability_totals
determine_accountability_settlement
calculate_petty_cash_balance
validate_expense_allocations
validate_requisition_submission
validate_advance_disbursement
validate_accountability_submission
mark_overdue_advances
release_expired_budget_reservations
```

These functions must remain tenant-aware.

---

# 40. Database Triggers

Triggers should be used cautiously.

Recommended trigger uses include:

- Automatically updating `updated_at`
- Incrementing version numbers where appropriate
- Creating immutable audit metadata
- Preventing restricted physical deletion
- Validating tenant consistency between parent and child records
- Preventing updates to closed records
- Enforcing outbox creation through controlled procedures

Complex business workflows should remain in domain and application services rather than database triggers.

---

# 41. Parent-Child Tenant Validation

A child record must always match its parent tenant.

Example:

```text
expense_requisition_lines.tenant_id
must equal
expense_requisitions.tenant_id
```

This may be enforced through:

- Composite foreign keys
- Database trigger validation
- Controlled repository methods

Cross-tenant parent-child references must be blocked.

---

# 42. Allocation Validation

For each source line:

```text
SUM(allocation_amount) = source_line_amount
```

Where percentages are used:

```text
SUM(allocation_percentage) = 100
```

Allowable rounding differences must be tenant-configurable or currency-aware.

---

# 43. Advance Balance Calculation

The outstanding advance should follow:

```text
Outstanding Amount =
Disbursed Amount
- Approved Accounted Amount
- Refunded Amount
- Recovered Amount
```

Reimbursements do not reduce the original disbursed amount but may affect final settlement.

Balances should be recalculated through trusted domain services.

---

# 44. Petty Cash Balance Calculation

Petty cash should follow:

```text
Current Cash Balance =
Opening Float
+ Funding
+ Refunds
+ Replenishments
- Payments
- Approved Shortages
```

Derived balances may be stored for performance but must always be traceable to fund transactions.

---

# 45. Closed Record Protection

Records with final statuses such as:

```text
closed
completed
settled
paid
reconciled
cancelled_after_approval
```

must not be freely edited.

Corrections must use:

- Reversal
- Reopening Workflow
- Adjustment Transaction
- Credit or Recovery Entry
- Authorized Correction Process

---

# 46. Soft Deletion

Soft deletion may be allowed only for records such as:

- Draft requisitions
- Draft claims
- Draft travel requests
- Inactive policies
- Unused configuration records

Approved or financially relevant records must not be deleted.

The `deleted_at` column must not be used to hide finalized financial history.

---

# 47. Audit Requirements

The Platform Activity & Audit Engine must capture:

- Entity Type
- Entity ID
- Action
- Previous Values
- New Values
- Actor
- Tenant
- Company
- Branch
- Timestamp
- Correlation ID
- Workflow Reference
- Override Reason
- Device or Session Metadata

Enhanced audit is required for:

- Requisition approval
- Disbursement
- Accountability approval
- Accountability line rejection
- Refund confirmation
- Reimbursement approval
- Payroll recovery request
- Petty cash reconciliation
- Cash variance
- Policy override
- Budget override
- Record reopening

---

# 48. Data Retention

Retention policies may vary by tenant and jurisdiction.

Recommended minimum retention categories include:

```text
Financial Expense Records
Advance Records
Accountability Records
Petty Cash Records
Payment References
Supporting Document References
Workflow History
Audit Events
Integration Events
```

Retention configuration must not allow deletion that violates accounting, legal, tax, grant, donor, or audit requirements.

---

# 49. Data Archiving

Large-volume tenants may archive:

- Closed expenses
- Settled advances
- Completed accountabilities
- Old petty cash transactions
- Published outbox events
- Completed integration logs
- Expired reminders

Archived records must remain:

- Searchable where authorized
- Auditable
- Tenant-isolated
- Restorable
- Reportable

---

# 50. Reporting Views

Recommended reporting views include:

```text
vw_expense_requisition_summary
vw_expense_requisition_details
vw_direct_expense_summary
vw_staff_advance_balances
vw_outstanding_advances
vw_overdue_accountabilities
vw_accountability_settlements
vw_employee_expense_claims
vw_expense_reimbursements
vw_expense_refunds
vw_expense_recoveries
vw_petty_cash_balances
vw_petty_cash_movements
vw_petty_cash_reconciliations
vw_department_expenses
vw_project_expenses
vw_budget_utilization
vw_expense_policy_exceptions
vw_expense_approval_lead_time
```

Views must respect RLS or be exposed through secure reporting services.

---

# 51. Materialized Views

For high-volume reporting, materialized views may support:

- Monthly Expenses by Department
- Monthly Expenses by Category
- Advance Aging
- Outstanding Accountabilities
- Petty Cash Movement Summary
- Expense Approval Lead Time
- Project Expense Summary
- Funding Source Utilization

Refresh operations must be tenant-aware.

---

# 52. Scheduled Database Operations

Scheduled jobs may process:

- Overdue advance detection
- Accountability reminders
- Refund reminders
- Expired budget reservations
- Petty cash minimum-balance alerts
- Policy expiry
- Rate activation
- Failed integration retries
- Outbox event publication
- Reporting aggregation

Jobs must use tenant context and idempotent execution.

---

# 53. Data Migration Strategy

Migration into the Expenses Management Engine may include:

- Existing requisitions
- Outstanding advances
- Pending accountabilities
- Petty cash balances
- Expense policies
- Employee claim balances
- Refund obligations
- Recovery obligations

Migration must preserve:

- Original document numbers
- Original transaction dates
- Source system references
- Historical status
- Opening balances
- Employee references
- Supporting document links
- Audit source metadata

---

# 54. Seed Data

Recommended seed reference values include:

## Requisition Statuses

```text
draft
submitted
under_review
returned_for_revision
partially_approved
approved
rejected
cancelled
pending_disbursement
partially_disbursed
fully_disbursed
pending_accountability
accountability_submitted
closed
overdue
```

## Advance Statuses

```text
requested
submitted
approved
rejected
pending_disbursement
partially_disbursed
disbursed
outstanding
accountability_due
accountability_submitted
settlement_pending
closed
overdue
```

## Accountability Statuses

```text
draft
submitted
under_review
returned
partially_approved
approved
rejected
settlement_pending
settled
closed
```

## Settlement Types

```text
fully_accounted
refund_required
reimbursement_required
recovery_required
partial_settlement
advance_offset
carry_forward
write_off
disputed
```

## Policy Results

```text
passed
warning
blocked
override_required
document_required
additional_approval_required
```

---

# 55. Database Security Rules

The database must enforce the following rules:

- A requisition cannot contain lines from another tenant.
- An advance cannot reference another tenant’s requisition.
- An accountability cannot reference another tenant’s advance.
- A petty cash transaction cannot use another tenant’s fund.
- A closed record cannot be modified without an authorized reopening process.
- An approved disbursement cannot be physically deleted.
- A refund receipt cannot exceed the outstanding refund amount.
- A reimbursement cannot exceed the approved reimbursement amount.
- Allocation totals must equal their source amount.
- An employee cannot access another employee’s records without permission.
- Finance references must be immutable after confirmation.
- Idempotency keys must prevent duplicate financial actions.
- Client-side totals must never be accepted without server recalculation.

---

# 56. Database Acceptance Requirements

The database implementation is accepted when:

- All major entities include tenant isolation.
- RLS is enabled on every business table.
- Parent-child tenant consistency is enforced.
- Document numbers are uniquely constrained.
- Financial integration operations are idempotent.
- Approved records cannot be physically deleted.
- Optimistic concurrency is enforced.
- Monetary calculations use fixed-precision values.
- Outbox events are committed transactionally.
- Accountabilities correctly resolve refunds, reimbursements, and recoveries.
- Petty cash balances reconcile to transaction history.
- Allocation totals are validated.
- Outstanding advances are accurately calculated.
- Reporting views remain tenant-safe.
- Audit metadata is complete.
- Finance, Workflow, HR, Procurement, Payroll, and Document references remain clearly separated.

---

# 57. Database Summary

The Expenses Management Engine database provides a comprehensive and enterprise-grade data foundation for:

- Requisitions
- Direct Expenses
- Staff Advances
- Accountabilities
- Claims
- Reimbursements
- Refunds
- Recoveries
- Settlements
- Petty Cash
- Travel Expenses
- Per Diem
- Mileage
- Policies
- Budget Controls
- Allocations
- Integrations

The design supports multi-tenant, multi-company, multi-branch, multi-currency, project-based, grant-based, departmental, and employee-based expense management.

It preserves clear ownership boundaries while integrating securely with the Finance Engine, Human Resources Engine, Payroll Engine, Procurement Engine, Workflow Engine, Document Management Engine, Reporting Engine, Authorization Engine, Audit Engine, and Platform Event Bus.

---

# 58. Next Document

The next document is:

**WORKFLOWS.md**

It will define:

- Requisition Workflows
- Approval Workflows
- Disbursement Workflows
- Direct Expense Workflows
- Advance Workflows
- Accountability Workflows
- Refund Workflows
- Reimbursement Workflows
- Recovery Workflows
- Petty Cash Workflows
- Travel Expense Workflows
- Budget Override Workflows
- Policy Exception Workflows
- Procurement Conversion Workflows
- Finance and Payroll Integration Workflows
