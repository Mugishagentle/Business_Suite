# Document Numbering Engine User Interface Specification

Version: 1.0

Status: Approved

Module: Document Numbering Engine

---

# 1. Purpose

This document defines the user interface standards for the Document Numbering Engine.

The engine provides administrators with a centralized interface for configuring and monitoring document numbering across the Business Suite platform.

---

# 2. Design Principles

The interface shall be:

- Simple
- Consistent
- Fast
- Responsive
- Easy to configure
- Easy to understand

Configuration should require minimal technical knowledge.

---

# 3. Navigation

The Document Numbering Engine should appear under:

```text
Platform Administration

↓

Document Numbering
```

Navigation Items:

- Dashboard
- Numbering Series
- Reservations
- History
- Settings

Navigation must respect user permissions.

---

# 4. Dashboard

The dashboard provides an overview of numbering activity.

Widgets include:

- Active Numbering Series
- Reserved Numbers
- Numbers Generated Today
- Expired Reservations
- Sequence Resets
- Recent Activity

Quick Actions:

- Create Numbering Series
- View Reservations
- View History

---

# 5. Numbering Series Screen

The main screen should organize Numbering Series by module.

Example:

```text
Platform Core
    Tenant Numbers
    User Numbers

Workflow Engine
    Workflow References

CRM
    Customer Numbers

Sales
    Quotations
    Sales Orders
    Invoices

Finance
    Journal Numbers
    Payment Numbers
```

Selecting a module displays its Numbering Series.

The right panel displays a DataTable.

Columns:

- Name
- Series Code
- Document Type
- Prefix
- Scope
- Reset Rule
- Status

Toolbar:

- Search
- Filter
- Refresh
- Export
- Add Series

Row Actions:

- View
- Edit
- Activate
- Deactivate
- Preview
- View History

This should be the primary administration screen.

---

# 6. Numbering Series Management

Administrators manage Numbering Series through a DataTable.

## List View

Display:

- Name
- Series Code
- Module
- Document Type
- Prefix
- Scope
- Reset Rule
- Status

Toolbar:

- Search
- Filter
- Refresh
- Export
- Add Series

Row Actions:

- View
- Edit
- Activate
- Deactivate
- Preview
- History

---

# 7. Create / Edit Numbering Series

Create and edit operations should use AppModal.

## General Information

Fields:

- Module
- Document Type
- Series Name
- Series Code
- Description

---

## Number Format

Fields:

- Prefix
- Format Template
- Number Length

Supported Placeholders:

- {PREFIX}
- {YEAR}
- {FY}
- {MONTH}
- {DAY}
- {BRANCH}
- {NUMBER}

A live preview should update as the administrator changes the format.

---

## Numbering Rules

Fields:

- Scope
- Reset Rule
- Reservation Timeout
- Status

---

Buttons

- Save
- Cancel

Rules

- Save uses application validation.
- HTML `required` attributes must not be used.
- Save button displays a loading spinner.
- Validation messages appear beside affected fields.

---

# 8. Number Preview

Administrators should preview generated numbers before saving.

Example

Configuration

```text
Prefix

INV

Format

{PREFIX}-{FY}-{NUMBER}

Digits

6
```

Preview

```text
INV-2026-000001
```

Preview must never consume a sequence.

---

# 9. Reservation Management

Administrators may monitor reserved numbers.

Columns:

- Generated Number
- Series
- Reserved By
- Entity
- Reserved At
- Expires At
- Status

Filters:

- Series
- Status
- Date

Actions:

- View
- Release Reservation
- Refresh

Released reservations remain available in history.

---

# 10. Number History

Displays numbering activity.

Columns:

- Date
- Generated Number
- Series
- Action
- User
- Status

Filters:

- Module
- Series
- Date
- Action

History is read-only.

Administrators may export history.

---

# 11. Tenant Numbering Rules

Each tenant owns its own Numbering Series and numbering sequences.

When a new tenant is provisioned, the platform automatically creates a default set of Numbering Series for that tenant.

Each tenant maintains its own independent numbering.

Example:

Tenant A

```text
Invoice

INV-2026-000001
INV-2026-000002
INV-2026-000003
```

Tenant B

```text
Invoice

INV-2026-000001
INV-2026-000002
```

This is correct because document numbers are unique within the tenant.

Business modules must always request numbers using the active tenant context.

---

# 12. Default Numbering Series

During tenant provisioning, the system should create default Numbering Series.

Examples include:

## Platform Core

- Tenant Number
- User Number

---

## Workflow Engine

- Workflow Reference Number

---

## CRM

- Customer Number
- Lead Number
- Opportunity Number

---

## Sales

- Quotation Number
- Sales Order Number
- Invoice Number
- Receipt Number
- Credit Note Number

---

## Procurement

- Purchase Request Number
- RFQ Number
- Purchase Order Number
- Goods Received Note Number

---

## Inventory

- Item Number
- Stock Adjustment Number
- Stock Transfer Number
- Stock Count Number

---

## Finance

- Journal Number
- Payment Voucher Number
- Receipt Number
- Budget Number

---

## HR

- Employee Number
- Leave Request Number
- Recruitment Number

Tenant Administrators may customize these Numbering Series without affecting other tenants.

---

# 13. Numbering Templates

To simplify configuration, administrators should be able to choose from predefined numbering templates.

Examples:

## Simple

```text
{PREFIX}-{NUMBER}
```

Produces:

```text
EMP-000001
```

---

## Financial Year

```text
{PREFIX}-{FY}-{NUMBER}
```

Produces:

```text
INV-2026-000001
```

---

## Branch Based

```text
{PREFIX}/{BRANCH}/{FY}/{NUMBER}
```

Produces:

```text
PO/KLA/2026/000001
```

---

## Custom

Administrators may define their own format using supported placeholders.

Supported placeholders:

- `{PREFIX}`
- `{YEAR}`
- `{FY}`
- `{MONTH}`
- `{DAY}`
- `{BRANCH}`
- `{NUMBER}`

The system should validate custom templates before allowing them to be saved.

---

# 14. Live Preview

The Numbering Series form should provide a real-time preview of the generated number.

Example configuration:

| Field  | Value                    |
| ------ | ------------------------ |
| Prefix | INV                      |
| Format | `{PREFIX}-{FY}-{NUMBER}` |
| Digits | 6                        |

Preview:

```text
INV-2026-000001
```

Rules:

- Preview must update immediately as configuration changes.
- Preview must never consume a sequence number.
- Preview should highlight invalid format templates.

---

# 15. Reservations Screen

Administrators should be able to monitor reserved numbers.

Display:

- Generated Number
- Module
- Document Type
- Numbering Series
- Reserved By
- Reserved At
- Expires At
- Status

Filters:

- Module
- Series
- Status
- Date

Actions:

- View Reservation
- Release Reservation
- Refresh

Released reservations remain available for audit purposes.

---

# 16. Numbering History

The Numbering History screen displays all numbering activity.

Display:

- Date & Time
- Generated Number
- Module
- Document Type
- Numbering Series
- Action
- User
- Status

Supported filters:

- Module
- Series
- Action
- Date Range
- User

History is read-only.

Users with appropriate permissions may export history.

---

# 17. UI States

Every screen within the Document Numbering Engine must support the following states.

## Loading

Display skeleton loaders or progress indicators.

---

## Empty

Example:

```text
No Numbering Series have been configured.
```

Action:

```text
Create Numbering Series
```

---

## Validation Error

Display field-level validation messages.

Examples:

- Series Code already exists.
- Invalid format template.
- Prefix is required.

---

## Error

Display a friendly error message with a retry option.

---

## No Permission

Display:

```text
You do not have permission to manage document numbering.
```

---

## Success

Display toast notifications.

Examples:

- Numbering Series created successfully.
- Numbering Series updated successfully.
- Reservation released successfully.

---

# 18. Shared Components

The Document Numbering Engine shall use shared Platform Framework components.

Examples:

- PageHeader
- DataTable
- AppModal
- AppButton
- AppInput
- AppSelect
- AppTextarea
- StatusBadge
- SearchToolbar
- FilterPanel
- EmptyState
- LoadingSkeleton
- ErrorState
- PermissionGate

Custom components should only be created where shared components cannot satisfy the requirement.

---

# 19. Responsive Design

The interface shall support:

- Desktop
- Tablet
- Mobile

Responsive behavior:

- Tables should support horizontal scrolling.
- Forms should stack vertically.
- Navigation panels may collapse into drawers.
- Primary actions should remain easily accessible.

---

# 20. Implementation Rules

The UI implementation shall comply with:

- `docs/architecture/DesignLanguage.md`
- `docs/architecture/CodingStandards.md`
- `docs/architecture/FolderStructure.md`

Implementation requirements:

- React + TypeScript
- Tailwind CSS
- shadcn/ui
- React Hook Form
- Zod
- React Router
- Service Layer Architecture
- Shared Platform Framework Components

---

# 21. Success Criteria

The Document Numbering Engine UI is considered complete when:

- Numbering Series can be managed.
- Live Preview functions correctly.
- Numbering Templates function correctly.
- Reservations can be monitored.
- History can be viewed.
- Permissions are enforced.
- All UI states are implemented.
- The interface is responsive.
- The interface follows the Business Suite Design Language.

---

# 22. Conclusion

The Document Numbering Engine UI provides a centralized and intuitive interface for configuring and monitoring document numbering across Business Suite.

By combining configurable numbering series, live previews, reusable templates, and tenant-specific ownership, the platform delivers a flexible, consistent, and enterprise-ready numbering experience for every business module.
