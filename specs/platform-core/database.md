# Platform Core Database Specification

Version: 1.0  
Status: Draft  
Module: Platform Core  
Database: PostgreSQL / Supabase

---

# 1. Purpose

This document defines the database structure for Platform Core.

It covers the tables, relationships, tenant isolation rules, indexes, auditing requirements, and data ownership model required to support:

- Multi-tenant SaaS architecture
- Workspace switching
- User memberships
- Authentication support
- Trial subscriptions
- Manual subscription activation
- Package-based module access
- Roles and permissions
- Email and SMS notifications
- Platform configuration
- Audit logging

This document must be used together with:

- `specs/platform-core/README.md`
- `docs/architecture/TechStack.md`
- `docs/architecture/FolderStructure.md`
- `docs/architecture/TenantArchitecture.md`
- `docs/architecture/Security.md`

---

# 2. Database Principles

The Platform Core database must follow these principles:

- Tenant isolation by design
- User accounts are global
- Users may belong to multiple tenants
- Authorization is tenant-specific
- Subscriptions belong to tenants
- Packages control module access
- Business data must always be tenant-owned
- Platform configuration must not be hardcoded
- Critical actions must be audited
- Soft deletes should be used where recovery is important
- Every table should have clear ownership and purpose

---

# 3. Naming Conventions

## Tables

Use plural snake_case table names.

Examples:

```text
tenants
tenant_members
packages
subscriptions
role_permissions
audit_logs
```

## Columns

Use snake_case column names.

Examples:

```text
tenant_id
created_at
updated_at
is_active
created_by
```

## Primary Keys

All application tables should use UUID primary keys.

```sql
id uuid primary key default gen_random_uuid()
```

## Foreign Keys

Foreign keys should use the referenced table name in singular form followed by `_id`.

Examples:

```text
tenant_id
user_id
role_id
package_id
subscription_id
```

## Timestamps

Standard timestamps:

```text
created_at
updated_at
deleted_at
```

Use `deleted_at` for soft deletes where applicable.

---

# 4. Database Ownership Model

The database has three categories of tables.

## 4.1 Global Platform Tables

These tables are platform-wide and do not belong to a tenant.

Examples:

- platform_users
- tenants
- packages
- modules
- platform_settings
- authentication_providers
- email_providers
- sms_providers

## 4.2 Tenant-Owned Tables

These tables belong to a specific tenant and must include `tenant_id`.

Examples:

- tenant_members
- branches
- roles
- permissions
- role_permissions
- subscriptions
- companies
- notifications

## 4.3 Audit Tables

These tables track actions across the platform.

Examples:

- audit_logs
- activity_logs

Audit records should include tenant context where applicable.

---

# 5. Core Table List

Platform Core Version 1 requires the following tables:

## Identity & Tenancy

- platform_users
- tenants
- tenant_members

## Subscription & Packages

- packages
- modules
- package_modules
- subscriptions

## Company & Branches

- companies
- branches

## Authorization

- roles
- permissions
- role_permissions

## Notifications

- notification_templates
- notifications
- email_providers
- sms_providers

## Platform Configuration

- platform_settings
- authentication_providers
- feature_flags

## Audit & Activity

- audit_logs
- activity_logs

---

---

# 5.1 Reference Tables

Reference tables store reusable lookup data used across the platform.

These tables help avoid repeated text values and make the system easier to scale across countries, currencies, and regions.

````text
user_codes

| Column      | Type      | Notes            |
| ----------- | --------- | ---------------- |
| id          | uuid      | Primary key      |
| code        | text      | Required, unique |
| name        | text      | Required         |
| description | text      | Optional         |
| scope       | text      | global, tenant   |
| tenant_id   | uuid      | Optional         |
| status      | text      | active, inactive |
| created_at  | timestamp | Required         |
| updated_at  | timestamp | Required         |

Examples:

SUBSCRIPTION_STATUS
TENANT_STATUS
USER_STATUS
BRANCH_STATUS
NOTIFICATION_CHANNEL
PERMISSION_ACTION
INDUSTRY_TYPE
SUPPORT_LEVEL

## 5.1.1 countries

Stores supported countries.

```text
countries
````

| Column                | Type      | Notes                   |
| --------------------- | --------- | ----------------------- |
| id                    | uuid      | Primary key             |
| name                  | text      | Required                |
| iso2                  | text      | Example: UG             |
| iso3                  | text      | Example: UGA            |
| phone_code            | text      | Example: +256           |
| default_currency_code | text      | Example: UGX            |
| default_timezone      | text      | Example: Africa/Kampala |
| status                | text      | active, inactive        |
| created_at            | timestamp | Required                |
| updated_at            | timestamp | Required                |

Rules:

- Countries are platform-wide.
- Countries do not belong to tenants.
- Tenants, companies, and branches should reference countries instead of storing country text.

---

## 5.1.2 currencies

Stores supported currencies.

```text
currencies
```

| Column         | Type      | Notes                    |
| -------------- | --------- | ------------------------ |
| id             | uuid      | Primary key              |
| code           | text      | Example: UGX, USD        |
| name           | text      | Example: Uganda Shilling |
| symbol         | text      | Example: UGX, $          |
| decimal_places | integer   | Default 2                |
| status         | text      | active, inactive         |
| created_at     | timestamp | Required                 |
| updated_at     | timestamp | Required                 |

Rules:

- Currencies are platform-wide.
- Packages, tenants, companies, and future finance modules should reference currencies.
- Currency values should not be stored as free text.

---

## 5.1.3 timezones

Stores supported time zones.

```text
timezones
```

| Column     | Type      | Notes                   |
| ---------- | --------- | ----------------------- |
| id         | uuid      | Primary key             |
| name       | text      | Example: Africa/Kampala |
| utc_offset | text      | Example: +03:00         |
| status     | text      | active, inactive        |
| created_at | timestamp | Required                |
| updated_at | timestamp | Required                |

Rules:

- Time zones are platform-wide.
- Tenants and companies should reference a timezone.
- Timezone values should not be hardcoded.

---

## 5.1.4 languages

Stores supported platform languages.

```text
languages
```

| Column     | Type      | Notes            |
| ---------- | --------- | ---------------- |
| id         | uuid      | Primary key      |
| code       | text      | Example: en      |
| name       | text      | Example: English |
| status     | text      | active, inactive |
| created_at | timestamp | Required         |
| updated_at | timestamp | Required         |

Rules:

- Languages are platform-wide.
- Users and companies may reference preferred languages.
- This supports future localization.

  5.1.6 user_code_values

Stores values under each user code group.

user_code_values

| Column       | Type      | Notes                    |
| ------------ | --------- | ------------------------ |
| id           | uuid      | Primary key              |
| user_code_id | uuid      | References user_codes.id |
| code         | text      | Required                 |
| name         | text      | Required                 |
| description  | text      | Optional                 |
| sort_order   | integer   | Optional                 |
| metadata     | jsonb     | Optional                 |
| status       | text      | active, inactive         |
| created_at   | timestamp | Required                 |
| updated_at   | timestamp | Required                 |

Examples:

For SUBSCRIPTION_STATUS:

TRIAL
ACTIVE
EXPIRED
SUSPENDED
CANCELLED

For PERMISSION_ACTION:

VIEW
CREATE
EDIT
DELETE
APPROVE
REJECT
EXPORT
PRINT
CONFIGURE

Rules:

Dropdown values should come from user_code_values.
Status values should come from user_code_values where practical.
User code groups may be global or tenant-specific.
Tenant-specific user codes must include tenant_id.
Do not hardcode dropdown values in the UI.

# 6. Identity & Tenancy Tables

## 6.1 platform_users

Stores global user profile information.

Authentication itself is handled by Supabase Auth, but this table stores additional platform profile data.

```text
platform_users
```

| Column             | Type      | Notes                                            |
| ------------------ | --------- | ------------------------------------------------ |
| id                 | uuid      | Primary key. Should match Supabase auth user id. |
| first_name         | text      | Required                                         |
| middle_name        | text      | Optional                                         |
| last_name          | text      | Required                                         |
| display_name       | text      | Optional                                         |
| email              | text      | Required, unique                                 |
| phone              | text      | Optional                                         |
| profile_photo_url  | text      | Optional                                         |
| default_tenant_id  | uuid      | Optional                                         |
| preferred_language | text      | Optional                                         |
| timezone           | text      | Optional                                         |
| status             | text      | active, suspended, disabled                      |
| mfa_enabled        | boolean   | Default false                                    |
| last_login_at      | timestamp | Optional                                         |
| created_at         | timestamp | Required                                         |
| updated_at         | timestamp | Required                                         |
| deleted_at         | timestamp | Optional                                         |

Rules:

- A person must exist only once in `platform_users`.
- A user may belong to many tenants through `tenant_members`.
- Do not duplicate users when they are invited to another workspace.
- Email must be unique.
- User authentication is global.

---

## 6.2 tenants

Stores registered businesses or organizations.

```text
tenants
```

| Column        | Type      | Notes                                        |
| ------------- | --------- | -------------------------------------------- |
| id            | uuid      | Primary key                                  |
| name          | text      | Legal business name                          |
| trading_name  | text      | Optional                                     |
| slug          | text      | Unique workspace slug                        |
| industry      | text      | Optional                                     |
| country       | text      | Required                                     |
| currency      | text      | Required                                     |
| timezone      | text      | Required                                     |
| phone         | text      | Optional                                     |
| email         | text      | Required                                     |
| status        | text      | trial, active, expired, suspended, cancelled |
| owner_user_id | uuid      | References platform_users.id                 |
| created_at    | timestamp | Required                                     |
| updated_at    | timestamp | Required                                     |
| deleted_at    | timestamp | Optional                                     |

Rules:

- A tenant represents one business workspace.
- Tenant status controls overall workspace access.
- Every tenant must have one owner.
- Slug must be unique.
- A tenant must not access data belonging to another tenant.

---

## 6.3 tenant_members

Links users to tenants.

This table enables one user to belong to multiple workspaces.

```text
tenant_members
```

| Column     | Type      | Notes                               |
| ---------- | --------- | ----------------------------------- |
| id         | uuid      | Primary key                         |
| tenant_id  | uuid      | References tenants.id               |
| user_id    | uuid      | References platform_users.id        |
| role_id    | uuid      | References roles.id                 |
| status     | text      | pending, active, suspended, removed |
| is_owner   | boolean   | Default false                       |
| is_default | boolean   | Default false                       |
| invited_by | uuid      | References platform_users.id        |
| invited_at | timestamp | Optional                            |
| joined_at  | timestamp | Optional                            |
| created_at | timestamp | Required                            |
| updated_at | timestamp | Required                            |
| deleted_at | timestamp | Optional                            |

Rules:

- A user can belong to many tenants.
- A tenant can have many users.
- The combination of `tenant_id` and `user_id` must be unique.
- A user may have different roles in different tenants.
- Authorization is evaluated through this table.
- Workspace switching is based on active tenant memberships.

Recommended constraint:

```sql
unique (tenant_id, user_id)
```

---

# 7. Subscription & Package Tables

## 7.1 packages

Defines subscription packages.

```text
packages
```

| Column             | Type      | Notes                                |
| ------------------ | --------- | ------------------------------------ |
| id                 | uuid      | Primary key                          |
| name               | text      | Required                             |
| code               | text      | Unique package code                  |
| description        | text      | Optional                             |
| price              | numeric   | Optional for MVP                     |
| billing_cycle      | text      | monthly, quarterly, annually, custom |
| max_users          | integer   | Required                             |
| max_branches       | integer   | Required                             |
| storage_limit_mb   | integer   | Optional                             |
| support_level      | text      | Optional                             |
| is_trial_available | boolean   | Default true                         |
| trial_days         | integer   | Default 30                           |
| status             | text      | active, inactive                     |
| created_at         | timestamp | Required                             |
| updated_at         | timestamp | Required                             |
| deleted_at         | timestamp | Optional                             |

Rules:

- Packages are managed by the Super Administrator.
- Packages control tenant limits and module access.
- Package values must be configurable.
- Do not hardcode package names or limits.

---

## 7.2 modules

Defines system modules.

```text
modules
```

| Column      | Type      | Notes                  |
| ----------- | --------- | ---------------------- |
| id          | uuid      | Primary key            |
| name        | text      | Required               |
| code        | text      | Unique module code     |
| description | text      | Optional               |
| status      | text      | active, inactive       |
| is_core     | boolean   | True for Platform Core |
| sort_order  | integer   | Optional               |
| created_at  | timestamp | Required               |
| updated_at  | timestamp | Required               |

Rules:

- Platform Core must always be enabled.
- Business modules are enabled through packages.
- Modules should be configurable.

---

## 7.3 package_modules

Links packages to modules.

```text
package_modules
```

| Column     | Type      | Notes                  |
| ---------- | --------- | ---------------------- |
| id         | uuid      | Primary key            |
| package_id | uuid      | References packages.id |
| module_id  | uuid      | References modules.id  |
| is_enabled | boolean   | Default true           |
| created_at | timestamp | Required               |
| updated_at | timestamp | Required               |

Rules:

- A package can include many modules.
- A module can belong to many packages.
- Module access is determined from this table.

Recommended constraint:

```sql
unique (package_id, module_id)
```

---

## 7.4 subscriptions

Stores tenant subscription records.

```text
subscriptions
```

| Column          | Type      | Notes                                        |
| --------------- | --------- | -------------------------------------------- |
| id              | uuid      | Primary key                                  |
| tenant_id       | uuid      | References tenants.id                        |
| package_id      | uuid      | References packages.id                       |
| status          | text      | trial, active, expired, suspended, cancelled |
| starts_at       | timestamp | Required                                     |
| ends_at         | timestamp | Required                                     |
| trial_starts_at | timestamp | Optional                                     |
| trial_ends_at   | timestamp | Optional                                     |
| activated_by    | uuid      | References platform_users.id                 |
| activated_at    | timestamp | Optional                                     |
| notes           | text      | Optional                                     |
| created_at      | timestamp | Required                                     |
| updated_at      | timestamp | Required                                     |
| deleted_at      | timestamp | Optional                                     |

Rules:

- Each tenant must have one current subscription.
- New tenants start with a trial subscription.
- MVP subscriptions are manually activated by Super Administrator.
- Online payment is not part of Version 1.
- Subscription history should be preserved.
- Do not delete historical subscription records.

---

# 8. Company & Branch Tables

## 8.1 companies

Stores the official business profile for a tenant.

For Version 1, each tenant has one company record.

```text
companies
```

| Column                     | Type      | Notes                 |
| -------------------------- | --------- | --------------------- |
| id                         | uuid      | Primary key           |
| tenant_id                  | uuid      | References tenants.id |
| legal_name                 | text      | Required              |
| trading_name               | text      | Optional              |
| registration_number        | text      | Optional              |
| tax_identification_number  | text      | Optional              |
| industry                   | text      | Optional              |
| logo_url                   | text      | Optional              |
| email                      | text      | Optional              |
| phone                      | text      | Optional              |
| website                    | text      | Optional              |
| address_line_1             | text      | Optional              |
| address_line_2             | text      | Optional              |
| city                       | text      | Optional              |
| district                   | text      | Optional              |
| country                    | text      | Required              |
| currency                   | text      | Required              |
| timezone                   | text      | Required              |
| language                   | text      | Optional              |
| financial_year_start_month | integer   | Optional              |
| date_format                | text      | Optional              |
| number_format              | text      | Optional              |
| status                     | text      | active, inactive      |
| created_at                 | timestamp | Required              |
| updated_at                 | timestamp | Required              |
| deleted_at                 | timestamp | Optional              |

Rules:

- A tenant must have one company record in Version 1.
- The company record stores the business identity shown to users.
- Future versions may allow one tenant to own multiple companies.
- Company records must always be tenant-scoped.

Recommended constraint:

```sql
unique (tenant_id)
```

---

## 8.2 branches

Stores branches or business locations under a tenant/company.

```text
branches
```

| Column          | Type      | Notes                        |
| --------------- | --------- | ---------------------------- |
| id              | uuid      | Primary key                  |
| tenant_id       | uuid      | References tenants.id        |
| company_id      | uuid      | References companies.id      |
| name            | text      | Required                     |
| code            | text      | Optional                     |
| email           | text      | Optional                     |
| phone           | text      | Optional                     |
| address         | text      | Optional                     |
| city            | text      | Optional                     |
| district        | text      | Optional                     |
| country         | text      | Optional                     |
| manager_user_id | uuid      | References platform_users.id |
| is_head_office  | boolean   | Default false                |
| status          | text      | active, inactive             |
| created_at      | timestamp | Required                     |
| updated_at      | timestamp | Required                     |
| deleted_at      | timestamp | Optional                     |

Rules:

- Branches belong to a tenant and company.
- The number of active branches must respect the tenant package limit.
- A tenant should have at least one default/head office branch.
- Users may later be assigned to specific branches.

---

# 9. Authorization Tables

## 9.1 roles

Stores tenant-specific roles.

```text
roles
```

| Column         | Type      | Notes                 |
| -------------- | --------- | --------------------- |
| id             | uuid      | Primary key           |
| tenant_id      | uuid      | References tenants.id |
| name           | text      | Required              |
| code           | text      | Optional              |
| description    | text      | Optional              |
| is_system_role | boolean   | Default false         |
| status         | text      | active, inactive      |
| created_at     | timestamp | Required              |
| updated_at     | timestamp | Required              |
| deleted_at     | timestamp | Optional              |

Rules:

- Roles belong to tenants.
- The same role name may exist in different tenants.
- System roles may be seeded during tenant creation.
- Tenant roles must not affect other tenants.

Recommended constraint:

```sql
unique (tenant_id, name)
```

---

## 9.2 permissions

Stores available platform permissions.

```text
permissions
```

| Column      | Type      | Notes                                                                 |
| ----------- | --------- | --------------------------------------------------------------------- |
| id          | uuid      | Primary key                                                           |
| module_id   | uuid      | References modules.id                                                 |
| name        | text      | Required                                                              |
| code        | text      | Required, unique                                                      |
| description | text      | Optional                                                              |
| action      | text      | view, create, edit, delete, approve, reject, export, print, configure |
| status      | text      | active, inactive                                                      |
| created_at  | timestamp | Required                                                              |
| updated_at  | timestamp | Required                                                              |

Rules:

- Permissions are platform-defined.
- Permissions should be grouped by module.
- Permissions should be reusable across tenants.
- Permissions should not be hardcoded in UI pages.

---

## 9.3 role_permissions

Links roles to permissions.

```text
role_permissions
```

| Column        | Type      | Notes                     |
| ------------- | --------- | ------------------------- |
| id            | uuid      | Primary key               |
| tenant_id     | uuid      | References tenants.id     |
| role_id       | uuid      | References roles.id       |
| permission_id | uuid      | References permissions.id |
| created_at    | timestamp | Required                  |
| updated_at    | timestamp | Required                  |

Rules:

- Role permissions are tenant-scoped.
- A role may have many permissions.
- A permission may belong to many roles.
- Permission assignment must only apply within the active tenant.

Recommended constraint:

```sql
unique (tenant_id, role_id, permission_id)
```

---

# 10. Optional Future Authorization Tables

These tables are not required for the first implementation unless needed.

## 10.1 user_permissions

Allows direct permission overrides for a specific user inside a tenant.

Use only if role-based permissions are not enough.

```text
user_permissions
```

| Column        | Type      | Notes                        |
| ------------- | --------- | ---------------------------- |
| id            | uuid      | Primary key                  |
| tenant_id     | uuid      | References tenants.id        |
| user_id       | uuid      | References platform_users.id |
| permission_id | uuid      | References permissions.id    |
| effect        | text      | allow, deny                  |
| created_at    | timestamp | Required                     |
| updated_at    | timestamp | Required                     |

Recommendation:

- Do not implement this in Version 1 unless absolutely necessary.
- Prefer role-based permissions first.

---

# 11. Notification Tables

## 11.1 notification_templates

Stores reusable email and SMS templates.

```text
notification_templates
```

| Column                 | Type      | Notes              |
| ---------------------- | --------- | ------------------ |
| id                     | uuid      | Primary key        |
| code                   | text      | Required, unique   |
| name                   | text      | Required           |
| channel                | text      | email, sms, in_app |
| subject                | text      | Required for email |
| body                   | text      | Required           |
| available_placeholders | jsonb     | Optional           |
| status                 | text      | active, inactive   |
| created_at             | timestamp | Required           |
| updated_at             | timestamp | Required           |

Rules:

- Templates are managed by the Super Administrator.
- Templates should support placeholders.
- Templates must not be hardcoded.

---

## 11.2 notifications

Stores sent or pending notifications.

```text
notifications
```

| Column        | Type      | Notes                                  |
| ------------- | --------- | -------------------------------------- |
| id            | uuid      | Primary key                            |
| tenant_id     | uuid      | Optional, references tenants.id        |
| user_id       | uuid      | Optional, references platform_users.id |
| channel       | text      | email, sms, in_app                     |
| recipient     | text      | Email address or phone number          |
| subject       | text      | Optional                               |
| message       | text      | Required                               |
| status        | text      | pending, sent, failed, read            |
| error_message | text      | Optional                               |
| sent_at       | timestamp | Optional                               |
| read_at       | timestamp | Optional                               |
| created_at    | timestamp | Required                               |
| updated_at    | timestamp | Required                               |

Rules:

- Platform notifications may exist without tenant_id.
- Tenant-specific notifications should include tenant_id.
- Failed notifications should store error details.

---

## 11.3 email_providers

Stores SMTP or email service configuration.

```text
email_providers
```

| Column             | Type      | Notes            |
| ------------------ | --------- | ---------------- |
| id                 | uuid      | Primary key      |
| name               | text      | Required         |
| provider_type      | text      | smtp, api        |
| host               | text      | Optional         |
| port               | integer   | Optional         |
| username           | text      | Optional         |
| password_encrypted | text      | Optional         |
| api_key_encrypted  | text      | Optional         |
| sender_name        | text      | Optional         |
| sender_email       | text      | Optional         |
| encryption         | text      | ssl, tls, none   |
| is_default         | boolean   | Default false    |
| status             | text      | active, inactive |
| created_at         | timestamp | Required         |
| updated_at         | timestamp | Required         |

Rules:

- Email provider credentials must be encrypted.
- Only one provider should be marked default.
- Email configuration should be managed by Super Administrator.

---

## 11.4 sms_providers

Stores SMS gateway configuration.

```text
sms_providers
```

| Column               | Type      | Notes            |
| -------------------- | --------- | ---------------- |
| id                   | uuid      | Primary key      |
| name                 | text      | Required         |
| provider_type        | text      | api              |
| api_url              | text      | Optional         |
| api_key_encrypted    | text      | Optional         |
| sender_id            | text      | Optional         |
| default_country_code | text      | Optional         |
| is_default           | boolean   | Default false    |
| status               | text      | active, inactive |
| created_at           | timestamp | Required         |
| updated_at           | timestamp | Required         |

Rules:

- SMS provider credentials must be encrypted.
- Only one provider should be marked default.
- SMS provider should be configurable.

---

# 12. Platform Configuration Tables

## 12.1 platform_settings

Stores global platform settings.

```text
platform_settings
```

| Column      | Type      | Notes            |
| ----------- | --------- | ---------------- |
| id          | uuid      | Primary key      |
| key         | text      | Required, unique |
| value       | jsonb     | Required         |
| description | text      | Optional         |
| category    | text      | Optional         |
| is_public   | boolean   | Default false    |
| created_at  | timestamp | Required         |
| updated_at  | timestamp | Required         |

Examples:

```text
platform_name
trial_duration_days
default_package_id
maintenance_mode
support_email
session_timeout_minutes
password_policy
```

Rules:

- Platform settings must not be hardcoded.
- Only safe values should be marked public.
- Sensitive values should not be stored here unless encrypted.

---

## 12.2 authentication_providers

Stores enabled authentication methods.

```text
authentication_providers
```

| Column        | Type      | Notes                                              |
| ------------- | --------- | -------------------------------------------------- |
| id            | uuid      | Primary key                                        |
| name          | text      | Required                                           |
| code          | text      | Required, unique                                   |
| provider_type | text      | email_password, google, microsoft, magic_link, mfa |
| configuration | jsonb     | Optional                                           |
| is_enabled    | boolean   | Default false                                      |
| sort_order    | integer   | Optional                                           |
| created_at    | timestamp | Required                                           |
| updated_at    | timestamp | Required                                           |

Rules:

- Authentication providers must be configurable.
- Disabled providers should not appear on login or registration pages.
- Configuration may include client IDs, redirect URLs, or provider-specific settings.
- Sensitive values should be encrypted where applicable.

---

## 12.3 feature_flags

Stores platform-wide feature toggles.

```text
feature_flags
```

| Column      | Type      | Notes                           |
| ----------- | --------- | ------------------------------- |
| id          | uuid      | Primary key                     |
| key         | text      | Required, unique                |
| name        | text      | Required                        |
| description | text      | Optional                        |
| is_enabled  | boolean   | Default false                   |
| scope       | text      | global, tenant                  |
| tenant_id   | uuid      | Optional, references tenants.id |
| created_at  | timestamp | Required                        |
| updated_at  | timestamp | Required                        |

Rules:

- Feature flags may be global or tenant-specific.
- Tenant-specific flags must include tenant_id.
- Feature flags should not replace proper package/module access control.

---

# 13. Audit & Activity Tables

## 13.1 audit_logs

Stores immutable audit records for security, compliance, and traceability.

```text
audit_logs
```

| Column          | Type      | Notes                                                        |
| --------------- | --------- | ------------------------------------------------------------ |
| id              | uuid      | Primary key                                                  |
| tenant_id       | uuid      | Optional, references tenants.id                              |
| user_id         | uuid      | Optional, references platform_users.id                       |
| module          | text      | Required                                                     |
| entity          | text      | Required                                                     |
| entity_id       | uuid      | Optional                                                     |
| action          | text      | create, update, delete, login, logout, approve, reject, etc. |
| previous_values | jsonb     | Optional                                                     |
| new_values      | jsonb     | Optional                                                     |
| ip_address      | text      | Optional                                                     |
| user_agent      | text      | Optional                                                     |
| created_at      | timestamp | Required                                                     |

Rules:

- Audit logs are immutable.
- Users must never edit or delete audit records.
- All critical platform operations should be audited.

---

## 13.2 activity_logs

Stores general user activity for reporting and monitoring.

```text
activity_logs
```

| Column     | Type      | Notes       |
| ---------- | --------- | ----------- |
| id         | uuid      | Primary key |
| tenant_id  | uuid      | Optional    |
| user_id    | uuid      | Optional    |
| module     | text      | Required    |
| activity   | text      | Required    |
| metadata   | jsonb     | Optional    |
| created_at | timestamp | Required    |

Examples:

- User logged in
- Workspace switched
- Customer created
- Invoice approved
- Report exported

Activity logs may be archived periodically.

---

# 14. Database Relationships

## Identity

```text
platform_users
        │
        │ 1
        │
        └───────────────∞ tenant_members
                            │
                            │
                            ▼
                         tenants
```

---

## Subscription

```text
packages
     │
     │ 1
     │
     └────────────∞ subscriptions
                        │
                        ▼
                    tenants
```

---

## Module Access

```text
packages
      │
      ▼
package_modules
      ▲
      │
modules
```

---

## Authorization

```text
roles
    │
    ▼
role_permissions
    ▲
    │
permissions
```

Tenant members receive permissions through roles.

---

## Company

```text
tenants
    │
    ▼
companies
    │
    ▼
branches
```

---

# 15. Row Level Security (RLS)

All tenant-owned tables must implement PostgreSQL Row Level Security.

The following tables require RLS:

- tenant_members
- companies
- branches
- subscriptions
- roles
- role_permissions
- notifications

Platform-wide tables such as `packages`, `modules`, and `platform_settings` do not require tenant-based RLS but should still enforce appropriate access controls.

Every query must execute within the context of the authenticated user's active tenant.

---

# 16. Indexing Strategy

Indexes should be created for:

### Primary Keys

All UUID primary keys.

### Foreign Keys

- tenant_id
- user_id
- role_id
- package_id
- module_id
- company_id
- subscription_id

### Lookup Columns

- email
- slug
- code
- status
- created_at

### Composite Indexes

Recommended examples:

```text
(tenant_id, status)

(tenant_id, created_at)

(tenant_id, role_id)

(tenant_id, user_id)

(package_id, module_id)
```

Indexes should be reviewed as the application grows.

---

# 17. Soft Deletes

Where recovery is important, use:

```text
deleted_at
```

instead of permanently deleting records.

Recommended tables:

- platform_users
- tenants
- companies
- branches
- tenant_members
- roles
- subscriptions

Do **not** soft delete:

- audit_logs
- activity_logs

Audit data should be preserved.

---

# 18. Seed Data

The initial database should seed the following data:

## Authentication Providers

- Email & Password
- Google
- Microsoft
- Magic Link

## Modules

- Platform Core
- CRM
- Sales
- Inventory
- Procurement
- Finance
- HR
- POS
- Reports

## Default Roles

- Workspace Owner
- Workspace Administrator
- Manager
- Viewer

## Default Permissions

Seed permissions for every module using standard actions:

- View
- Create
- Edit
- Delete
- Approve
- Reject
- Export
- Print
- Configure

---

## User Codes

Seed default user code groups and values for:

- Tenant Status
- Subscription Status
- User Status
- Branch Status
- Package Status
- Module Status
- Notification Channel
- Notification Status
- Permission Actions
- Industry Types
- Support Levels

# 19. Future Expansion

The database has been designed to support future enhancements without redesign.

Examples include:

- Multi-company tenants
- Online payments
- Mobile applications
- Public APIs
- API keys
- Webhooks
- Organization hierarchies
- AI assistants
- Workflow engine
- Marketplace integrations

Future business modules should extend the platform without modifying the core tenancy model.

---

# 20. Implementation Rules

All database development must follow these rules:

- Use UUID primary keys.
- Respect tenant isolation.
- Apply Row Level Security to tenant-owned tables.
- Use foreign key constraints.
- Use soft deletes where appropriate.
- Avoid hardcoded configuration.
- Keep authentication global and authorization tenant-specific.
- Preserve audit history.
- Follow naming conventions defined in this specification.

---

# 21. Conclusion

This document defines the official Platform Core database architecture for the Business Suite.

It provides a scalable, secure, and maintainable foundation for a multi-tenant SaaS platform.

All future modules—including CRM, Sales, Inventory, Finance, HR, Procurement, POS, and Reporting—must build upon this database architecture without violating the principles of tenant isolation, modularity, and configuration-driven development.
