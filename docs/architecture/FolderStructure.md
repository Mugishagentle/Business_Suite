# Business Suite Folder Structure

## 1. Purpose

This document defines the official folder structure for the Business Suite project.

The objective is to ensure that every developer, AI assistant (Lovable, Cursor, ChatGPT), and future contributor follows the same project organization.

No module should introduce its own folder structure.

---

# 2. Architecture Principles

The project follows these principles:

- Feature-based architecture
- Modular development
- Separation of concerns
- Reusable shared components
- API-first architecture
- Scalability
- Maintainability
- Low coupling
- High cohesion

---

# 3. Root Project Structure

```text
business-suite/
│
├── docs/
├── prompts/
├── specs/
├── assets/
├── database/
├── scripts/
├── public/
├── src/
├── .github/
│
├── README.md
├── CHANGELOG.md
├── CONTRIBUTING.md
├── LICENSE
├── package.json
├── tsconfig.json
├── vite.config.ts
└── .gitignore
```

---

# 4. Documentation

```text
docs/

architecture/
product/
modules/
database/
api/
ui/
workflows/
decisions/
```

Contains all human-readable documentation.

---

# 5. AI Specifications

````text
specs/

platform-framework/
    README.md
    routing.md
    layouts.md
    navigation.md
    shared-components.md
    state-management.md
    services.md
    authentication.md
    theme.md

platform-core/
    README.md
    database.md
    ui.md
    workflows.md
    security.md
    acceptance.md

workflow-engine/
    README.md
    database.md
    ui.md
    workflows.md
    security.md
    acceptance.md

reference-data-engine/
    README.md
    database.md
    ui.md
    workflows.md
    security.md
    acceptance.md

notification-engine/
    README.md
    database.md
    ui.md
    workflows.md
    security.md
    acceptance.md

document-numbering-engine/
    README.md
    database.md
    ui.md
    workflows.md
    security.md
    acceptance.md

document-management-engine/
    README.md
    database.md
    ui.md
    workflows.md
    security.md
    acceptance.md

reporting-engine/
    README.md
    database.md
    ui.md
    workflows.md
    security.md
    acceptance.md

crm/
sales/
inventory/
procurement/
finance/
hr/
pos/
reports/

---

# 6. Prompts

```text
prompts/

lovable/
cursor/
architecture/
templates/
````

Contains reusable prompts used during development.

---

# 7. Assets

```text
assets/

branding/
logos/
icons/
mockups/
wireframes/
diagrams/
images/
```

Contains all design assets.

---

# 8. Database

```text
database/

schema/
migrations/
seeders/
backups/
```

Contains database-related artifacts.

---

# 9. Scripts

```text
scripts/

deployment/
development/
utilities/
```

Contains helper scripts.

---

# 10. Source Code

```text
src/

app/
components/
features/
hooks/
layouts/
providers/
routes/
services/
lib/
types/
utils/
styles/
```

---

# 11. App

```text
src/app/

App.tsx
main.tsx
router.tsx
```

Application entry point.

---

# 12. Components

```text
src/components/

ui/
forms/
tables/
cards/
dialogs/
charts/
navigation/
layout/
feedback/
```

Contains reusable UI components.

Business logic must never be placed here.

---

# 13. Features

Every platform service and business module lives inside:

````text
src/features/

platform-framework/
platform-core/
workflow-engine/
reference-data-engine/
notification-engine/
document-numbering-engine/
document-management-engine/
reporting-engine/

crm/
sales/
inventory/
procurement/
finance/
hr/
pos/
settings/




# 14. Standard Module Structure

Every module must follow this structure.

```text
module-name/

components/
pages/
services/
hooks/
types/
validators/
utils/
constants/
````

Example:

```text
inventory/

components/
pages/
services/
hooks/
types/
validators/
utils/
constants/
```

---

# 15. Pages

Pages represent screens.

Examples:

```text
CustomersPage.tsx

CreateInvoicePage.tsx

DashboardPage.tsx
```

---

# 16. Components

Components are reusable pieces used inside pages.

Examples:

```text
CustomerForm.tsx

InvoiceTable.tsx

ProductCard.tsx
```

---

# 17. Services

Every module communicates with Supabase through services.

Example:

```text
CustomerService.ts

InvoiceService.ts

StockService.ts
```

UI components must never communicate directly with Supabase.

---

# 18. Hooks

Hooks encapsulate reusable logic.

Examples:

```text
useCustomers.ts

useInvoices.ts

useCurrentTenant.ts
```

---

# 19. Validators

Contains Zod validation schemas.

Examples:

```text
customerSchema.ts

invoiceSchema.ts

itemSchema.ts
```

---

# 20. Types

Contains TypeScript models.

Examples:

```text
customer.ts

invoice.ts

tenant.ts
```

---

# 21. Utilities

Contains helper methods.

Examples:

```text
money.ts

dates.ts

formatters.ts

validators.ts
```

Utilities must never contain business logic.

---

# 22. Global Services

Some services are shared across all modules.

```text
src/services/

AuthService.ts

TenantService.ts

PermissionService.ts

NotificationService.ts

SubscriptionService.ts

StorageService.ts
```

---

# 23. Providers

```text
src/providers/

AuthProvider.tsx

TenantProvider.tsx

ThemeProvider.tsx

PermissionProvider.tsx
```

Contains global React Context providers.

---

# 24. Routes

```text
src/routes/

public.tsx

auth.tsx

tenant.tsx

admin.tsx
```

Routes are grouped by access level.

---

# 25. Library

```text
src/lib/

supabase.ts

config.ts

constants.ts

permissions.ts
```

Contains technical configuration.

---

# 26. Styles

```text
src/styles/

globals.css

themes.css
```

Contains global styling.

---

# 27. Naming Conventions

Folders

- kebab-case

Files

- PascalCase for React Components
- camelCase for utilities
- camelCase for services
- camelCase for hooks

Examples

```text
CustomerTable.tsx

InvoiceForm.tsx

moneyFormatter.ts

dateHelper.ts

useCustomers.ts
```

---

# 28. Module Independence

Each module must be independently maintainable.

A module should own:

- Pages
- Components
- Services
- Validation
- Types
- Utilities

Modules should communicate only through shared services or well-defined interfaces.

---

# 29. AI Development Rules

Lovable, Cursor, and any future AI assistant must follow this folder structure.

New modules must not invent new folder hierarchies.

If a new architectural requirement emerges, this document must be updated before implementation begins.

---

# 30. Conclusion

This folder structure is the official standard for the Business Suite project.

Its purpose is to provide a predictable, scalable, and maintainable codebase capable of supporting dozens of modules and multiple contributors over the lifetime of the platform.
