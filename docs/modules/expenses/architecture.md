# ARCHITECTURE.md

# Expenses Management Engine Architecture

---

# 1. Overview

The Expenses Management Engine architecture defines how Business Suite manages internal organizational expenditure from request initiation through approval, disbursement, utilization, accountability, reimbursement, recovery, reconciliation, and closure.

The engine provides a central operational layer for:

- Expense Requisitions
- Direct Expenses
- Staff Advances
- Accountabilities
- Expense Claims
- Employee Reimbursements
- Petty Cash
- Travel Expenses
- Per Diem
- Mileage Claims
- Expense Allocations
- Budget Validation
- Expense Policy Enforcement

The Expenses Management Engine does not perform accounting, maintain employee records, manage procurement, or implement approval logic independently.

It coordinates these processes by consuming services from existing Business Suite engines and publishing domain events to the Platform Event Bus.

---

# 2. Architectural Role

The Expenses Management Engine acts as the authoritative operational owner of internal expenditure processes.

It connects:

- Employees
- Departments
- Approvers
- Finance Teams
- Cashiers
- Petty Cash Custodians
- Projects
- Cost Centers
- Budgets
- Supporting Documents
- Bank and Cash Disbursements
- Payroll Recoveries

The engine manages the expense lifecycle while delegating specialized responsibilities to their respective owners.

```text
Requester / Employee / Payee
             │
             ▼
Expenses Management Engine
             │
             ├────────► Platform Core
             ├────────► Authorization Engine
             ├────────► Workflow Engine
             ├────────► Finance Engine
             ├────────► Human Resources Engine
             ├────────► Procurement Engine
             ├────────► Payroll Engine
             ├────────► Document Numbering Engine
             ├────────► Document Management Engine
             ├────────► Notification Engine
             ├────────► Reporting Engine
             ├────────► Search & Indexing Engine
             ├────────► Activity & Audit Engine
             └────────► Platform Event Bus
```

---

# 3. Architectural Principles

The Expenses Management Engine follows these principles:

- Expenses Management is the authoritative owner of internal expenditure workflows.
- Finance remains the authoritative owner of accounting and financial posting.
- Human Resources remains the authoritative owner of employee information.
- Procurement remains the authoritative owner of formal supplier purchasing.
- Payroll remains the authoritative owner of salary deductions and payroll processing.
- Approval logic must be delegated to the Workflow Engine.
- Documents must be managed through the Document Management Engine.
- All financial-impacting actions must publish events.
- Every transaction must be traceable by tenant, company, branch, department, requester, and funding dimension.
- Completed expense records must be preserved through reversals and status transitions rather than physical deletion.
- Budget controls and expense policies must be configurable per tenant.

---

# 4. High-Level Architecture

```text
┌──────────────────────────────────────────────────────────────┐
│                    Expenses Frontend                         │
│ React, TypeScript, Tailwind, shadcn/ui, RHF, Zod             │
└──────────────────────────────┬───────────────────────────────┘
                               │
                               ▼
┌──────────────────────────────────────────────────────────────┐
│                  Application Service Layer                   │
│ Requisitions, Advances, Claims, Accountability, Petty Cash  │
└──────────────────────────────┬───────────────────────────────┘
                               │
                               ▼
┌──────────────────────────────────────────────────────────────┐
│                       Domain Layer                           │
│ Policies, Lifecycle Rules, Budget Rules, Allocations         │
└──────────────────────────────┬───────────────────────────────┘
                               │
                               ▼
┌──────────────────────────────────────────────────────────────┐
│                   Infrastructure Layer                       │
│ Repositories, Supabase, RLS, Edge Functions, Realtime       │
└──────────────────────────────┬───────────────────────────────┘
                               │
                               ▼
┌──────────────────────────────────────────────────────────────┐
│             Platform Engines and Business Engines            │
│ Finance, HR, Workflow, Documents, Audit, Reporting, Payroll │
└──────────────────────────────────────────────────────────────┘
```

---

# 5. Domain Boundaries

## 5.1 Expenses-Owned Domain

The Expenses Management Engine owns:

- Expense Requisitions
- Requisition Lines
- Direct Expenses
- Expense Claims
- Staff Advances
- Advance Disbursement Records
- Accountabilities
- Accountability Lines
- Expense Settlements
- Employee Reimbursements
- Refund Requirements
- Refund Receipts
- Recovery Requests
- Petty Cash Funds
- Petty Cash Transactions
- Petty Cash Replenishments
- Petty Cash Reconciliations
- Travel Expense Requests
- Per Diem Calculations
- Mileage Claims
- Expense Allocations
- Expense Policies
- Expense Limits
- Expense Exceptions
- Operational Expense Statuses
- Expense-to-Procurement Conversion References
- Expense-to-Finance Integration References

---

## 5.2 Finance-Owned Domain

The Finance Engine owns:

- Chart of Accounts
- General Ledger
- Journals
- Accounts Payable
- Cash Management
- Banking
- Taxes
- Financial Periods
- Payment Posting
- Financial Reports

The Expenses Management Engine must never create or update ledger entries directly.

---

## 5.3 Human Resources-Owned Domain

The Human Resources Engine owns:

- Employees
- Departments
- Positions
- Supervisors
- Employment Status
- Duty Stations
- Employee Grades
- Employee Bank Details
- Travel Eligibility

The Expenses Management Engine stores employee references only.

---

## 5.4 Procurement-Owned Domain

The Procurement Engine owns:

- Purchase Requisitions
- Supplier Selection
- Requests for Quotation
- Purchase Orders
- Goods Receiving
- Supplier Invoices
- Supplier Payment Requests

The Expenses Management Engine may initiate a procurement conversion but must not perform procurement activities itself.

---

## 5.5 Payroll-Owned Domain

The Payroll Engine owns:

- Payroll Periods
- Employee Earnings
- Employee Deductions
- Salary Payments
- Statutory Calculations
- Payroll Journals

The Expenses Management Engine may request payroll recovery but cannot directly deduct salaries.

---

# 6. Bounded Contexts

The engine is divided into bounded contexts to preserve clear ownership and implementation separation.

```text
Expenses Management Engine
│
├── Requisition Context
├── Direct Expense Context
├── Advance Context
├── Accountability Context
├── Claim & Reimbursement Context
├── Petty Cash Context
├── Travel Expense Context
├── Expense Policy Context
├── Budget Control Context
├── Expense Allocation Context
├── Settlement & Recovery Context
└── Integration Context
```

---

# 7. Requisition Context

The Requisition Context manages requests for funds before expenditure occurs.

Responsibilities include:

- Requisition drafting
- Requisition numbering
- Multiple expense lines
- Beneficiary selection
- Activity description
- Cost allocation
- Funding source selection
- Budget checking
- Policy checking
- Submission
- Revision
- Cancellation
- Approval integration
- Disbursement eligibility
- Procurement conversion

Primary aggregate:

```text
ExpenseRequisition
│
├── RequisitionLines
├── Allocations
├── BudgetReservations
├── SupportingDocuments
├── WorkflowReference
└── DisbursementReferences
```

---

# 8. Direct Expense Context

The Direct Expense Context manages expenses that are incurred or paid without a prior staff advance.

Examples include:

- Rent
- Utilities
- Internet
- Subscriptions
- Bank Charges
- Fuel
- Repairs
- Statutory Fees
- Emergency Operational Expenses

Responsibilities include:

- Expense capture
- Payee reference
- Supplier reference where applicable
- Expense category selection
- Tax information
- Supporting documents
- Policy validation
- Approval integration
- Payment request publication
- Finance event publication

---

# 9. Advance Context

The Advance Context manages funds issued before expenditure.

Responsibilities include:

- Advance request creation
- Outstanding advance validation
- Employee eligibility checking
- Advance limits
- Due date calculation
- Workflow submission
- Approval
- Disbursement authorization
- Outstanding balance tracking
- Accountability reminders
- Overdue classification
- Closure eligibility

Primary aggregate:

```text
StaffAdvance
│
├── AdvanceLines
├── ExpenseAllocations
├── PolicyValidation
├── DisbursementReferences
├── AccountabilityReferences
├── RefundReferences
└── RecoveryReferences
```

---

# 10. Accountability Context

The Accountability Context manages evidence of how approved and disbursed funds were utilized.

Responsibilities include:

- Accountability submission
- Actual expense line capture
- Receipt attachment
- Amount validation
- Category validation
- Date validation
- Policy validation
- Variance calculation
- Review and approval
- Rejection and revision
- Refund determination
- Reimbursement determination
- Recovery determination
- Advance settlement

Primary aggregate:

```text
Accountability
│
├── AccountabilityLines
├── SupportingDocuments
├── ExpenseAllocations
├── ApprovedAmounts
├── RejectedAmounts
├── RefundRequirement
├── ReimbursementRequirement
└── RecoveryRequirement
```

---

# 11. Claim and Reimbursement Context

This context manages expenses paid personally by employees or approved beneficiaries.

Responsibilities include:

- Expense claim creation
- Claim line capture
- Receipt validation
- Policy validation
- Duplicate claim detection
- Approval integration
- Approved reimbursement calculation
- Payment request publication
- Claim settlement
- Claim closure

Claims may include:

- Employee Expenses
- Mileage
- Travel
- Medical Expenses where policy permits
- Communication Expenses
- Field Expenses
- Emergency Purchases

---

# 12. Petty Cash Context

The Petty Cash Context manages controlled cash funds used for low-value operational expenses.

Responsibilities include:

- Petty cash fund setup
- Custodian assignment
- Float limits
- Cash issue
- Voucher creation
- Cash return
- Cash count
- Expense recording
- Replenishment
- Reconciliation
- Cash variance
- Fund suspension
- Fund closure

Primary aggregate:

```text
PettyCashFund
│
├── Custodian
├── FundTransactions
├── PaymentVouchers
├── Replenishments
├── Reconciliations
└── VarianceRecords
```

---

# 13. Travel Expense Context

The Travel Expense Context provides an extensible foundation for travel-related spending.

Responsibilities include:

- Travel request
- Travel purpose
- Destination
- Travel dates
- Traveler selection
- Per diem calculation
- Accommodation estimates
- Transport estimates
- Travel advance
- Mileage calculation
- Travel accountability
- Travel reimbursement

Travel booking and fleet scheduling may later be managed by separate modules.

---

# 14. Expense Policy Context

The Expense Policy Context manages tenant-defined expense controls.

Policies may be configured by:

- Tenant
- Company
- Branch
- Department
- Employee Grade
- Expense Category
- Travel Grade
- Country
- Currency
- Project
- Funding Source

Policy rules include:

- Maximum amount
- Daily limit
- Monthly limit
- Receipt requirement
- Minimum receipt threshold
- Advance eligibility
- Maximum outstanding advances
- Accountability due period
- Return deadline
- Reimbursement eligibility
- Per diem rate
- Mileage rate
- Approval threshold
- Prohibited expense types
- Required supporting documents

---

# 15. Budget Control Context

The Budget Control Context coordinates with the Finance Engine or budget services to validate available funds.

It does not own official budget balances.

Responsibilities include:

- Budget dimension resolution
- Budget availability requests
- Budget reservation requests
- Budget commitment requests
- Budget release requests
- Budget consumption notifications
- Budget override requests
- Budget validation result storage

Supported control modes:

```text
None
Soft Warning
Hard Stop
Override Required
Workflow Escalation
```

---

# 16. Expense Allocation Context

The Expense Allocation Context distributes expenses across organizational and financial dimensions.

Supported dimensions include:

- Company
- Branch
- Department
- Cost Center
- Project
- Grant
- Program
- Activity
- Funding Source
- Employee
- Asset
- Customer Engagement
- Custom Tenant Dimensions

An expense line may have one or multiple allocations.

Allocation totals must equal the expense line amount.

---

# 17. Settlement and Recovery Context

This context resolves outstanding amounts after accountability or claim review.

Supported settlement outcomes include:

- Fully Accounted
- Employee Refund Required
- Employee Reimbursement Required
- Salary Recovery Required
- Advance Offset
- Carry Forward
- Disputed Amount
- Write-Off Request
- Policy Exception

Settlement processing must remain separate from accounting.

---

# 18. Application Layer

The Application Layer coordinates domain operations and external integrations.

It includes:

- Commands
- Queries
- Application Services
- DTOs
- Validators
- Integration Adapters
- Event Publishers

Application services must not contain database-specific implementation details.

---

# 19. Core Application Services

Recommended services include:

```text
CreateExpenseRequisitionService
SubmitExpenseRequisitionService
CancelExpenseRequisitionService
ConvertRequisitionToProcurementService

CreateDirectExpenseService
SubmitDirectExpenseService

RequestStaffAdvanceService
ApproveAdvanceDisbursementService
RecordAdvanceDisbursementService

SubmitAccountabilityService
ReviewAccountabilityService
ApproveAccountabilityService
RejectAccountabilityService

CreateExpenseClaimService
ApproveExpenseClaimService
CreateReimbursementRequestService

CreatePettyCashFundService
IssuePettyCashService
ReconcilePettyCashService
RequestPettyCashReplenishmentService

CalculatePerDiemService
CalculateMileageClaimService

ValidateExpensePolicyService
ValidateExpenseBudgetService
ReserveExpenseBudgetService
ReleaseExpenseBudgetService

DetermineExpenseSettlementService
RecordExpenseRefundService
RequestPayrollRecoveryService
CloseExpenseTransactionService
```

---

# 20. Domain Services

Domain services implement business rules that do not belong to a single entity.

Examples include:

```text
ExpensePolicyEvaluator
BudgetValidationCoordinator
AdvanceEligibilityEvaluator
AccountabilityVarianceCalculator
ExpenseAllocationValidator
PerDiemCalculator
MileageCalculator
DuplicateExpenseDetector
SettlementResolver
OutstandingAdvanceEvaluator
PettyCashBalanceCalculator
```

---

# 21. Repository Pattern

All persistence operations must pass through repositories.

Recommended repositories include:

```text
ExpenseRequisitionRepository
ExpenseRequisitionLineRepository
DirectExpenseRepository
StaffAdvanceRepository
AccountabilityRepository
AccountabilityLineRepository
ExpenseClaimRepository
ReimbursementRepository
RefundRepository
RecoveryRequestRepository
PettyCashFundRepository
PettyCashTransactionRepository
PettyCashReconciliationRepository
ExpensePolicyRepository
ExpenseAllocationRepository
ExpenseExceptionRepository
ExpenseIntegrationReferenceRepository
```

Repositories must enforce:

- Tenant filtering
- Company filtering
- Branch filtering
- Optimistic concurrency
- Audit metadata
- Soft deletion where applicable
- Status transition validation
- Repository-level consistency checks

---

# 22. Frontend Architecture

The frontend uses a feature-based structure.

```text
src/
└── features/
    └── expenses/
        ├── components/
        │   ├── accountabilities/
        │   ├── advances/
        │   ├── allocations/
        │   ├── claims/
        │   ├── direct-expenses/
        │   ├── documents/
        │   ├── petty-cash/
        │   ├── policies/
        │   ├── requisitions/
        │   ├── settlements/
        │   └── travel/
        ├── context/
        ├── hooks/
        ├── pages/
        ├── routes/
        ├── schemas/
        ├── services/
        ├── types/
        └── utils/
```

React Context API may be used for:

- Requisition draft state
- Current expense filters
- Accountability draft state
- Petty cash session state
- Expense configuration state

Server persistence remains authoritative.

---

# 23. Backend Architecture

The backend uses:

- Supabase PostgreSQL
- Supabase Auth
- Row Level Security
- Supabase Edge Functions
- Supabase Storage
- Supabase Realtime
- PostgreSQL Transactions

Recommended structure:

```text
supabase/
├── functions/
│   └── expenses/
│       ├── submit-requisition/
│       ├── approve-disbursement/
│       ├── record-disbursement/
│       ├── submit-accountability/
│       ├── approve-accountability/
│       ├── process-refund/
│       ├── request-reimbursement/
│       ├── reconcile-petty-cash/
│       ├── validate-budget/
│       ├── evaluate-policy/
│       └── request-payroll-recovery/
│
└── migrations/
    └── expenses/
```

Critical operations must execute within database transactions.

---

# 24. Expense Requisition Flow

```text
Requester Creates Requisition

↓

Validate Tenant and Employee

↓

Validate Expense Lines

↓

Resolve Expense Policies

↓

Resolve Budget Dimensions

↓

Check Budget Availability

↓

Attach Supporting Documents

↓

Generate Requisition Number

↓

Submit to Workflow Engine

↓

Approval Outcome

├── Rejected
│     ↓
│ Return or Close
│
├── Approved for Disbursement
│     ↓
│ Create Disbursement Request
│
└── Procurement Required
      ↓
Convert to Procurement Requisition
```

---

# 25. Direct Expense Flow

```text
Expense Captured

↓

Validate Payee

↓

Validate Category and Tax Information

↓

Validate Expense Policy

↓

Validate Budget

↓

Approval Required?

├── Yes
│     ↓
│ Workflow Approval
│
└── No
      ↓
Continue

↓

Publish Payment or Finance Event

↓

Record Integration Reference

↓

Complete Expense
```

---

# 26. Staff Advance Flow

```text
Employee Requests Advance

↓

Validate Employment Status

↓

Check Existing Advances

↓

Evaluate Advance Policy

↓

Validate Budget

↓

Submit for Approval

↓

Approved?

├── No
│     ↓
│ Reject or Return
│
└── Yes
      ↓
Create Disbursement Authorization

↓

Finance Processes Payment

↓

Receive Disbursement Confirmation

↓

Mark Advance Outstanding

↓

Schedule Accountability Due Date
```

---

# 27. Accountability Processing Flow

```text
Employee Selects Advance

↓

Capture Actual Expense Lines

↓

Upload Receipts

↓

Validate Amounts and Dates

↓

Validate Expense Policies

↓

Calculate Total Accounted Amount

↓

Compare Against Advance

↓

Submit for Review

↓

Reviewer Approves or Rejects Lines

↓

Determine Settlement

├── Fully Accounted
├── Refund Required
├── Reimbursement Required
├── Recovery Required
└── Disputed

↓

Complete Settlement

↓

Close Advance
```

---

# 28. Petty Cash Flow

```text
Create Petty Cash Fund

↓

Assign Custodian

↓

Set Authorized Float

↓

Fund Petty Cash

↓

Process Expense Vouchers

↓

Track Cash Balance

↓

Perform Cash Count

↓

Reconcile Expected and Actual Cash

↓

Variance?

├── No
│     ↓
│ Approve Reconciliation
│
└── Yes
      ↓
Create Variance Exception

↓

Request Replenishment

↓

Finance Processes Replenishment
```

---

# 29. Integration Architecture

## 29.1 Platform Core Integration

Provides:

- Tenant context
- Company context
- Branch context
- Workspace context
- User identity
- Subscription validation
- Feature availability

Every request must contain a valid active context.

---

## 29.2 Authorization Engine Integration

Provides access decisions for:

- Creating requisitions
- Approving expenses
- Disbursing advances
- Reviewing accountabilities
- Managing petty cash
- Approving exceptions
- Viewing confidential employee expenses
- Exporting reports
- Managing policies

Authorization must be enforced both in the application layer and through RLS.

---

## 29.3 Workflow Engine Integration

The engine submits workflow subjects containing:

- Entity Type
- Entity ID
- Tenant
- Company
- Branch
- Requester
- Amount
- Currency
- Expense Category
- Department
- Project
- Risk or Exception Flags

The Workflow Engine returns:

- Current Step
- Assigned Approvers
- Approval Outcome
- Rejection Reason
- Delegation
- Escalation
- Workflow Completion

---

## 29.4 Finance Engine Integration

Finance integration occurs through commands and events.

Outgoing requests include:

- Reserve Budget
- Release Budget
- Commit Budget
- Create Disbursement
- Create Payment Request
- Record Refund
- Create Reimbursement
- Record Petty Cash Funding
- Record Petty Cash Replenishment

Incoming confirmations include:

- Budget Reserved
- Budget Rejected
- Payment Processed
- Payment Failed
- Advance Disbursed
- Refund Received
- Reimbursement Paid
- Journal Posted

---

## 29.5 Human Resources Integration

HR services provide:

- Employee validation
- Department
- Supervisor
- Employment status
- Employee grade
- Duty station
- Payroll reference
- Travel eligibility
- Per diem classification

If HR is not yet implemented, Platform Core users may temporarily act as requesters, but the architecture must retain a dedicated employee reference for future HR integration.

---

## 29.6 Procurement Integration

When an approved requisition requires formal procurement:

```text
Expenses Engine

↓

Create Procurement Conversion Request

↓

Procurement Engine Creates Purchase Requisition

↓

Return Procurement Reference

↓

Expenses Engine Marks Requisition as Converted
```

The original expense record remains immutable and traceable.

---

## 29.7 Payroll Integration

For recoveries:

```text
Accountability Settlement

↓

Salary Recovery Required

↓

Create Recovery Request

↓

Payroll Engine Accepts Request

↓

Deduction Scheduled

↓

Payroll Confirms Recovery

↓

Expense Settlement Updated
```

---

## 29.8 Document Management Integration

Documents must be stored outside the Expenses domain tables.

The Expenses Engine stores:

- Document ID
- Document Type
- Entity Type
- Entity ID
- Version
- Verification Status

Supported documents include:

- Receipts
- Invoices
- Quotations
- Attendance Lists
- Activity Reports
- Travel Tickets
- Hotel Bills
- Bank Slips
- Mobile Money Confirmations

---

# 30. Event-Driven Architecture

The Expenses Management Engine publishes events for meaningful business state changes.

## 30.1 Requisition Events

```text
expense.requisition.created
expense.requisition.updated
expense.requisition.submitted
expense.requisition.returned
expense.requisition.approved
expense.requisition.partially_approved
expense.requisition.rejected
expense.requisition.cancelled
expense.requisition.converted_to_procurement
```

---

## 30.2 Advance Events

```text
expense.advance.requested
expense.advance.approved
expense.advance.rejected
expense.advance.disbursement_requested
expense.advance.disbursed
expense.advance.overdue
expense.advance.closed
```

---

## 30.3 Accountability Events

```text
expense.accountability.created
expense.accountability.submitted
expense.accountability.returned
expense.accountability.approved
expense.accountability.partially_approved
expense.accountability.rejected
expense.accountability.settled
```

---

## 30.4 Direct Expense and Claim Events

```text
expense.direct.created
expense.direct.approved
expense.direct.payment_requested
expense.direct.paid

expense.claim.submitted
expense.claim.approved
expense.claim.rejected
expense.claim.reimbursement_requested
expense.claim.reimbursed
```

---

## 30.5 Settlement Events

```text
expense.refund.required
expense.refund.received
expense.reimbursement.required
expense.reimbursement.paid
expense.recovery.required
expense.salary_recovery.requested
expense.salary_recovery.completed
expense.settlement.completed
```

---

## 30.6 Petty Cash Events

```text
expense.petty_cash.fund_created
expense.petty_cash.funded
expense.petty_cash.payment_recorded
expense.petty_cash.refund_recorded
expense.petty_cash.reconciled
expense.petty_cash.variance_recorded
expense.petty_cash.replenishment_requested
expense.petty_cash.replenished
```

---

# 31. Event Envelope

All events must use the standard Business Suite event envelope.

```json
{
  "event_id": "uuid",
  "event_type": "expense.advance.disbursed",
  "event_version": 1,
  "tenant_id": "uuid",
  "company_id": "uuid",
  "branch_id": "uuid",
  "aggregate_type": "staff_advance",
  "aggregate_id": "uuid",
  "occurred_at": "2026-07-21T08:00:00Z",
  "actor_id": "uuid",
  "correlation_id": "uuid",
  "causation_id": "uuid",
  "payload": {}
}
```

---

# 32. Idempotency

Financial and disbursement operations must be idempotent.

Operations requiring idempotency keys include:

- Advance disbursement
- Direct expense payment request
- Reimbursement request
- Refund receipt
- Petty cash replenishment
- Payroll recovery request
- Budget reservation
- Budget release
- Finance event publication

Duplicate requests must return the existing result rather than create duplicate financial effects.

---

# 33. Transaction Management

Critical operations must use atomic transactions.

Examples include:

- Requisition submission and workflow reference creation
- Advance disbursement confirmation and balance update
- Accountability approval and settlement calculation
- Refund receipt and outstanding balance update
- Petty cash payment and fund balance update
- Reconciliation and variance creation
- Requisition cancellation and budget release

Outbox events must be committed in the same transaction as the business state change.

---

# 34. Outbox Pattern

The Expenses Management Engine shall use the transactional outbox pattern.

```text
Domain Transaction

↓

Update Expense Record

↓

Insert Outbox Event

↓

Commit Transaction

↓

Event Publisher Reads Outbox

↓

Publish to Platform Event Bus

↓

Mark Event Published
```

This prevents missing events after successful database commits.

---

# 35. Optimistic Concurrency

Expense records must include a version number.

Optimistic concurrency is required for:

- Requisitions
- Advances
- Accountabilities
- Claims
- Petty cash funds
- Reconciliations
- Expense policies

If two users update the same record, the second update must fail with a clear conflict response.

---

# 36. Status Transition Rules

All status changes must pass through domain services.

Direct database status edits are prohibited.

Example requisition transitions:

```text
Draft
  ├── Submitted
  └── Cancelled

Submitted
  ├── Under Review
  ├── Returned for Revision
  ├── Approved
  ├── Partially Approved
  └── Rejected

Approved
  ├── Pending Disbursement
  ├── Converted to Procurement
  └── Cancelled with Authorization

Pending Disbursement
  ├── Partially Disbursed
  └── Fully Disbursed
```

---

# 37. Budget Reservation Architecture

Where enabled, budget reservation occurs before final approval or disbursement.

```text
Expense Request

↓

Resolve Budget Dimensions

↓

Request Budget Availability

↓

Budget Available?

├── No
│     ├── Hard Stop
│     ├── Warning
│     └── Override Workflow
│
└── Yes
      ↓
Reserve Budget

↓

Store Reservation Reference

↓

Continue Approval
```

Budget reservations must be released when:

- Requisition is rejected
- Requisition is cancelled
- Approved amount is reduced
- Request expires
- Procurement conversion changes the commitment owner

---

# 38. Policy Evaluation Architecture

Policy evaluation occurs at multiple lifecycle points.

```text
Draft Validation
Submission Validation
Approval Validation
Disbursement Validation
Accountability Validation
Settlement Validation
```

Policy results include:

```text
Passed
Warning
Blocked
Override Required
Document Required
Additional Approval Required
```

Policy evaluation results must be stored for auditability.

---

# 39. Duplicate Expense Detection

The engine should identify possible duplicate expenses using:

- Employee
- Amount
- Date
- Merchant or Payee
- Receipt Number
- Invoice Number
- Expense Category
- Document Hash
- Payment Reference

A suspected duplicate must not always be rejected automatically.

It may be:

- Blocked
- Warned
- Sent for review
- Allowed with override

---

# 40. Multi-Tenant Architecture

Every expenses table must include:

- tenant_id
- company_id where applicable
- branch_id where applicable

Tenant isolation must be enforced through:

- Supabase Row Level Security
- Active tenant context
- Repository filtering
- API validation
- Event tenant metadata

Cross-tenant references are prohibited.

---

# 41. Multi-Company Architecture

Each expense belongs to one legal company.

Cross-company expenses must be represented using separate transactions and intercompany references.

The Expenses Management Engine must not create intercompany accounting entries.

The Finance Engine handles intercompany posting.

---

# 42. Realtime Architecture

Supabase Realtime may support:

- Approval status updates
- Disbursement status updates
- Accountability review updates
- Petty cash balance alerts
- Budget override requests
- Settlement updates
- Document verification status

Realtime messages must respect RLS and authorization.

---

# 43. Notification Architecture

Notification triggers include:

- Requisition submitted
- Requisition approved
- Requisition rejected
- Advance disbursed
- Accountability due
- Accountability overdue
- Refund required
- Reimbursement approved
- Recovery initiated
- Petty cash balance low
- Replenishment approved
- Policy exception detected

The Notification Engine controls channels and templates.

---

# 44. Search Architecture

The Search & Indexing Engine indexes:

- Requisition Number
- Advance Number
- Accountability Number
- Claim Number
- Employee Name
- Department
- Payee
- Expense Category
- Project
- Cost Center
- Amount
- Status
- Document Reference

Search results must be filtered by authorization and tenant access.

---

# 45. Reporting Architecture

The Expenses Management Engine exposes reporting datasets but does not implement report rendering.

Reporting datasets include:

- Requisition Summary
- Expense Detail
- Advance Aging
- Outstanding Accountabilities
- Employee Advance Balances
- Department Expenses
- Project Expenses
- Budget Utilization
- Expense Category Analysis
- Policy Exceptions
- Reimbursements
- Refunds
- Payroll Recoveries
- Petty Cash Movements
- Petty Cash Reconciliations
- Expense Approval Lead Time

---

# 46. Audit Architecture

The Platform Activity & Audit Engine records:

- Before and after values
- Actor
- Timestamp
- Tenant
- Company
- Branch
- Device or IP context
- Reason
- Workflow action
- Override details
- Correlation ID

Sensitive operations requiring enhanced audit include:

- Approval
- Disbursement
- Accountability line rejection
- Refund confirmation
- Salary recovery request
- Petty cash variance
- Policy override
- Budget override
- Record reopening
- Cancellation after approval

---

# 47. Security Architecture

The architecture enforces:

- Supabase authentication
- Authorization Engine permissions
- Row Level Security
- Tenant context
- Company and branch restrictions
- Segregation of duties
- Workflow approvals
- Immutable audit trails
- Document access controls
- Server-side validation
- Idempotent finance integration

Examples of segregation-of-duty rules:

- A requester should not approve their own expense unless policy explicitly permits.
- A petty cash custodian should not approve their own reconciliation.
- A disbursement officer should not authorize the same payment where dual control is required.
- An accountability reviewer should not modify the submitted accountability lines.

---

# 48. Error Handling

The engine must handle:

- Invalid tenant context
- Missing employee reference
- Insufficient budget
- Policy violation
- Duplicate expense
- Workflow failure
- Disbursement failure
- Finance integration failure
- Missing receipt
- Invalid accountability amount
- Refund mismatch
- Petty cash insufficiency
- Payroll recovery rejection
- Concurrency conflicts

Errors must be:

- User-friendly
- Logged
- Auditable
- Correlated
- Retriable where appropriate
- Non-destructive to committed records

---

# 49. Retry Architecture

Retryable integrations include:

- Event publishing
- Notification delivery
- Document processing
- Finance requests
- Payroll recovery requests
- Procurement conversion
- Search indexing

Retries must use:

- Exponential backoff
- Maximum retry count
- Dead-letter handling
- Idempotency keys
- Error metadata
- Manual replay capability

---

# 50. Performance Architecture

The engine must support:

- Large expense volumes
- Multiple concurrent approvers
- Multi-branch operations
- High-volume petty cash transactions
- Large supporting document collections
- Complex allocation dimensions
- Reporting without transactional slowdown

Performance strategies include:

- Indexed status and date columns
- Composite tenant indexes
- Pagination
- Asynchronous event publishing
- Cached policies and reference data
- Summary tables or reporting views
- Deferred document processing
- Batched reminder generation

---

# 51. Scalability Architecture

The Expenses Management Engine shall scale across:

- Multiple tenants
- Multiple companies
- Multiple branches
- Thousands of employees
- Multiple currencies
- Multiple projects and grants
- High-volume expense claims
- Concurrent workflows
- Distributed finance teams

Stateless Edge Functions should be horizontally scalable.

---

# 52. Mobile Architecture

The architecture supports future mobile applications for:

- Requisition creation
- Receipt capture
- Claim submission
- Accountability submission
- Approval actions
- Petty cash voucher capture
- Mileage capture
- Push notifications

Mobile clients must use the same APIs, permissions, workflows, and validation rules as the web client.

---

# 53. Offline Considerations

Limited offline capability may be added for field operations.

Potential offline features include:

- Draft requisitions
- Receipt capture
- Draft accountability lines
- Draft expense claims

The following must not become official while offline:

- Approval
- Disbursement
- Refund Confirmation
- Reimbursement Authorization
- Payroll Recovery
- Petty Cash Reconciliation

Offline records must be synchronized and validated before submission.

---

# 54. Deployment Architecture

The Expenses Management Engine is deployed as part of the Business Suite platform.

```text
React Web Application
        │
        ▼
Supabase APIs and Edge Functions
        │
        ├── PostgreSQL
        ├── Row Level Security
        ├── Storage
        ├── Realtime
        └── Scheduled Jobs
                │
                ▼
Platform Event Bus
        │
        ├── Finance Engine
        ├── Workflow Engine
        ├── Notification Engine
        ├── Reporting Engine
        ├── Payroll Engine
        └── Procurement Engine
```

---

# 55. Scheduled Jobs

Scheduled jobs may include:

- Accountability due reminders
- Overdue advance detection
- Outstanding refund reminders
- Policy limit refresh
- Per diem rate activation
- Petty cash balance monitoring
- Workflow escalation
- Failed integration retries
- Expired draft cleanup
- Budget reservation expiry
- Reporting aggregation

Scheduled jobs must remain tenant-aware.

---

# 56. Observability

The engine must expose operational metrics such as:

- Requisitions submitted
- Approval completion time
- Advances outstanding
- Accountabilities overdue
- Refunds outstanding
- Reimbursements pending
- Petty cash variances
- Finance integration failures
- Workflow failures
- Event publishing failures
- Budget validation failures

Logs must include:

- Correlation ID
- Tenant ID
- Entity ID
- Event Type
- Actor ID
- Error Category

---

# 57. Feature Flags

Feature Management may enable or disable:

- Staff Advances
- Accountabilities
- Direct Expenses
- Petty Cash
- Travel Expenses
- Per Diem
- Mileage Claims
- Budget Controls
- Payroll Recovery
- Procurement Conversion
- Offline Draft Capture
- Corporate Card Integration

Feature flags must not replace permission checks.

---

# 58. Extension Points

The architecture supports future integrations with:

- Corporate Cards
- Banking Platforms
- Mobile Money Platforms
- Travel Booking Systems
- Receipt OCR
- AI Expense Classification
- Fraud Detection
- GPS Mileage Tracking
- Fuel Cards
- Donor Compliance Rules
- Electronic Tax Invoicing
- Fiscal Devices
- Grant Management
- Project Management
- Fleet Management

---

# 59. Architectural Rules

The Expenses Management Engine must follow these rules:

- Never post directly to the General Ledger.
- Never update official budget balances directly.
- Never maintain employee master records.
- Never deduct employee salary directly.
- Never implement procurement processes internally.
- Never bypass the Workflow Engine for configured approvals.
- Never generate document numbers manually.
- Never store unmanaged attachments.
- Never allow an unapproved disbursement.
- Never close an advance with unresolved settlement.
- Never permit allocation totals to differ from the transaction amount.
- Never physically delete approved or financially relevant records.
- Never publish financial events without idempotency controls.
- Never allow cross-tenant or unauthorized company access.
- Never trust client-side calculations without server validation.

---

# 60. Architecture Summary

The Expenses Management Engine provides a secure, scalable, policy-driven, budget-aware, and event-driven foundation for managing organizational expenditure.

It supports the complete lifecycle of:

- Requisitions
- Direct Expenses
- Advances
- Accountabilities
- Claims
- Reimbursements
- Refunds
- Recoveries
- Petty Cash
- Travel Expenses

The architecture preserves clear ownership boundaries by integrating with Finance, Human Resources, Payroll, Procurement, Workflow, Authorization, Document Management, Notifications, Reporting, Search, Audit, and the Platform Event Bus.

It is suitable for commercial businesses, NGOs, government institutions, donor-funded programs, projects, and multi-branch enterprises.

---

# 61. Next Document

The next document is:

**DATABASE.md**

It will define:

- Expense Tables
- Requisition Tables
- Advance Tables
- Accountability Tables
- Direct Expense Tables
- Claim and Reimbursement Tables
- Petty Cash Tables
- Travel Expense Tables
- Policy Tables
- Allocation Tables
- Settlement and Recovery Tables
- Integration References
- Constraints
- Indexes
- Row Level Security
- Audit and Data Retention Rules
