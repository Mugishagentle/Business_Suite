# Search & Indexing Engine Database Specification

Version: 1.0  
Status: Draft  
Module: Search & Indexing Engine

---

# 1. Purpose

This document defines the database structure for the Search & Indexing Engine.

The engine provides centralized search indexing, searchable metadata, search execution history, and search analytics across Business Suite.

It supports global search, module search, document search, report search, permission-aware results, and tenant isolation.

---

# 2. Design Principles

The database shall be:

- Index-driven
- Tenant-aware
- Permission-aware
- Event-driven
- Fast to query
- Scalable
- Metadata-driven
- Extensible

Business modules publish events through the Platform Event Bus.

The Search & Indexing Engine updates the search index in response to those events.

---

# 3. Core Tables

Version 1 consists of the following primary tables.

````text
search_indexes
        │
        ▼
search_history
        │
        ▼
search_analytics

---

# 5. Database Tables

## 5.1 search_indexes

Stores searchable representations of business records.

```text
search_indexes
````

| Column           | Type      | Notes                                      |
| ---------------- | --------- | ------------------------------------------ |
| id               | uuid      | Primary Key                                |
| tenant_id        | uuid      | Required                                   |
| module_code      | text      | CRM, Sales, HR, Finance                    |
| entity_type      | text      | Customer, Invoice, Employee                |
| entity_id        | uuid      | Business Record ID                         |
| title            | text      | Primary display text                       |
| subtitle         | text      | Secondary display text                     |
| reference_number | text      | Optional business reference                |
| searchable_text  | text      | Combined searchable content                |
| keywords         | text[]    | Search keywords                            |
| status           | text      | Active, Archived, Deleted                  |
| metadata         | jsonb     | Additional searchable metadata             |
| route_path       | text      | Application navigation route               |
| classification   | text      | Public, Internal, Confidential, Restricted |
| last_indexed_at  | timestamp | Last successful indexing                   |
| created_at       | timestamp | Required                                   |
| updated_at       | timestamp | Required                                   |

### Business Rules

- Each search index record belongs to one tenant.
- Each search index record references exactly one business entity.
- Search indexes are updated only through Platform Event Bus subscribers.
- Business modules must never write directly to the search index.
- Search results must respect permissions and data classification.
- Search indexes should remain synchronized with the source business record.

---

## 5.2 search_history

Stores user search activity.

```text
search_history
```

| Column            | Type      | Notes                                    |
| ----------------- | --------- | ---------------------------------------- |
| id                | uuid      | Primary Key                              |
| tenant_id         | uuid      | Required                                 |
| user_id           | uuid      | Platform User                            |
| search_query      | text      | User-entered search text                 |
| search_scope      | text      | Global, Module, Entity, Document, Report |
| filters           | jsonb     | Applied filters                          |
| result_count      | integer   | Number of results returned               |
| execution_time_ms | integer   | Search duration                          |
| searched_at       | timestamp | Required                                 |

### Business Rules

- Search history is tenant-specific.
- Search history supports audit and analytics.
- Sensitive search queries may be masked according to security policy.
- Search history must not expose restricted information.

---

## 5.3 search_analytics

Stores aggregated search statistics.

```text
search_analytics
```

| Column                    | Type      | Notes                    |
| ------------------------- | --------- | ------------------------ |
| id                        | uuid      | Primary Key              |
| tenant_id                 | uuid      | Required                 |
| search_term               | text      | Search keyword or phrase |
| search_count              | integer   | Number of searches       |
| average_result_count      | integer   | Average results returned |
| average_execution_time_ms | integer   | Average search duration  |
| last_searched_at          | timestamp | Required                 |

### Business Rules

- Analytics are generated asynchronously.
- Analytics support reporting and platform optimization.
- Analytics should not expose individual user behavior unless explicitly authorized.
- Analytics data should remain tenant-aware.

---

## 5.4 search_index_jobs

Tracks indexing operations initiated by the Platform Event Bus.

```text
search_index_jobs
```

| Column        | Type      | Notes                                |
| ------------- | --------- | ------------------------------------ |
| id            | uuid      | Primary Key                          |
| tenant_id     | uuid      | Required                             |
| event_id      | uuid      | References platform_events.id        |
| module_code   | text      | Source Module                        |
| entity_type   | text      | Customer, Invoice, Employee          |
| entity_id     | uuid      | Business Record                      |
| operation     | text      | Create, Update, Delete, Rebuild      |
| status        | text      | Pending, Processing, Success, Failed |
| retry_count   | integer   | Default 0                            |
| error_message | text      | Optional                             |
| processed_at  | timestamp | Optional                             |
| created_at    | timestamp | Required                             |

### Business Rules

- Index jobs are created only by Platform Event Bus subscribers.
- Every indexing operation should be traceable to a Platform Event.
- Failed jobs may be retried according to platform retry policies.
- Job history supports troubleshooting and monitoring.

---

## 5.5 search_saved_searches

Stores reusable searches created by users.

```text
search_saved_searches
```

| Column       | Type      | Notes             |
| ------------ | --------- | ----------------- |
| id           | uuid      | Primary Key       |
| tenant_id    | uuid      | Required          |
| user_id      | uuid      | Platform User     |
| name         | text      | Saved Search Name |
| search_query | text      | Search Text       |
| search_scope | text      | Search Scope      |
| filters      | jsonb     | Saved Filters     |
| is_default   | boolean   | Default false     |
| created_at   | timestamp | Required          |
| updated_at   | timestamp | Required          |

### Business Rules

- Saved searches belong to the creating user.
- Users may define one default search per scope.
- Saved searches must respect permissions when executed.

---

## 5.6 search_suggestions

Stores search suggestions and autocomplete terms.

```text
search_suggestions
```

| Column           | Type      | Notes                           |
| ---------------- | --------- | ------------------------------- |
| id               | uuid      | Primary Key                     |
| tenant_id        | uuid      | Nullable for Global Suggestions |
| suggestion       | text      | Suggested Search                |
| module_code      | text      | Optional                        |
| popularity_score | integer   | Ranking Score                   |
| is_active        | boolean   | Default true                    |
| created_at       | timestamp | Required                        |

### Business Rules

- Suggestions may be generated automatically or manually.
- Suggestions should not expose confidential information.
- Suggestions should respect tenant boundaries where applicable.

---

# 6. Reference Data Usage

The Search & Indexing Engine should use the Reference Data Engine for configurable values.

Examples include:

- Search Scope
- Index Status
- Index Operation
- Classification
- Search Job Status

Examples:

| Reference Group   | Example Values                             |
| ----------------- | ------------------------------------------ |
| Search Scope      | Global, Module, Entity, Document, Report   |
| Index Status      | Active, Archived, Deleted                  |
| Index Operation   | Create, Update, Delete, Rebuild            |
| Search Job Status | Pending, Processing, Success, Failed       |
| Classification    | Public, Internal, Confidential, Restricted |

This prevents hardcoded search values.

---

# 7. Constraints

The following constraints should be enforced.

## Search Index

```sql
UNIQUE (tenant_id, module_code, entity_type, entity_id)
```

## Saved Searches

```sql
UNIQUE (tenant_id, user_id, name)
```

---

# 8. Indexing

Recommended indexes:

- tenant_id
- module_code
- entity_type
- entity_id
- reference_number
- status
- classification
- last_indexed_at

Full-text indexes should be created for:

- title
- subtitle
- searchable_text
- keywords

Composite indexes:

```text
(tenant_id, module_code)

(module_code, entity_type)

(status, classification)

(user_id, searched_at)

(event_id, status)
```

These indexes optimize:

- Global search
- Module search
- Entity lookup
- Autocomplete
- Index maintenance

---

# 9. Row Level Security

The Search & Indexing Engine must enforce tenant isolation.

Rules:

- Users may only search records belonging to their tenant.
- Search history must remain tenant-specific.
- Saved searches remain private unless explicitly shared.
- Search analytics should respect tenant boundaries.

RLS must apply to:

- search_indexes
- search_history
- search_analytics
- search_saved_searches
- search_index_jobs

---

# 10. Event Bus Integration

The Search & Indexing Engine updates its index through the Platform Event Bus.

Typical events include:

- CustomerCreated
- CustomerUpdated
- CustomerArchived
- InvoiceCreated
- InvoiceUpdated
- DocumentUploaded
- EmployeeCreated

Business Rules

- Business modules publish events.
- The Search & Indexing Engine subscribes to relevant events.
- Index updates occur asynchronously.
- Every indexing job references its originating Platform Event.

---

# 11. Search Processing Rules

Search execution shall follow these principles.

- Search the index only.
- Never query business tables directly.
- Apply permission filtering.
- Apply tenant filtering.
- Apply classification filtering.
- Rank results by relevance.

Search results should be returned in a consistent format.

---

# 12. Seed Data

Default Search Scopes:

- Global
- Module
- Entity
- Document
- Report

Default Index Operations:

- Create
- Update
- Delete
- Rebuild

Default Search Job Status:

- Pending
- Processing
- Success
- Failed

---

# 13. Implementation Rules

The database implementation shall follow:

- UUID Primary Keys
- Foreign Key Constraints
- Tenant Isolation
- Row Level Security
- Event-Driven Indexing
- Immutable Search History
- Service Layer Architecture

Business modules must never update search indexes directly.

All indexing operations must originate from Platform Event Bus subscribers.

---

# 14. Conclusion

The Search & Indexing Engine database provides a scalable, event-driven foundation for enterprise search across Business Suite.

By separating searchable indexes, search history, analytics, indexing jobs, saved searches, and suggestions while integrating with the Platform Event Bus, the platform delivers fast, secure, permission-aware, and tenant-aware search without coupling search functionality to individual business modules.
