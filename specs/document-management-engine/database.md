# Document Management Engine Database Specification

Version: 1.0

Status: Draft

Module: Document Management Engine

---

# 1. Purpose

This document defines the database structure for the Document Management Engine.

The engine provides centralized storage and management of business documents, metadata, document versions, permissions, ownership, and audit history across the Business Suite platform.

The design supports scalability, tenant isolation, document versioning, and future enterprise content management capabilities.

---

# 2. Design Principles

The database shall be:

- Document-centric
- Multi-tenant
- Highly scalable
- Storage-provider independent
- Auditable
- Secure
- Version-aware
- Metadata-driven

The database stores document information.

Actual files remain in the configured storage provider.

---

# 3. Core Tables

Version 1 consists of the following primary tables.

```text
documents
        │
        ├──────────────┐
        │              │
        ▼              ▼
document_versions   document_links
        │
        ▼
document_permissions

documents
        │
        ▼
document_history
```

Future versions may introduce:

- document_tags
- document_retention
- document_comments
- document_workflows

---

# 4. Table Responsibilities

## documents

Stores the logical document.

Contains:

- Ownership
- Metadata
- Category
- Current Version
- Status

---

## document_versions

Stores every uploaded version of the document.

Each replacement creates a new version.

The latest version becomes active.

---

## document_links

Connects documents to business records.

Examples:

- Customer
- Supplier
- Employee
- Invoice
- Purchase Order
- Workflow

---

## document_permissions

Defines document-specific permissions where required.

Most permissions inherit from Platform Core.

This table is used only when document-level security overrides are required.

---

## document_history

Stores immutable audit history.

Examples:

- Uploaded
- Downloaded
- Previewed
- Replaced
- Archived
- Deleted

---

# 5. Ownership

Every document belongs to a tenant.

Every document also belongs to a business entity.

Ownership consists of:

- Tenant
- Module
- Entity Type
- Entity Identifier

Examples:

CRM

↓

Customer

↓

customer_id

HR

↓

Employee

↓

employee_id

Finance

↓

Invoice

↓

invoice_id

Business modules store only the document identifier.

The Document Management Engine owns all remaining document information.

---

# 6. Database Tables

## 6.1 documents

Stores the logical business document.

```text
documents
```

| Column             | Type      | Notes                       |
| ------------------ | --------- | --------------------------- |
| id                 | uuid      | Primary Key                 |
| tenant_id          | uuid      | Required                    |
| module_code        | text      | Example: crm, hr, finance   |
| entity_type        | text      | Customer, Employee, Invoice |
| entity_id          | uuid      | Business Record Identifier  |
| category_id        | uuid      | Reference Data Category     |
| title              | text      | Document Title              |
| description        | text      | Optional                    |
| current_version_id | uuid      | Active Document Version     |
| status             | text      | Draft, Active, Archived     |
| created_by         | uuid      | Platform User               |
| created_at         | timestamp | Required                    |
| updated_at         | timestamp | Required                    |
| archived_at        | timestamp | Optional                    |

Business Rules

- Every document belongs to one tenant.
- Every document belongs to one business entity.
- Category comes from the Reference Data Engine.
- Current Version always points to the latest active version.

---

## 6.2 document_versions

Stores every uploaded version of a document.

```text
document_versions
```

| Column            | Type      | Notes                   |
| ----------------- | --------- | ----------------------- |
| id                | uuid      | Primary Key             |
| document_id       | uuid      | References documents.id |
| version_number    | integer   | Starts at 1             |
| original_filename | text      | User uploaded name      |
| stored_filename   | text      | Internal filename       |
| storage_provider  | text      | Supabase, S3, Azure     |
| storage_key       | text      | Storage Path            |
| mime_type         | text      | File MIME Type          |
| file_extension    | text      | pdf, docx, jpg          |
| file_size         | bigint    | Bytes                   |
| checksum          | text      | SHA256                  |
| uploaded_by       | uuid      | Platform User           |
| uploaded_at       | timestamp | Required                |
| is_current        | boolean   | Current Version         |

Business Rules

- Every replacement creates a new version.
- Previous versions remain immutable.
- Only one version may be current.

---

## 6.3 document_links

Links documents to business entities.

```text
document_links
```

| Column       | Type      | Notes                           |
| ------------ | --------- | ------------------------------- |
| id           | uuid      | Primary Key                     |
| document_id  | uuid      | References documents.id         |
| module_code  | text      | CRM, HR, Finance                |
| entity_type  | text      | Customer, Employee, Invoice     |
| entity_id    | uuid      | Business Record                 |
| relationship | text      | Primary, Attachment, Supporting |
| created_at   | timestamp | Required                        |

Business Rules

- One document may have multiple links where permitted.
- Links must always belong to the same tenant.
- Business modules retrieve documents through these links.

---

## 6.4 document_permissions

Stores document-specific permission overrides.

```text
document_permissions
```

| Column         | Type      | Notes                   |
| -------------- | --------- | ----------------------- |
| id             | uuid      | Primary Key             |
| document_id    | uuid      | References documents.id |
| principal_type | text      | User, Role, Department  |
| principal_id   | uuid      | Related Identifier      |
| can_view       | boolean   | Default false           |
| can_download   | boolean   | Default false           |
| can_replace    | boolean   | Default false           |
| can_delete     | boolean   | Default false           |
| created_at     | timestamp | Required                |

Business Rules

- Platform permissions remain the default.
- Overrides are only used when document-level security is required.
- Deny rules always take precedence.

---

## 6.5 document_history

Stores immutable audit history.

```text
document_history
```

| Column       | Type      | Notes                                            |
| ------------ | --------- | ------------------------------------------------ |
| id           | uuid      | Primary Key                                      |
| tenant_id    | uuid      | Required                                         |
| document_id  | uuid      | References documents.id                          |
| version_id   | uuid      | Optional                                         |
| action       | text      | Uploaded, Viewed, Downloaded, Replaced, Archived |
| performed_by | uuid      | Platform User                                    |
| metadata     | jsonb     | Optional                                         |
| created_at   | timestamp | Required                                         |

Business Rules

- History is append-only.
- History cannot be edited.
- History cannot be deleted.
- Every important action must be recorded.

---

# 7. Constraints

The following constraints should be enforced.

## Document Versions

Only one version of a document may be current.

Recommended rule:

```text
One document_id should have only one is_current = true.
```

---

## Document Links

A document link must reference a valid document.

Business modules should not store physical file paths.

They should store or retrieve:

```text
document_id
```

or query documents through:

```text
document_links
```

---

## Tenant Consistency

All linked records must belong to the same tenant.

Documents from one tenant must never be linked to another tenant's business records.

---

# 8. Indexing

Recommended indexes:

- tenant_id
- document_id
- current_version_id
- category_id
- module_code
- entity_type
- entity_id
- status
- created_at
- uploaded_at

Composite indexes:

```text
(tenant_id, module_code)

(tenant_id, entity_type, entity_id)

(document_id, version_number)

(document_id, is_current)

(tenant_id, status)
```

---

# 9. Row Level Security

The Document Management Engine must enforce Row Level Security.

Rules:

- Users may only access documents belonging to tenants where they have active membership.
- Users may only access documents they are authorized to view.
- Document downloads must verify permissions.
- Document previews must verify permissions.
- Document history must respect tenant isolation.

RLS must apply to:

- documents
- document_versions
- document_links
- document_permissions
- document_history

---

# 10. Storage Rules

The database must not store public file URLs.

Instead, store:

```text
storage_provider
storage_key
```

The Document Management Engine should generate signed URLs or download streams when access is authorized.

Rules:

- Files should be private by default.
- Signed URLs should expire.
- Business modules should never access storage providers directly.
- Storage provider changes should not affect business modules.

---

# 11. File Integrity

Each uploaded file should store a checksum.

Recommended field:

```text
checksum
```

Purpose:

- Detect duplicate files.
- Verify file integrity.
- Support future deduplication.
- Support audit validation.

---

# 12. Soft Deletes

Documents may be archived instead of permanently deleted.

Use:

```text
archived_at
```

Rules:

- Archived documents remain available for audit.
- Archived documents should not appear in normal active document lists.
- Document versions should not be deleted if they were previously used.
- Document history must never be deleted.

---

# 13. Seed Data

Default document categories should be seeded through the Reference Data Engine.

Examples:

## Platform Core

- Company Logo
- User Profile Photo

## CRM

- Customer Registration Document
- National ID
- TIN Certificate

## HR

- CV
- Employment Contract
- Academic Certificate
- Passport Photo

## Finance

- Invoice Attachment
- Receipt
- Payment Voucher Support

## Procurement

- Supplier Registration
- Quotation
- Purchase Order Attachment

## Workflow

- Supporting Document
- Approval Evidence

---

# 14. Implementation Rules

The database implementation must follow:

- UUID primary keys
- Foreign key constraints
- Tenant isolation
- Row Level Security
- Private storage by default
- Signed access URLs
- Immutable version history
- Immutable audit history
- Service Layer access only

Business modules must never store physical file paths or direct storage URLs.

---

# 15. Conclusion

The Document Management Engine database provides a secure, tenant-aware, document-centric structure for managing files across Business Suite.

By separating logical documents from physical file versions, the platform supports versioning, auditability, storage-provider independence, access control, and future enterprise content management capabilities.
