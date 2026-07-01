# Business Suite Technology Stack

## 1. Purpose

This document defines the official technology stack for the Business Suite platform.

Its purpose is to ensure that every developer, AI assistant (Lovable, Cursor, ChatGPT), and future contributor builds the platform using the same technologies and architectural principles.

No new technology should be introduced without first updating this document.

---

# 2. Technology Philosophy

The Business Suite is designed around the following principles:

- Simplicity over complexity
- Maintainability over trends
- Configuration over hardcoding
- API-first development
- Modular architecture
- Feature-based organization
- Cloud-native deployment
- Strong typing
- Scalability
- Security by default

Every technology selected must contribute to one or more of these principles.

---

# 3. Official Technology Stack

The following technologies are officially approved for the Business Suite.

| Layer            | Technology        |
| ---------------- | ----------------- |
| Frontend         | React             |
| Language         | TypeScript        |
| Build Tool       | Vite              |
| Styling          | Tailwind CSS      |
| UI Components    | shadcn/ui         |
| Forms            | React Hook Form   |
| Validation       | Zod               |
| Backend Platform | Supabase          |
| Database         | PostgreSQL        |
| Authentication   | Supabase Auth     |
| Storage          | Supabase Storage  |
| Hosting          | Vercel + Supabase |
| Version Control  | Git & GitHub      |

No alternative technology should be introduced without architectural approval.

---

# 4. Frontend

## Purpose

The frontend delivers the user interface for the Business Suite.

## Official Standard

The frontend must use:

- React
- TypeScript
- Vite

## Design Principles

The frontend must:

- Be component-driven
- Be modular
- Be reusable
- Be responsive
- Be strongly typed
- Support feature-based development

## Rules

Developers and AI assistants must:

- Build all UI using React.
- Use TypeScript for every new file.
- Avoid plain JavaScript where TypeScript is appropriate.
- Avoid introducing another frontend framework.

## AI Development Rules

Lovable and Cursor must generate React + TypeScript components only.

---

# 5. User Interface

## Official Standard

The user interface uses:

- Tailwind CSS
- shadcn/ui

## Design Principles

The UI should be:

- Clean
- Responsive
- Accessible
- Modern
- Consistent

## Rules

- Prefer existing shadcn components before creating new ones.
- Build reusable components.
- Avoid duplicate UI implementations.
- Follow the project Design System.

---

# 6. State Management

## Purpose

Manage application state using React's native capabilities.

## Official Standard

The project uses:

- React Context API
- React Hooks
- useState
- useReducer
- useEffect

## Design Principles

State should remain:

- Simple
- Predictable
- Easy to debug

## Rules

The project intentionally avoids introducing client-side state management libraries such as Redux, MobX, Zustand, or TanStack Query unless a future architectural decision explicitly requires them.

Server communication should be handled through service classes rather than client-side query caching.

---

# 7. API Communication

## Official Standard

Business Suite follows a Service Layer architecture.

UI components must never communicate directly with Supabase.

Every module owns its own services.

Example:

```text
src/features/sales/services/
src/features/inventory/services/
src/features/crm/services/
```

## Responsibilities

Services are responsible for:

- Database communication
- Authentication
- Error handling
- File uploads
- Logging
- Response mapping
- Retry logic

Components should only consume service methods.

---

# 8. Forms & Validation

## Official Standard

Forms use:

- React Hook Form
- Zod

## Rules

Every form must:

- Validate user input
- Be type-safe
- Use reusable validation schemas
- Share validators across the module

---

# 9. Backend

## Official Standard

The backend platform is Supabase.

Services include:

- PostgreSQL
- Authentication
- Storage
- Edge Functions
- Realtime
- Row Level Security (RLS)

The frontend should treat Supabase as the platform backend rather than communicating directly with PostgreSQL.

---

# 10. Database

## Official Standard

Database engine:

PostgreSQL

## Design Principles

The database should be:

- Relational
- Normalized
- Auditable
- Indexed
- Secure
- Multi-tenant

All application data belongs to a tenant unless explicitly defined as platform data.

---

# 11. Authentication

## Official Standard

Authentication is managed by Supabase Auth.

Initial methods:

- Email
- Password

Future methods:

- Google OAuth
- Microsoft OAuth
- Magic Links
- Multi-factor Authentication

Authentication should remain centralized.

---

# 12. Authorization

## Official Standard

Business Suite uses Role-Based Access Control (RBAC).

Authorization is determined by:

- Tenant
- User
- Role
- Permissions
- Subscription Package
- Enabled Modules

Permissions should never be hardcoded into pages.

---

# 13. Storage

Business documents are stored using Supabase Storage.

Examples:

- Logos
- Attachments
- Contracts
- Images
- Reports
- User profile photos

Storage paths should always be tenant-aware.

---

# 14. Notifications

Initial channels:

- Email
- In-App Notifications

Future channels:

- SMS
- WhatsApp
- Push Notifications

Notification providers should be configurable.

---

# 15. Reporting

Supported formats:

- PDF
- Excel
- CSV

Charts should use:

- Recharts

Reports should be generated from reusable reporting services.

---

# 16. Payment Integrations

Supported providers will include:

- Mobile Money
- Stripe
- Flutterwave
- Pesapal
- Bank APIs

Payment providers must be configurable.

---

# 17. APIs

## Official Standard

Architecture:

REST

Format:

JSON

Version:

```text
/api/v1/
```

Future GraphQL support may be introduced if justified.

---

# 18. Security

The platform enforces:

- HTTPS
- JWT Authentication
- Row Level Security
- RBAC
- Tenant Isolation
- Audit Logging
- Secure File Access
- Input Validation

Security requirements are expanded in Security.md.

---

# 19. Deployment

Current deployment:

Frontend

- Vercel

Backend

- Supabase

Future deployment options may include:

- Docker
- Self-hosted PostgreSQL
- VPS
- Kubernetes

Deployment architecture must remain cloud-native.

---

# 20. Development Tools

Official tools:

- Lovable
- Cursor
- Git
- GitHub

Optional:

- VS Code
- Supabase CLI

Documentation:

- Markdown
- Mermaid

---

# 21. Mobile Strategy

Business Suite follows an API-first architecture.

Future mobile applications will consume the same backend services used by the web platform.

Recommended stack:

- React Native
- Expo

Business logic should never be duplicated between Web and Mobile.

---

# 22. Technology Governance

Before introducing any new technology, the following questions must be answered:

- Does it improve maintainability?
- Does it improve scalability?
- Does it improve security?
- Does it improve developer productivity?
- Can the current stack already solve the problem?

If the answer is no, the technology should not be adopted.

---

# 23. AI Development Rules

Lovable, Cursor, ChatGPT, and future AI development tools must follow this document.

They must not:

- Introduce new frameworks
- Replace approved libraries
- Generate code using technologies outside this standard

unless this document is updated first.

---

# 24. Conclusion

This document establishes the official technology standards for the Business Suite platform.

Its purpose is to ensure consistency, maintainability, scalability, and long-term stability across every module, every developer, and every AI-assisted implementation throughout the lifetime of the project.
