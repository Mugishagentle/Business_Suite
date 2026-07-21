# SECURITY.md

# Expenses Management Engine Security Specification

---

# 1. Overview

This document defines the security architecture, controls, permissions, policies, fraud-prevention mechanisms, audit requirements, data-protection rules, and integration safeguards for the **Expenses Management Engine**.

The security model protects:

- Expense Requisitions
- Direct Expenses
- Staff Advances
- Advance Disbursements
- Accountabilities
- Expense Claims
- Reimbursements
- Refunds
- Recoveries
- Petty Cash
- Travel Expenses
- Per Diem
- Mileage Claims
- Operational Fund Transfers
- Budget Overrides
- Policy Exceptions
- Expense Documents
- Approval Decisions
- Financial Integration References

The Expenses Management Engine operates within the shared Business Suite security architecture and must integrate with:

- Platform Core
- Authorization Engine
- Workflow Engine
- Finance Engine
- Human Resources Engine
- Payroll Engine
- Procurement Engine
- Document Management Engine
- Notification Engine
- Activity & Audit Engine
- Search & Indexing Engine
- Platform Event Bus

---

# 2. Security Objectives

The Expenses Management Engine must ensure:

- Tenant data isolation
- Company and branch access control
- Confidentiality of employee expense data
- Integrity of financial amounts
- Controlled transaction approval
- Segregation of duties
- Prevention of duplicate payment
- Prevention of unauthorized disbursement
- Protection against expense fraud
- Traceability of every material action
- Secure document handling
- Secure system integrations
- Protection of personally identifiable information
- Controlled record reopening and reversal
- Availability of critical expense records
- Compliance with tenant, legal, tax, donor, and audit requirements

---

# 3. Security Principles

The engine follows these principles:

- Deny access by default.
- Grant the minimum required permission.
- Validate every action server-side.
- Never trust frontend permissions alone.
- Enforce tenant isolation at database level.
- Separate initiation, approval, payment, and reconciliation duties.
- Preserve original financial history.
- Require explicit approval for exceptional actions.
- Treat financial integrations as idempotent operations.
- Record all sensitive actions in an immutable audit trail.
- Protect documents based on their parent transaction.
- Avoid exposing sensitive employee and payment information.
- Require stronger controls for high-value transactions.
- Use secure service identities for system integrations.
- Reject cross-tenant references.
- Require correlation IDs for distributed transactions.

---

# 4. Security Boundaries

The Expenses Management Engine owns security enforcement for:

- Expense entity access
- Expense lifecycle actions
- Expense-specific permissions
- Expense amount authority
- Petty cash controls
- Accountability review controls
- Refund and recovery initiation
- Operational fund transfer controls
- Policy exception handling
- Expense document requirements
- Duplicate expense checks
- Expense-specific fraud indicators

The engine does not independently own:

- Authentication
- User identity
- Tenant membership
- Global role definitions
- Workflow assignment rules
- General Ledger permissions
- Bank account security
- Payroll deduction execution
- Supplier master security
- Employee master security
- Document storage infrastructure

These remain owned by their respective platform engines.

---

# 5. Authentication

Authentication is provided by Platform Core and Supabase Auth.

Supported authentication methods may include:

- Email and password
- Google OAuth
- Microsoft OAuth
- Magic links
- Multi-factor authentication
- Single sign-on
- Enterprise identity providers

The Expenses Management Engine must not implement a separate authentication system.

---

# 6. Session Security

Every request must validate:

- Authenticated user
- Active session
- Active tenant membership
- Active company access
- Active branch access
- Session validity
- Token expiry
- Account status
- Required authentication assurance level

Sensitive actions may require recent reauthentication.

Examples include:

- High-value disbursement
- Refund confirmation
- Payroll recovery authorization
- Petty cash fund closure
- Fund transfer dispatch
- Record reversal
- Policy override
- Budget override
- Reopening a closed transaction

---

# 7. Multi-Factor Authentication

MFA should be mandatory or strongly recommended for:

- Finance administrators
- Cashiers
- Petty cash custodians
- Payroll recovery approvers
- Workflow administrators
- Expense policy administrators
- High-value approvers
- Security administrators
- Users authorized to reopen or reverse transactions

Tenant policies may enforce MFA by role, action, amount, company, or risk level.

---

# 8. Tenant Isolation

Every Expenses Management Engine record must include:

```sql
tenant_id uuid not null
```

All queries must be scoped by tenant.

A user must never access a record based only on:

```text
entity_id
```

Access must validate:

```text
authenticated_user
+
active_tenant
+
tenant_membership
+
entity_tenant
+
permission
```

Cross-tenant access must be blocked at:

- API layer
- Application service layer
- Repository layer
- Database RLS layer
- Search layer
- Reporting layer
- Realtime subscriptions
- Document access layer

---

# 9. Company Isolation

Within a tenant, users may have access to:

- All companies
- Selected companies
- One company
- Shared service companies
- Consolidated reporting only

Every company-sensitive transaction must validate:

```text
record.company_id
IN
user_authorized_company_ids
```

Company access does not automatically grant permission to perform every expense action.

---

# 10. Branch Isolation

Branch-level access may restrict:

- Requisition visibility
- Petty cash fund access
- Cashier operations
- Operational fund transfers
- Branch reimbursements
- Branch advances
- Branch reporting

Users may be assigned:

- All branches
- Selected branches
- Home branch only
- Regional branches
- Read-only branch access

---

# 11. Row Level Security

RLS must be enabled on every Expenses Management Engine business table.

Example:

```sql
alter table expense_requisitions enable row level security;
```

Basic tenant policy:

```sql
create policy expense_requisitions_select
on expense_requisitions
for select
using (
    tenant_id = platform_current_tenant_id()
);
```

RLS helper functions must be:

- Security-reviewed
- Tenant-aware
- Stable
- Non-user-modifiable
- Protected from privilege escalation

---

# 12. RLS Security Model

A complete RLS policy should evaluate:

```text
Tenant Access
AND
Company Access
AND
Branch Access
AND
Record Visibility Rule
AND
Permission
```

Example conceptual logic:

```sql
using (
    tenant_id = platform_current_tenant_id()
    and platform_user_has_company_access(company_id)
    and (
        platform_user_has_permission('expenses.requisitions.view_all')
        or requester_user_id = auth.uid()
        or platform_user_is_assigned_approver(id)
        or platform_user_has_audit_access()
    )
);
```

---

# 13. Service Role Security

Supabase service-role credentials must:

- Never be exposed to the frontend.
- Be restricted to secure backend services.
- Be stored in protected environment variables.
- Be rotated according to platform policy.
- Be monitored for unusual usage.
- Be used only when RLS bypass is genuinely required.

Business actions must not use service-role privileges merely for convenience.

---

# 14. Permission Model

Permissions should follow:

```text
expenses.<domain>.<action>
```

Examples:

```text
expenses.dashboard.view

expenses.requisitions.view
expenses.requisitions.view_all
expenses.requisitions.create
expenses.requisitions.edit
expenses.requisitions.submit
expenses.requisitions.withdraw
expenses.requisitions.cancel
expenses.requisitions.approve
expenses.requisitions.partial_approve
expenses.requisitions.reject

expenses.direct_expenses.create
expenses.direct_expenses.approve
expenses.direct_expenses.request_payment

expenses.advances.view
expenses.advances.create
expenses.advances.approve
expenses.advances.disburse

expenses.accountabilities.create
expenses.accountabilities.submit
expenses.accountabilities.review
expenses.accountabilities.approve

expenses.claims.create
expenses.claims.approve

expenses.reimbursements.view
expenses.reimbursements.request_payment
expenses.reimbursements.retry_payment

expenses.refunds.record_evidence
expenses.refunds.confirm_receipt

expenses.recoveries.initiate
expenses.recoveries.approve

expenses.petty_cash.manage_funds
expenses.petty_cash.issue
expenses.petty_cash.replenish
expenses.petty_cash.reconcile
expenses.petty_cash.approve_reconciliation

expenses.travel.create
expenses.travel.approve

expenses.mileage.create
expenses.mileage.approve

expenses.fund_transfers.create
expenses.fund_transfers.approve
expenses.fund_transfers.dispatch
expenses.fund_transfers.receive

expenses.policies.manage
expenses.budget_overrides.approve
expenses.policy_exceptions.approve

expenses.records.reopen
expenses.records.reverse

expenses.reports.view
expenses.reports.export
```

---

# 15. Role Model

Suggested business roles include:

- Employee
- Requester
- Supervisor
- Department Head
- Project Manager
- Budget Holder
- Finance Reviewer
- Finance Approver
- Cashier
- Disbursement Officer
- Petty Cash Custodian
- Accountability Reviewer
- Payroll Liaison
- Internal Auditor
- Expense Administrator
- Workflow Administrator
- Tenant Administrator

Roles should map to permissions through the Authorization Engine.

Hard-coded role-name checks should be avoided.

---

# 16. Employee Self-Service Access

Employees may be allowed to:

- Create requisitions
- View their own requisitions
- View their own advances
- Submit their own accountabilities
- Create claims
- View their own reimbursements
- View their own refund obligations
- View their own recovery obligations
- Create travel requests
- Submit mileage claims

Employees must not automatically access:

- Other employees’ expenses
- Internal fraud indicators
- Confidential reviewer notes
- Other employees’ bank details
- Company-wide petty cash balances
- Audit investigation records
- Restricted policy configuration

---

# 17. Record Ownership

Record ownership may be determined by:

- Requester user
- Employee
- Beneficiary
- Custodian
- Assigned reviewer
- Assigned approver
- Company
- Branch
- Department
- Project

Ownership alone does not always grant modification rights.

For example:

- An employee owns an accountability submission.
- The employee cannot edit it after approval.
- A custodian operates a petty cash fund.
- The custodian cannot approve their own reconciliation.

---

# 18. Segregation of Duties

The system must separate critical duties.

Core duties include:

```text
Request
Approve
Disburse
Receive
Account
Review
Reconcile
Reverse
Administer
Audit
```

No user should control an entire high-risk financial process without independent review.

---

# 19. Requisition Segregation

The following controls should apply:

- Requester cannot provide final approval.
- Requester cannot approve their own budget override.
- Requester cannot approve their own policy exception.
- Requester should not confirm their own payment.
- Requester cannot independently reopen an approved record.

Limited low-value exceptions may be tenant-configurable but must be audited.

---

# 20. Advance Segregation

The advance workflow should separate:

- Advance requester
- Advance approver
- Disbursement processor
- Accountability reviewer
- Refund confirmer

An employee receiving an advance must not:

- Approve the advance
- Confirm disbursement
- Approve their accountability
- Confirm their own refund receipt

---

# 21. Petty Cash Segregation

Petty cash should separate:

- Voucher requester
- Voucher approver
- Custodian
- Reconciliation reviewer
- Variance approver

The custodian must not:

- Approve their own fund setup
- Approve their own reconciliation
- Approve their own shortage write-off
- Alter completed cash transactions
- Confirm both sides of a custodian transfer

---

# 22. Fund Transfer Segregation

Operational fund transfer duties include:

- Transfer requester
- Transfer approver
- Source dispatcher
- Destination receiver
- Variance reviewer

The same user should not independently:

- Approve
- Dispatch
- Receive

the same transfer.

---

# 23. Amount Authority

Approval authority may depend on:

- Transaction amount
- Currency
- Base currency equivalent
- Expense category
- Department
- Project
- Funding source
- Company
- Branch
- Policy exception
- Emergency status

The system must validate authority at the moment of action.

A user’s previous authority must not be assumed if:

- Their role changed.
- Their company assignment changed.
- The amount changed.
- Exchange rates changed.
- A policy exception was introduced.

---

# 24. Approval Security

Every approval action must validate:

- User identity
- Workflow assignment
- Active approval step
- Permission
- Amount authority
- Record status
- Expected version
- Segregation of duties
- Required comments
- Required documents
- Previous workflow outcome

Approval must be processed through a secure backend command.

---

# 25. Approval Token Security

Approval links in notifications must not directly authorize an action.

A notification link may identify the transaction, but the system must still require:

- Authentication
- Tenant selection
- Permission validation
- Workflow assignment validation
- Current step validation

Sensitive approvals should not be completed from unauthenticated email links.

---

# 26. Delegation Security

Approval delegation must validate:

- Delegator authority
- Delegate eligibility
- Delegation start and end dates
- Company scope
- Branch scope
- Permission compatibility
- Conflict of interest
- Prohibited self-approval

Every delegated action must record:

- Original approver
- Delegate
- Delegation rule
- Action timestamp

---

# 27. Workflow Administration Security

Workflow administrators may configure routing but should not automatically gain:

- Expense approval rights
- Payment rights
- Petty cash rights
- Reversal rights

Workflow configuration and transaction approval must remain separate permissions.

Changes to active workflow definitions must be audited.

---

# 28. Requisition Security

Requisition creation must validate:

- Requester identity
- Employee eligibility
- Department access
- Project access
- Funding source access
- Expense category permissions
- Currency validity
- Amount limits

After submission:

- Core financial fields must be locked.
- Changes require return or amendment.
- Original values must remain visible.
- Status cannot be changed manually.

---

# 29. Expense Line Security

Expense lines must be protected against:

- Negative values
- Hidden amount manipulation
- Invalid category changes
- Unauthorized tax changes
- Invalid allocation changes
- Cross-tenant references
- Unsupported currency conversion

All totals must be recalculated server-side.

---

# 30. Direct Expense Security

Direct expense controls must detect:

- Duplicate invoices
- Duplicate receipts
- Duplicate payment references
- Unauthorized suppliers
- Closed accounting periods
- Personally paid expense misuse
- Amounts above policy limits
- Split transactions intended to bypass approval thresholds

---

# 31. Split Transaction Detection

The system should identify transactions with similar:

- Requester
- Supplier
- Expense date
- Category
- Purpose
- Amount
- Project
- Payment method

that may have been split to avoid:

- Approval limits
- Procurement thresholds
- Receipt requirements
- Budget controls

A suspected split should create a warning or exception.

---

# 32. Staff Advance Security

Before approving or disbursing an advance, the system must validate:

- Employee is active.
- Employee is eligible for advances.
- No prohibited overdue accountability exists.
- Amount is within policy.
- Purpose is valid.
- Required approvals are complete.
- Budget reservation remains valid.
- Payment destination is approved.

---

# 33. Advance Disbursement Security

Disbursement controls include:

- Approved advance required
- Remaining approved balance validation
- Payment destination validation
- Duplicate submission protection
- Idempotency key
- Maker-checker controls
- High-value approval
- Finance Engine confirmation
- Immutable payment reference after confirmation

The frontend must not directly mark an advance as disbursed.

---

# 34. Payment Destination Security

Employee payment details should be sourced from an approved master record.

Where manual details are allowed:

- Additional approval may be required.
- The destination must be verified.
- The reason must be captured.
- Changes must be audited.
- Sensitive account details must be masked.

Example:

```text
Bank Account: ****4821
Mobile Number: +256 *** *** 827
```

---

# 35. Accountability Security

Accountability submission must protect against:

- False receipts
- Duplicate receipts
- Altered invoices
- Expenses outside the activity period
- Prohibited categories
- Unsupported amounts
- Related-party transactions
- Personal expenses
- Currency manipulation
- Receipt reuse across claims
- Submission after closure without approval

---

# 36. Accountability Review Security

Reviewers must not:

- Overwrite submitted amounts without history.
- Delete rejected lines.
- Approve their own accountability.
- Approve beyond their authority.
- Change verified documents without audit.
- Close settlement before required refunds or reimbursements are resolved.

Line-level decisions must be traceable.

---

# 37. Document Authenticity Controls

Supporting documents may be evaluated using:

- File hash
- Metadata
- Upload timestamp
- Duplicate hash matching
- Receipt number
- Invoice number
- Merchant
- Amount
- Transaction date
- Document verification
- Manual reviewer confirmation

The platform may later support automated document analysis, but automated results should not be treated as final approval without tenant policy.

---

# 38. Expense Claim Security

Claims must validate:

- Employee identity
- Claim eligibility
- Claim submission period
- Duplicate document use
- Personal payment evidence
- Expense category
- Policy limits
- Budget or project context
- Previous reimbursement status

An approved claim cannot be reimbursed twice.

---

# 39. Reimbursement Security

Before creating a reimbursement request:

- Source claim or accountability must be approved.
- Approved reimbursement amount must be positive.
- No successful reimbursement must already exist.
- Employee payment details must be valid.
- Idempotency must be enforced.

Retrying a failed request must reuse the same business reference.

---

# 40. Refund Security

Uploading evidence of a refund must not mark it as received.

Refund confirmation must require:

- Authorized Finance or Cashier role
- Valid receipt reference
- Finance Engine confirmation where applicable
- Amount validation
- Payment date validation
- Idempotency
- Audit event

Refund receipts must not exceed the outstanding amount without controlled handling.

---

# 41. Recovery Security

Recovery actions must validate:

- Approved recovery obligation
- Employee identity
- Outstanding amount
- Recovery method
- Required legal or HR approval
- Payroll authorization
- Installment limits
- Protected earnings rules where applicable

The Expenses Management Engine must not directly change payroll deductions.

---

# 42. Petty Cash Fund Security

Petty cash fund setup must validate:

- Company
- Branch
- Custodian
- Currency
- Authorized float
- Linked cash account
- Approval
- Custodian permissions

A fund must not become active until opening funding is confirmed.

---

# 43. Petty Cash Balance Security

Petty cash balance changes must occur only through approved transaction types:

- Opening float
- Funding
- Payment
- Refund
- Replenishment
- Return
- Adjustment
- Variance
- Closure

Direct editing of balance fields must be prohibited.

Balances should be derived or reconciled from immutable transactions.

---

# 44. Petty Cash Voucher Security

A petty cash payment must validate:

- Active fund
- Authorized custodian
- Approved voucher where required
- Sufficient balance
- Transaction limit
- Expense policy
- Payee details
- Duplicate voucher
- Required evidence

A voucher must not be paid twice.

---

# 45. Petty Cash Reconciliation Security

Reconciliation must include:

- Expected balance
- Actual count
- Outstanding vouchers
- Refunds
- Variance
- Counted by
- Reviewed by
- Approved by

The custodian must not provide final approval.

Completed reconciliation records must be immutable.

---

# 46. Cash Count Security

For higher-risk cash operations, the platform may require:

- Dual cash count
- Denomination capture
- Independent witness
- Timestamp
- Location
- Supporting image
- Session or device information

Cash counts must not expose unnecessary personal data.

---

# 47. Operational Fund Transfer Security

Before approval, validate:

- Source eligibility
- Destination eligibility
- Available balance
- Transfer limit
- Currency
- Purpose
- User authority
- Duplicate request
- Source and destination separation

After dispatch, source amount must not be silently edited.

---

# 48. Transfer Receipt Security

Receipt confirmation must validate:

- Assigned recipient
- Dispatch exists
- Transfer is in transit
- Amount received
- Receipt reference
- Destination
- Version
- No previous completed receipt

A mismatch must create a variance instead of automatically completing the transfer.

---

# 49. Custodian Handover Security

Custodian handover must require:

- Outgoing custodian confirmation
- Incoming custodian confirmation
- Independent approval
- Cash count
- Outstanding voucher review
- Variance review
- Effective handover date

Historical custodian assignments must remain preserved.

---

# 50. Travel Security

Travel request controls may validate:

- Employee eligibility
- Destination
- travel dates
- policy limits
- project authorization
- travel class
- per diem eligibility
- overlapping trips
- duplicate travel claims
- duty-of-care requirements

Sensitive itinerary information must only be visible to authorized users.

---

# 51. Per Diem Security

Per diem calculations must use:

- Approved rate
- Effective date
- Employee grade
- Destination
- Eligible days
- Meal deductions
- Accommodation treatment
- Currency

Manual overrides require:

- Permission
- Reason
- Audit record
- Approval where configured

---

# 52. Mileage Security

Mileage claims should validate:

- Valid journey date
- Approved rate
- Distance
- Vehicle type
- Duplicate route
- Related travel request
- Employee eligibility

Manually altered mileage rates must require elevated permission.

---

# 53. Budget Override Security

A budget override request must capture:

- Source transaction
- Requested amount
- Available amount
- Variance
- Justification
- Approver
- Approval scope
- Expiry or conditions

An override must not silently modify the official budget.

---

# 54. Policy Exception Security

Policy exception approval must validate:

- Policy violated
- Transaction
- User justification
- Severity
- Approver authority
- Conditions
- Expiry
- Supporting documents

Repeated exceptions by the same user, category, or department should be reportable.

---

# 55. Expense Policy Administration Security

Policy administrators may:

- Create drafts
- Test rules
- Submit for approval
- Activate approved policies
- Retire policies

They should not automatically approve their own policy changes.

Changes affecting active transactions should require controlled effective dates.

---

# 56. Configuration Security

Configuration changes must be permission-controlled for:

- Expense categories
- Limits
- Advance rules
- Accountability deadlines
- Per diem rates
- Mileage rates
- Petty cash limits
- Transfer types
- Document requirements
- Recovery rules
- Settlement rules

Every configuration change must be audited.

---

# 57. Reference Data Security

Shared values managed by the Reference Data Engine must not be duplicated or modified through unrestricted expense screens.

Examples include:

- Currency
- Country
- Department
- Cost center
- Project
- Employee grade
- Tax code
- Payment method

---

# 58. Document Security

Expense documents must be stored through the Document Management Engine.

Security must include:

- Private storage
- Tenant-aware paths
- Secure upload
- Malware scanning where available
- File-type validation
- File-size limits
- Signed temporary access URLs
- Permission checks before download
- Version history
- Retention controls
- Audit logging

Raw public URLs must not be used for confidential documents.

---

# 59. Allowed File Types

Tenant policy may allow:

```text
PDF
JPEG
PNG
WebP
DOCX
XLSX
CSV
```

Executable and dangerous file types must be blocked.

Filename validation must prevent:

- Path traversal
- Script injection
- Invalid Unicode tricks
- Unsupported extensions
- Double-extension bypass

---

# 60. Document Access

Document access must derive from:

- Parent expense record access
- Document type
- User role
- Workflow involvement
- Confidentiality level

A user must not access a document merely because they know its storage identifier.

---

# 61. Sensitive Document Classification

Documents may be classified as:

- General
- Internal
- Confidential
- Restricted
- Audit-only

Examples of restricted content may include:

- Employee bank details
- Payroll recovery records
- Fraud investigation documents
- Identification documents
- Legal recovery documents
- Confidential travel documents

---

# 62. Personal Data Protection

Expense records may contain:

- Employee names
- Contact details
- Bank details
- Mobile money details
- Travel information
- Identification documents
- Receipts
- Location details
- Employment information

The engine must apply:

- Data minimization
- Purpose limitation
- Access restriction
- Secure transmission
- Secure storage
- Retention controls
- Masking
- Audit logging

---

# 63. Data Masking

Sensitive values should be masked where full visibility is unnecessary.

Examples:

```text
Bank Account: ****4821
Mobile Money Number: +256 *** *** 827
National ID: CF**********9
```

Unmasking should require a specific permission and audit event.

---

# 64. Encryption

The platform must use encryption:

## In Transit

```text
TLS 1.2 or higher
```

## At Rest

Database, storage, backup, and infrastructure encryption should be enabled through the platform provider.

Highly sensitive application-level data may use additional encryption where required.

---

# 65. Secrets Management

Secrets must not be stored in:

- Source code
- Frontend environment files
- Browser storage
- Database configuration tables
- Logs

Secrets must use an approved secrets manager or protected environment configuration.

Examples include:

- Service credentials
- Payment integration keys
- Document signing keys
- Webhook secrets
- Encryption keys

---

# 66. API Security

Every API endpoint must validate:

- Authentication
- Tenant
- Company
- Branch
- Permission
- Record ownership
- Workflow assignment
- Status
- Version
- Input schema
- Rate limits
- Idempotency where required

Use Zod or equivalent backend validation for request payloads.

---

# 67. API Input Validation

Input validation must prevent:

- SQL injection
- Command injection
- JSON injection
- Cross-site scripting
- Oversized payloads
- Invalid UUIDs
- Negative monetary values
- Unsupported currencies
- Invalid dates
- Invalid status transitions
- Unauthorized foreign references

---

# 68. Mass Assignment Protection

APIs must explicitly whitelist writable fields.

The client must not be allowed to set protected fields such as:

```text
tenant_id
approved_amount
approved_by
status_code
workflow_instance_id
finance_transaction_reference_id
recovered_amount
refunded_amount
version
created_by
```

These must be controlled server-side.

---

# 69. Status Transition Security

Status must not be directly editable.

Transitions must use commands such as:

```text
submitRequisition
approveRequisition
rejectRequisition
disburseAdvance
submitAccountability
approveAccountability
confirmRefund
completeReimbursement
reconcilePettyCash
dispatchFundTransfer
receiveFundTransfer
reopenExpense
reverseExpense
```

Each command must validate the allowed transition.

---

# 70. Optimistic Concurrency

Sensitive updates must include the expected record version.

Example:

```sql
update staff_advances
set
    status_code = 'disbursed',
    version = version + 1
where id = :id
  and tenant_id = :tenant_id
  and version = :expected_version;
```

Concurrency conflicts must not be silently overwritten.

---

# 71. Idempotency

Idempotency is mandatory for:

- Advance disbursement
- Reimbursement
- Refund receipt confirmation
- Payroll recovery request
- Petty cash replenishment
- Procurement conversion
- Operational fund dispatch
- Operational fund receipt
- Reversal
- Finance posting requests

Idempotency keys must be unique within the tenant.

---

# 72. Replay Protection

Integration commands should include:

- Idempotency key
- Timestamp
- Correlation ID
- Event ID
- Signature where applicable

Duplicate or stale requests must be rejected or safely return the original result.

---

# 73. Integration Security

System integrations must use:

- Secure service identities
- Least-privilege credentials
- Signed webhooks where applicable
- TLS
- Idempotency
- Correlation IDs
- Schema validation
- Retry controls
- Dead-letter handling
- Audit logging

---

# 74. Finance Engine Integration Security

The Expenses Management Engine may request:

- Payment
- Refund receipt confirmation
- Reimbursement
- Petty cash funding
- Reversal
- Journal reference
- Exchange-rate confirmation

It must not directly:

- Insert General Ledger entries
- Update bank balances
- Mark a payment complete without confirmation
- Change accounting periods
- Alter official exchange differences

---

# 75. Payroll Engine Integration Security

Payroll recovery requests must include:

- Employee
- Approved recovery amount
- Currency
- Recovery reason
- Start period
- Installment instructions
- Approval references
- Idempotency key

Payroll responses must be validated before updating recovered balances.

---

# 76. Procurement Integration Security

Procurement conversion must validate:

- Approved source requisition
- Eligible lines
- Amount
- Budget context
- Company
- Branch
- Supplier requirement
- Idempotency

The system must prevent duplicate purchase requisitions.

---

# 77. Event Security

Events must include:

```json
{
  "event_id": "uuid",
  "event_type": "expense.advance.disbursed",
  "event_version": 1,
  "tenant_id": "uuid",
  "company_id": "uuid",
  "aggregate_id": "uuid",
  "correlation_id": "uuid",
  "actor_id": "uuid",
  "occurred_at": "timestamp",
  "payload": {}
}
```

Consumers must validate:

- Event schema
- Event version
- Tenant
- Event ID
- Source
- Signature where used

---

# 78. Outbox Security

Outbox events must:

- Be created in the same transaction as the business change.
- Be immutable after creation.
- Prevent unauthorized payload modification.
- Store publication attempts.
- Avoid sensitive data unless required.
- Be protected from frontend access.

---

# 79. Search Security

Search results must respect:

- Tenant
- Company
- Branch
- Permission
- Employee self-service rules
- Document classification

Search indexes must not become a path to bypass RLS.

Sensitive fields should not be indexed unless required.

---

# 80. Reporting Security

Reports must enforce:

- Row-level authorization
- Field-level masking
- Company scope
- Branch scope
- Employee scope
- Export permission

A user allowed to view a dashboard may not automatically be allowed to export raw employee data.

---

# 81. Export Security

Exports must be controlled because they may contain sensitive financial data.

Controls include:

- Dedicated export permission
- Export audit log
- Row limit
- Field masking
- Secure generated file
- Temporary download URL
- Automatic expiration
- Watermark where appropriate

---

# 82. Realtime Security

Supabase Realtime subscriptions must:

- Respect tenant access.
- Respect record authorization.
- Avoid exposing broad tables.
- Use secure filtered channels.
- Revalidate after tenant switching.
- Stop subscriptions when access is revoked.

Realtime must not bypass RLS.

---

# 83. Notification Security

Notifications must avoid exposing excessive sensitive data.

Example notification:

```text
Your expense requisition REQ-2026-00124 was approved.
```

Avoid including:

- Full bank details
- Full payroll recovery details
- Confidential fraud information
- Sensitive supporting documents
- Unmasked personal data

Notification deep links must still require authorization.

---

# 84. Fraud Risk Indicators

The engine should support risk indicators such as:

- Duplicate receipt
- Duplicate invoice
- Reused document hash
- Repeated round amounts
- Split transactions
- Expense outside working dates
- Expense after employee termination
- Excessive missing receipts
- Repeated policy exceptions
- Merchant and employee relationship concern
- Expense just below approval threshold
- Duplicate mileage route
- Overlapping travel claims
- Repeated petty cash shortages
- Frequent cash adjustments
- High number of reversals
- Unusual after-hours activity
- Rapid changes to payment details
- Multiple failed payment attempts

---

# 85. Fraud Review Workflow

```text
Risk Indicator Detected

↓

Assign Risk Level

↓

Low Risk
├── Log Warning
└── Continue

Medium Risk
├── Require Reviewer Confirmation
└── Continue or Block

High Risk
├── Suspend Transaction
├── Create Exception
├── Notify Authorized Reviewer
└── Require Formal Resolution
```

Risk indicators must not automatically accuse a user of fraud.

---

# 86. High-Risk Transaction Controls

High-risk actions may require:

- MFA
- Dual approval
- Reauthentication
- Reason
- Supporting documents
- Restricted role
- Independent review
- Notification to Finance
- Enhanced audit logging

Examples include:

- Large cash advance
- Petty cash shortage write-off
- Manual payment destination
- High-value fund transfer
- Reversal after posting
- Payroll recovery
- Multiple policy overrides
- Reopening a closed accountability

---

# 87. Velocity Controls

The system may limit:

- Number of disbursement attempts
- Number of refunds recorded
- Number of payment detail changes
- Number of policy overrides
- Number of high-value transactions
- Number of exports
- Number of failed approval actions

Suspicious activity should trigger alerts.

---

# 88. Rate Limiting

Rate limiting should apply to:

- Login attempts
- Document uploads
- Payment requests
- Disbursement requests
- Export generation
- Search
- Integration retries
- Approval endpoints

Financial action endpoints should have stricter limits than read endpoints.

---

# 89. Audit Logging

The Activity & Audit Engine must record:

- Record creation
- Field changes
- Submission
- Approval
- Partial approval
- Return
- Rejection
- Cancellation
- Disbursement
- Refund confirmation
- Reimbursement
- Recovery
- Reconciliation
- Fund transfer dispatch
- Fund transfer receipt
- Policy override
- Budget override
- Reopening
- Reversal
- Permission changes
- Configuration changes
- Export
- Sensitive document access
- Sensitive data unmasking

---

# 90. Audit Event Contents

Audit records should include:

```text
Tenant
Company
Branch
Entity Type
Entity ID
Action
Actor
Impersonator, if any
Previous Values
New Values
Reason
Workflow Step
Amount
Currency
Timestamp
Correlation ID
Session ID
IP Context
Device Context
Integration Reference
```

---

# 91. Immutable Audit Trail

Audit records must not be editable through normal application functionality.

Corrections to audit information must use:

- Additional audit event
- Security administrator procedure
- Documented reason
- Independent authorization

---

# 92. Administrative Impersonation

Where support impersonation exists:

- It must require elevated permission.
- It must display a visible banner.
- It must be time-limited.
- It must record the administrator and impersonated user.
- Sensitive financial actions may be prohibited.
- Every action must be specially audited.

---

# 93. Record Immutability

Finalized records must be protected.

Protected statuses include:

```text
approved
paid
disbursed
settled
reconciled
closed
reversed
```

Corrections must use:

- Adjustment
- Reopening
- Reversal
- Linked correction record

Original financial history must remain available.

---

# 94. Deletion Security

Physical deletion must be prohibited for:

- Approved requisitions
- Disbursements
- Accountabilities
- Claims
- Reimbursements
- Refund receipts
- Recoveries
- Petty cash transactions
- Reconciliations
- Fund transfers
- Audit records
- Integration records

Draft deletion may be allowed under controlled conditions.

---

# 95. Reopening Security

Reopening requires:

- Specific permission
- Valid reason
- Supporting evidence
- Approval workflow
- Impact assessment
- Audit event
- Restricted editable fields

Reopening must not erase previous approval or closure history.

---

# 96. Reversal Security

Reversal requires:

- Original completed transaction
- No existing full reversal
- Appropriate permission
- Approval
- Finance confirmation where applicable
- Linked reversal record
- Budget impact handling
- Audit event

The original transaction must remain immutable.

---

# 97. Backup Security

Expense data must be included in platform backup procedures.

Backups must support:

- Encryption
- Access restriction
- Retention policy
- Recovery testing
- Geographic resilience where required
- Audit of restore actions

---

# 98. Data Recovery

Recovery objectives must be defined for:

- Business records
- Documents
- Audit history
- Integration events
- Policy configuration
- Petty cash transactions
- Financial references

Restoration must preserve tenant isolation.

---

# 99. Retention Security

Retention must consider:

- Financial law
- Tax law
- Employment law
- Donor requirements
- Project requirements
- Contract requirements
- Audit requirements
- Tenant policies

Retention expiry must not automatically permit deletion of legally protected records.

---

# 100. Legal Hold

Authorized users may place records under legal or audit hold.

A held record must not be:

- Deleted
- Archived beyond access
- Purged
- Altered outside controlled procedures

Legal hold actions must be audited.

---

# 101. Security Monitoring

Monitoring should detect:

- Repeated access denials
- Cross-tenant access attempts
- Unusual export activity
- Unusual payment attempts
- Multiple failed MFA attempts
- Unexpected service-role use
- High reversal activity
- Repeated policy overrides
- Excessive document access
- High-value transaction spikes
- Repeated petty cash variances
- Suspicious account changes

---

# 102. Security Alerts

Alerts may be generated for:

- Unauthorized approval attempt
- Cross-tenant query attempt
- Duplicate payment request
- Payment destination change
- Repeated failed disbursement
- High-value cash transfer
- Large cash variance
- Service credential misuse
- Audit log failure
- Malware detection
- Unusual export
- Suspicious impersonation

---

# 103. Incident Response

Security incidents may include:

- Unauthorized data access
- Fraudulent expense
- Duplicate disbursement
- Document tampering
- Credential compromise
- Cross-tenant exposure
- Cash shortage
- Integration compromise
- Malicious upload

Incident handling should follow:

```text
Detect

↓

Contain

↓

Preserve Evidence

↓

Assess Impact

↓

Notify Authorized Stakeholders

↓

Remediate

↓

Recover

↓

Document Lessons Learned
```

---

# 104. Evidence Preservation

During an investigation, preserve:

- Audit logs
- Record versions
- Documents
- File hashes
- Payment references
- Workflow history
- Login context
- Device information
- Integration messages
- Notifications
- Export history

Evidence access must be restricted.

---

# 105. Error Security

Errors shown to users must not expose:

- SQL statements
- Database schema
- Service credentials
- Stack traces
- Storage paths
- Internal service URLs
- Other tenant IDs
- Security rules

Detailed diagnostic information should remain in protected logs.

---

# 106. Logging Security

Logs must not contain:

- Passwords
- Access tokens
- Refresh tokens
- Full bank account numbers
- Full mobile money numbers
- Private encryption keys
- Sensitive document contents

Structured logs should include correlation IDs but minimize personal data.

---

# 107. Environment Separation

Development, testing, staging, and production environments must be separated.

Production data must not be copied into non-production environments without:

- Authorization
- Masking
- Encryption
- Secure transfer
- Retention controls

---

# 108. Test Data Security

Test environments should use:

- Synthetic users
- Synthetic bank details
- Synthetic receipts
- Synthetic employee data
- Non-production payment integrations

Real personal financial data should not be used unnecessarily.

---

# 109. Secure Development Requirements

Development must include:

- Code review
- Dependency scanning
- Secret scanning
- Static analysis
- Database migration review
- RLS testing
- Authorization testing
- Input-validation testing
- Integration contract testing
- Security regression testing

---

# 110. Dependency Security

Frontend and backend dependencies must be:

- Pinned or lockfile-controlled
- Regularly scanned
- Updated for critical vulnerabilities
- Limited to required packages
- Reviewed before introduction

Unmaintained financial or security packages should be avoided.

---

# 111. Database Migration Security

Database migrations must:

- Preserve RLS
- Preserve constraints
- Avoid unintended public access
- Include rollback planning
- Be reviewed for tenant safety
- Be tested with production-like volumes
- Avoid destructive changes without approved migration plans

---

# 112. RLS Testing

Automated tests must verify:

- Tenant A cannot access Tenant B.
- Company-restricted users cannot access unauthorized companies.
- Employees see only permitted personal records.
- Approvers see assigned tasks.
- Auditors receive intended read-only access.
- Unauthorized users cannot update protected fields.
- Service operations do not leak data.

---

# 113. Authorization Testing

Tests must cover:

- Every permission
- Every role
- Every workflow action
- Self-approval prevention
- Amount authority
- Branch restrictions
- Company restrictions
- Record ownership
- Reopening
- Reversal
- Export

---

# 114. Financial Integrity Testing

Security tests should verify:

- Duplicate disbursement prevention
- Duplicate reimbursement prevention
- Refund overpayment prevention
- Petty cash overspending prevention
- Fund transfer double receipt prevention
- Allocation integrity
- Concurrent update conflicts
- Idempotent retry behavior
- Immutable confirmed references

---

# 115. Penetration Testing

Periodic penetration testing should include:

- Authentication
- Authorization
- RLS
- API access
- File upload
- Signed URLs
- Tenant switching
- Search
- Reporting
- Realtime
- Integration endpoints
- Privilege escalation
- Business logic abuse

---

# 116. Security Configuration

Tenant-level security configuration may include:

- MFA requirements
- Approval thresholds
- Dual-control thresholds
- Receipt requirements
- Advance restrictions
- Petty cash limits
- Transfer limits
- Reauthentication rules
- Export permissions
- Retention periods
- Fraud alerts
- Session duration

Configuration must not weaken mandatory platform controls.

---

# 117. Security Acceptance Criteria

The Expenses Management Engine security implementation is accepted when:

- Every business table has RLS enabled.
- Cross-tenant access is blocked.
- Company and branch restrictions are enforced.
- Permissions are action-specific.
- Employee self-service access is limited to authorized records.
- Requesters cannot approve their own requests.
- Advance recipients cannot approve their own accountabilities.
- Petty cash custodians cannot approve their own reconciliations.
- Fund transfers separate approval, dispatch, and receipt.
- Financial actions use idempotency.
- Payment completion requires Finance Engine confirmation.
- Refund evidence does not automatically confirm receipt.
- Payroll deductions are executed only by the Payroll Engine.
- Finalized transactions cannot be physically deleted.
- Reopening and reversal require controlled authorization.
- Supporting documents use private storage and permission checks.
- Sensitive payment data is masked.
- Approval authority is validated at action time.
- Amounts are recalculated server-side.
- Duplicate and split-transaction risks are detectable.
- Audit logs capture every material action.
- Exports are permission-controlled and audited.
- Realtime, search, and reporting respect authorization.
- Service-role credentials are not exposed.
- Security monitoring and incident-response procedures are defined.
- Automated tests verify RLS, permissions, segregation, and financial integrity.

---

# 118. Security Summary

The Expenses Management Engine security architecture protects the complete operational spending lifecycle, including:

- Requisition
- Approval
- Disbursement
- Accountability
- Settlement
- Reimbursement
- Refund
- Recovery
- Petty Cash
- Travel
- Mileage
- Operational Fund Transfers
- Budget Overrides
- Policy Exceptions
- Adjustments
- Reopening
- Reversal

The security model combines:

- Authentication
- Tenant isolation
- Row Level Security
- Fine-grained authorization
- Segregation of duties
- Amount authority
- Fraud controls
- Secure documents
- Secure integrations
- Financial integrity
- Immutable audit history
- Monitoring
- Incident response

This ensures that expense operations remain secure, traceable, tenant-isolated, auditable, and suitable for enterprise deployment.

---

# 119. Next Document

The next document is:

**ACCEPTANCE.md**

It will define:

- Functional Acceptance Criteria
- Workflow Acceptance Criteria
- Database Acceptance Criteria
- UI Acceptance Criteria
- Security Acceptance Criteria
- Integration Acceptance Criteria
- Reporting Acceptance Criteria
- Performance Acceptance Criteria
- Accessibility Acceptance Criteria
- Multi-Tenant Acceptance Criteria
- End-to-End Test Scenarios
- Go-Live Readiness
