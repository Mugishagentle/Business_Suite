# Reporting Engine Database Specification

Version: 1.0

Status: Draft

Module: Reporting Engine

---

# 1. Purpose

This document defines the database structure for the Reporting Engine.

The engine provides centralized management of datasets, report definitions, dashboards, widgets, report execution history, exports, and report permissions across the Business Suite.

The design supports reusable datasets, tenant isolation, configurable reports, and future business intelligence capabilities.

---

# 2. Design Principles

The database shall be:

- Dataset-driven
- Multi-tenant
- Highly scalable
- Metadata-driven
- Permission-aware
- Auditable
- Extensible

Business modules expose approved datasets.

The Reporting Engine consumes datasets to generate reports and dashboards.

---

# 3. Core Tables

Version 1 consists of the following primary tables.

```text
report_datasets
        │
        ├──────────────┐
        │              │
        ▼              ▼
report_definitions   dashboard_widgets
        │
        ▼
report_executions

report_definitions
        │
        ▼
report_permissions

report_exports
```

Future versions may introduce:

- report_schedules
- report_subscriptions
- report_snapshots
- report_comments
- report_favorites

---

# 4. Table Responsibilities

## report_datasets

Defines reusable datasets.

Contains:

- Dataset metadata
- Source module
- Parameters
- Permissions

---

## report_definitions

Defines reports built from datasets.

Contains:

- Layout
- Filters
- Columns
- Grouping
- Sorting
- Visualization

---

## dashboard_widgets

Defines dashboard widgets linked to datasets or reports.

Supports:

- Charts
- Tables
- Summary Cards
- KPI Widgets

---

## report_executions

Stores report execution history.

Contains:

- User
- Parameters
- Execution Time
- Duration
- Status

---

## report_permissions

Defines report-specific permission overrides where required.

Platform Core remains the primary authorization provider.

---

## report_exports

Stores information about generated exports.

Examples:

- PDF
- Excel
- CSV

---

# 5. Database Tables

## 5.1 report_datasets

Defines reusable datasets exposed by business modules.

```text
report_datasets
```

| Column       | Type      | Notes                        |
| ------------ | --------- | ---------------------------- |
| id           | uuid      | Primary Key                  |
| tenant_id    | uuid      | Nullable for Global Datasets |
| dataset_code | text      | Unique Code                  |
| name         | text      | Dataset Name                 |
| module_code  | text      | CRM, Sales, HR               |
| service_name | text      | Dataset Service              |
| description  | text      | Optional                     |
| is_active    | boolean   | Default true                 |
| created_at   | timestamp | Required                     |

Business Rules

- Dataset codes must be unique.
- Business modules own their datasets.
- The Reporting Engine consumes datasets through the Service Layer.
- Datasets may be global or tenant-specific.

---

## 5.2 report_definitions

Defines reports built from datasets.

```text
report_definitions
```

| Column        | Type      | Notes                              |
| ------------- | --------- | ---------------------------------- |
| id            | uuid      | Primary Key                        |
| tenant_id     | uuid      | Nullable for Global Reports        |
| dataset_id    | uuid      | References report_datasets.id      |
| report_code   | text      | Unique Code                        |
| report_name   | text      | Display Name                       |
| report_type   | text      | Operational, Analytical, Dashboard |
| visualization | text      | Table, Chart, KPI                  |
| configuration | jsonb     | Report Configuration               |
| is_active     | boolean   | Default true                       |
| created_at    | timestamp | Required                           |

Business Rules

- Reports are metadata.
- Reports never store business data.
- One dataset may support many reports.

---

## 5.3 dashboard_widgets

Defines widgets displayed on dashboards.

```text
dashboard_widgets
```

| Column         | Type    | Notes                            |
| -------------- | ------- | -------------------------------- |
| id             | uuid    | Primary Key                      |
| dashboard_code | text    | Dashboard Identifier             |
| report_id      | uuid    | References report_definitions.id |
| widget_type    | text    | Card, Chart, Table               |
| title          | text    | Widget Title                     |
| position       | integer | Display Order                    |
| width          | integer | Grid Width                       |
| height         | integer | Grid Height                      |
| configuration  | jsonb   | Widget Settings                  |

Business Rules

- Widgets use reports as their data source.
- Widget layout is configurable.
- Dashboards should be responsive.

---

## 5.4 report_executions

Stores report execution history.

```text
report_executions
```

| Column            | Type      | Notes                            |
| ----------------- | --------- | -------------------------------- |
| id                | uuid      | Primary Key                      |
| tenant_id         | uuid      | Required                         |
| report_id         | uuid      | References report_definitions.id |
| executed_by       | uuid      | Platform User                    |
| parameters        | jsonb     | Execution Parameters             |
| execution_time_ms | integer   | Duration                         |
| status            | text      | Success, Failed                  |
| created_at        | timestamp | Required                         |

Business Rules

- Every report execution is recorded.
- Parameters are stored for audit and troubleshooting.
- Execution history is immutable.

---

## 5.5 report_permissions

Stores report-specific permission overrides.

```text
report_permissions
```

| Column         | Type      | Notes                            |
| -------------- | --------- | -------------------------------- |
| id             | uuid      | Primary Key                      |
| report_id      | uuid      | References report_definitions.id |
| principal_type | text      | User, Role, Permission           |
| principal_id   | uuid      | Related Identifier               |
| can_view       | boolean   | Default false                    |
| can_export     | boolean   | Default false                    |
| created_at     | timestamp | Required                         |

Business Rules

- Platform permissions remain the default.
- Overrides are used only when required.
- Deny rules take precedence.

---

## 5.6 report_exports

Stores metadata for generated report exports.

```text
report_exports
```

| Column              | Type      | Notes                           |
| ------------------- | --------- | ------------------------------- |
| id                  | uuid      | Primary Key                     |
| tenant_id           | uuid      | Required                        |
| report_execution_id | uuid      | References report_executions.id |
| export_format       | text      | PDF, Excel, CSV                 |
| storage_key         | text      | Export Storage Reference        |
| file_size           | bigint    | Bytes                           |
| generated_by        | uuid      | Platform User                   |
| generated_at        | timestamp | Required                        |
| expires_at          | timestamp | Optional                        |

Business Rules

- Exports should reference generated files, not business data.
- Export files may have an expiration policy.
- Business modules must not generate exports directly.

---

## 5.3 dashboards

Defines dashboards that group report widgets.

```text
dashboards
```

| Column         | Type      | Notes                          |
| -------------- | --------- | ------------------------------ |
| id             | uuid      | Primary Key                    |
| tenant_id      | uuid      | Nullable for Global Dashboards |
| dashboard_code | text      | Unique Code                    |
| name           | text      | Dashboard Name                 |
| module_code    | text      | CRM, Sales, Finance, HR        |
| description    | text      | Optional                       |
| is_default     | boolean   | Default false                  |
| is_active      | boolean   | Default true                   |
| configuration  | jsonb     | Layout and settings            |
| created_at     | timestamp | Required                       |

### Business Rules

- Dashboards may be global or tenant-specific.
- A module may have one or many dashboards.
- One dashboard may be marked as the default dashboard.
- Dashboard layout and widget configuration should be stored in the `configuration` field.
- Dashboards contain widgets but do not store business data directly.

---

## 5.4 dashboard_widgets

Defines widgets displayed on dashboards.

```text
dashboard_widgets
```

| Column        | Type      | Notes                            |
| ------------- | --------- | -------------------------------- |
| id            | uuid      | Primary Key                      |
| dashboard_id  | uuid      | References dashboards.id         |
| report_id     | uuid      | References report_definitions.id |
| widget_type   | text      | Card, Chart, Table, KPI          |
| title         | text      | Widget Title                     |
| position      | integer   | Display Order                    |
| width         | integer   | Grid Width                       |
| height        | integer   | Grid Height                      |
| configuration | jsonb     | Widget Settings                  |
| created_at    | timestamp | Required                         |

### Business Rules

- Every widget belongs to one dashboard.
- Widgets use reports as their data source.
- Widgets should not query business modules directly.
- Widget layout should be configurable.
- Dashboards should be responsive across supported devices.

---

## 5.5 report_executions

Stores report execution history.

```text
report_executions
```

| Column            | Type      | Notes                            |
| ----------------- | --------- | -------------------------------- |
| id                | uuid      | Primary Key                      |
| tenant_id         | uuid      | Required                         |
| report_id         | uuid      | References report_definitions.id |
| executed_by       | uuid      | Platform User                    |
| parameters        | jsonb     | Execution Parameters             |
| execution_time_ms | integer   | Duration                         |
| status            | text      | Success, Failed                  |
| created_at        | timestamp | Required                         |

### Business Rules

- Every report execution is recorded.
- Parameters are stored for audit and troubleshooting.
- Execution history is immutable.

---

## 5.6 report_permissions

Stores report-specific permission overrides.

```text
report_permissions
```

| Column         | Type      | Notes                            |
| -------------- | --------- | -------------------------------- |
| id             | uuid      | Primary Key                      |
| report_id      | uuid      | References report_definitions.id |
| principal_type | text      | User, Role, Permission           |
| principal_id   | uuid      | Related Identifier               |
| can_view       | boolean   | Default false                    |
| can_export     | boolean   | Default false                    |
| created_at     | timestamp | Required                         |

### Business Rules

- Platform permissions remain the default authorization mechanism.
- Permission overrides are used only where required.
- Deny rules always take precedence over allow rules.

---

## 5.7 report_exports

Stores metadata for generated report exports.

```text
report_exports
```

| Column              | Type      | Notes                           |
| ------------------- | --------- | ------------------------------- |
| id                  | uuid      | Primary Key                     |
| tenant_id           | uuid      | Required                        |
| report_execution_id | uuid      | References report_executions.id |
| export_format       | text      | PDF, Excel, CSV                 |
| storage_key         | text      | Export Storage Reference        |
| file_size           | bigint    | Bytes                           |
| generated_by        | uuid      | Platform User                   |
| generated_at        | timestamp | Required                        |
| expires_at          | timestamp | Optional                        |

### Business Rules

- Export records store metadata only.
- Export files should be managed through the Document Management Engine or secure storage service.
- Export files may expire based on tenant policy.
- Business modules must never generate exports directly.
- Export downloads must respect report permissions.\

---

# 6. Reference Data Usage

The Reporting Engine should use the Reference Data Engine for configurable values.

Examples include:

- Report Types
- Visualization Types
- Export Formats
- Execution Status
- Widget Types

Examples:

| Reference Group     | Example Values                                |
| ------------------- | --------------------------------------------- |
| Report Types        | Operational, Analytical, Dashboard            |
| Visualization Types | Table, Card, Bar Chart, Line Chart, Pie Chart |
| Export Formats      | PDF, Excel, CSV                               |
| Execution Status    | Pending, Running, Success, Failed             |
| Widget Types        | KPI, Chart, Table, Summary Card               |

This prevents hardcoded reporting values.

---

# 7. Constraints

The following constraints should be enforced.

## Report Datasets

```sql
UNIQUE (tenant_id, dataset_code)
```

## Report Definitions

```sql
UNIQUE (tenant_id, report_code)
```

## Dashboards

```sql
UNIQUE (tenant_id, dashboard_code)
```

---

# 8. Indexing

Recommended indexes:

- tenant_id
- module_code
- dataset_code
- report_code
- dashboard_code
- report_type
- status
- created_at

Composite indexes:

```text
(tenant_id, module_code)

(tenant_id, report_type)

(report_id, created_at)

(executed_by, created_at)

(dashboard_id, position)
```

These indexes optimize:

- Report execution
- Dashboard loading
- Report history
- Dashboard rendering
- Export retrieval

---

# 9. Row Level Security

The Reporting Engine must enforce tenant isolation.

Rules:

- Users may only access reports belonging to their tenant.
- Users may only execute reports they are authorized to run.
- Users may only view dashboards they have permission to access.
- Exports must respect report permissions.

RLS must apply to:

- report_datasets
- report_definitions
- dashboards
- dashboard_widgets
- report_executions
- report_permissions
- report_exports

---

# 10. Dataset Rules

Datasets are the only approved source of report data.

Rules:

- Reports must consume datasets.
- Dashboards must consume reports or datasets.
- Business modules expose datasets through the Service Layer.
- Reports must never execute arbitrary SQL.
- Dataset logic belongs to the owning module.

---

# 11. Export Rules

Generated exports shall follow these rules.

- Exports are generated from report results.
- Export files should be stored securely.
- Export downloads must be authorized.
- Export files may expire according to tenant policy.
- Export generation must be recorded.

---

# 12. Seed Data

Default report types:

- Operational
- Analytical
- Dashboard

Default visualization types:

- Table
- KPI Card
- Bar Chart
- Line Chart
- Pie Chart
- Area Chart

Default export formats:

- PDF
- Excel
- CSV

---

# 13. Implementation Rules

The database implementation shall follow:

- UUID Primary Keys
- Foreign Key Constraints
- Tenant Isolation
- Row Level Security
- Service Layer Architecture
- Metadata-driven Reports
- Immutable Execution History

Business modules must never expose database tables directly to the Reporting Engine.

---

# 14. Conclusion

The Reporting Engine database provides a scalable, dataset-driven, and tenant-aware foundation for reporting across Business Suite.

By separating datasets, reports, dashboards, widgets, executions, permissions, and exports, the platform supports reusable analytics, secure reporting, consistent calculations, and future business intelligence capabilities without duplicating reporting logic.
