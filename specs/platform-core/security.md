# Platform Core Security Specification

Version: 1.0

Status: Approved

Module: Platform Core

---

# 1. Purpose

This document defines the security architecture for Platform Core.

It establishes the authentication, authorization, tenant isolation, session management, auditing, and security policies that apply across the entire Business Suite platform.

All current and future modules inherit these security standards.

---

# 2. Objectives

Platform Core security aims to:

- Protect user accounts.
- Protect tenant data.
- Prevent unauthorized access.
- Enforce tenant isolation.
- Secure authentication.
- Secure API access.
- Protect sensitive information.
- Provide complete auditability.
- Support enterprise authentication providers.
- Provide a scalable security foundation.

---

# 3. Security Principles

Business Suite follows the principle of **Security by Design**.

Security is implemented at every layer of the platform.

The platform follows these principles:

- Least Privilege
- Defense in Depth
- Zero Trust
- Secure Defaults
- Audit Everything
- Never Trust Client Input
- Configuration over Hardcoding

---

# 4. Authentication

Authentication is managed exclusively by Platform Core.

Business modules must never implement their own authentication.

Supported authentication methods:

- Email & Password
- Google OAuth
- Microsoft OAuth
- Magic Link
- Multi-Factor Authentication (MFA)

Future support:

- Single Sign-On (SSO)
- SAML
- OpenID Connect

---

# 5. Password Policy

Passwords must meet the following minimum requirements:

- Minimum 8 characters
- At least one uppercase letter
- At least one lowercase letter
- At least one number
- At least one special character

Passwords must never be stored in plain text.

Password hashing is managed by the authentication provider.

---

# 6. Email Verification

Every newly registered user must verify their email address.

Rules:

- Verification links expire.
- Verification links are single-use.
- Unverified users cannot access protected areas.
- Administrators cannot bypass email verification unless explicitly allowed by platform policy.

---

# 7. Session Management

Platform Core manages all user sessions.

Sessions should support:

- Automatic refresh
- Session timeout
- Remember Me
- Secure logout

Future support:

- Device management
- Active session management
- Force logout
- Trusted devices

Expired sessions must redirect users to the login page.

---

# 8. Multi-Factor Authentication (MFA)

Platform Core supports optional MFA.

Supported methods:

- Email Verification Code

Future support:

- Authenticator Apps
- SMS OTP
- Passkeys
- Security Keys

Platform administrators may enforce MFA in future versions.

---

# 9. Authentication Failure

Authentication must fail securely.

Common failure scenarios:

- Invalid email
- Invalid password
- Locked account
- Suspended account
- Disabled account
- Expired session
- Expired token
- Invalid token

Users should receive clear, non-technical error messages.

The system must never expose sensitive security information.

Example:

Instead of:

```text
Password incorrect.
```

Display:

```text
Invalid email or password.
```

---

# 10. Authorization

Authorization determines what an authenticated user is allowed to do.

Business Suite uses Role-Based Access Control (RBAC).

Authorization is always evaluated within the active workspace.

A user may have different permissions in different workspaces.

---

## Authorization Checks

Every protected action must verify:

- Authenticated User
- Active Workspace
- Tenant Membership
- User Status
- Role
- Permission
- Module Access
- Subscription Status

Do not rely only on hiding buttons in the UI.

Authorization must be enforced in:

- Frontend
- Service Layer
- Database / RLS

---

# 11. Role-Based Access Control

Roles belong to tenants.

Permissions belong to roles.

Users receive permissions through tenant membership roles.

Example:

```text
User: John

Workspace: ABC Ltd
Role: Administrator

Workspace: XYZ Ltd
Role: Finance Officer
```

The same user may have different permissions depending on the active workspace.

---

# 12. Permission Rules

Permissions should be grouped by module.

Standard permission actions include:

- View
- Create
- Edit
- Delete
- Approve
- Reject
- Export
- Print
- Configure

Permission actions should come from user codes or reference data where practical.

Permissions must not be hardcoded inside pages.

---

# 13. Tenant Isolation

Tenant isolation is mandatory.

Every tenant-owned record must include:

```text
tenant_id
```

Users may only access tenant data if they have an active membership in that tenant.

Tenant isolation must be enforced through:

- Active workspace context
- Service layer filters
- Database policies
- Row Level Security

No module may bypass tenant isolation.

---

# 14. Row Level Security

All tenant-owned tables must use PostgreSQL Row Level Security where practical.

RLS policies should ensure that users only access records belonging to tenants where they have active membership.

Tenant-owned tables include:

- tenant_members
- companies
- branches
- subscriptions
- roles
- role_permissions
- notifications
- future business tables

Platform-wide tables may not require tenant RLS but must still enforce access control.

---

# 15. Workspace Security

Workspace switching must validate membership before access is granted.

When a user switches workspace, the system must reload:

- Tenant
- Company
- Branches
- Roles
- Permissions
- Modules
- Subscription

The active workspace must never be trusted only from local storage.

It must be validated against the authenticated user.

---

# 16. Module Access Security

Modules are controlled by subscription packages.

Before opening a module, the platform must verify:

- Tenant subscription is valid.
- Package includes the module.
- User has permission to access the module.
- Module is active.

If access is denied, show a friendly restricted-access message.

---

# 17. Subscription Security

Tenant access depends on subscription status.

Supported statuses include:

- Trial
- Active
- Expired
- Suspended
- Cancelled

Rules:

- Suspended tenants cannot access the workspace.
- Expired tenants may be restricted according to platform policy.
- Cancelled tenants should not access active modules.
- Platform Core access may remain limited for subscription renewal or support.

---

# 18. API Security

All API or service operations must enforce:

- Authentication
- Tenant context
- Permission checks
- Input validation
- Rate limiting where applicable

API responses must not expose sensitive internal details.

Raw database errors should not be shown to users.

---

# 19. Service Layer Security

UI components must never communicate directly with Supabase.

All backend operations must pass through service classes.

Services are responsible for:

- Applying tenant filters
- Checking permissions
- Validating module access
- Handling errors safely
- Creating audit logs where required

---

# 20. Frontend Security

Frontend checks improve user experience but are not enough for security.

The frontend should:

- Hide unavailable navigation items.
- Disable unauthorized actions.
- Show permission messages.
- Redirect unauthorized users.

However, backend and database security must still enforce the same rules.

---

# 10. Authorization

Authorization determines what an authenticated user is allowed to do.

Business Suite uses Role-Based Access Control (RBAC).

Authorization is always evaluated within the active workspace.

A user may have different permissions in different workspaces.

---

## Authorization Checks

Every protected action must verify:

- Authenticated User
- Active Workspace
- Tenant Membership
- User Status
- Role
- Permission
- Module Access
- Subscription Status

Do not rely only on hiding buttons in the UI.

Authorization must be enforced in:

- Frontend
- Service Layer
- Database / RLS

---

# 11. Role-Based Access Control

Roles belong to tenants.

Permissions belong to roles.

Users receive permissions through tenant membership roles.

Example:

```text
User: John

Workspace: ABC Ltd
Role: Administrator

Workspace: XYZ Ltd
Role: Finance Officer
```

The same user may have different permissions depending on the active workspace.

---

# 12. Permission Rules

Permissions should be grouped by module.

Standard permission actions include:

- View
- Create
- Edit
- Delete
- Approve
- Reject
- Export
- Print
- Configure

Permission actions should come from user codes or reference data where practical.

Permissions must not be hardcoded inside pages.

---

# 13. Tenant Isolation

Tenant isolation is mandatory.

Every tenant-owned record must include:

```text
tenant_id
```

Users may only access tenant data if they have an active membership in that tenant.

Tenant isolation must be enforced through:

- Active workspace context
- Service layer filters
- Database policies
- Row Level Security

No module may bypass tenant isolation.

---

# 14. Row Level Security

All tenant-owned tables must use PostgreSQL Row Level Security where practical.

RLS policies should ensure that users only access records belonging to tenants where they have active membership.

Tenant-owned tables include:

- tenant_members
- companies
- branches
- subscriptions
- roles
- role_permissions
- notifications
- future business tables

Platform-wide tables may not require tenant RLS but must still enforce access control.

---

# 15. Workspace Security

Workspace switching must validate membership before access is granted.

When a user switches workspace, the system must reload:

- Tenant
- Company
- Branches
- Roles
- Permissions
- Modules
- Subscription

The active workspace must never be trusted only from local storage.

It must be validated against the authenticated user.

---

# 16. Module Access Security

Modules are controlled by subscription packages.

Before opening a module, the platform must verify:

- Tenant subscription is valid.
- Package includes the module.
- User has permission to access the module.
- Module is active.

If access is denied, show a friendly restricted-access message.

---

# 17. Subscription Security

Tenant access depends on subscription status.

Supported statuses include:

- Trial
- Active
- Expired
- Suspended
- Cancelled

Rules:

- Suspended tenants cannot access the workspace.
- Expired tenants may be restricted according to platform policy.
- Cancelled tenants should not access active modules.
- Platform Core access may remain limited for subscription renewal or support.

---

# 18. API Security

All API or service operations must enforce:

- Authentication
- Tenant context
- Permission checks
- Input validation
- Rate limiting where applicable

API responses must not expose sensitive internal details.

Raw database errors should not be shown to users.

---

# 19. Service Layer Security

UI components must never communicate directly with Supabase.

All backend operations must pass through service classes.

Services are responsible for:

- Applying tenant filters
- Checking permissions
- Validating module access
- Handling errors safely
- Creating audit logs where required

---

# 20. Frontend Security

Frontend checks improve user experience but are not enough for security.

The frontend should:

- Hide unavailable navigation items.
- Disable unauthorized actions.
- Show permission messages.
- Redirect unauthorized users.

However, backend and database security must still enforce the same rules.

---

# 34. Platform Security Policies

Business Suite uses configurable security policies.

Security policies should be managed by the Super Administrator.

Policies should be stored in the platform configuration and not hardcoded into the application.

Examples include:

- Password Policy
- Session Policy
- Registration Policy
- Login Policy
- Lockout Policy
- MFA Policy
- Trial Policy
- Subscription Policy
- Notification Policy

Changes to security policies should take effect without requiring application redeployment where practical.

---

# 35. Security Policy Examples

## Password Policy

Configurable settings:

- Minimum Password Length
- Require Uppercase
- Require Lowercase
- Require Numbers
- Require Special Characters
- Password Expiration (Future)
- Password History (Future)

---

## Session Policy

Configurable settings:

- Session Timeout
- Remember Me Duration
- Maximum Concurrent Sessions (Future)
- Automatic Logout
- Idle Timeout

---

## Login Policy

Configurable settings:

- Maximum Login Attempts
- Lockout Duration
- CAPTCHA Threshold (Future)
- Email Verification Required
- MFA Required

---

## Trial Policy

Configurable settings:

- Trial Duration
- Trial Package
- Grace Period
- Reminder Schedule

---

## Notification Policy

Configurable settings:

- Email Notifications
- SMS Notifications
- In-App Notifications
- Security Alerts

---

# 36. Security Responsibilities

Security is a shared responsibility.

## Platform Core

Responsible for:

- Authentication
- Authorization
- Session Management
- Tenant Isolation
- Audit Logging
- Security Policies

---

## Feature Modules

Responsible for:

- Respecting Platform Core permissions
- Respecting tenant isolation
- Logging module-specific audit events
- Following secure coding practices

Feature modules must never implement their own authentication or authorization systems.

---

## Developers

Developers must:

- Follow the Architecture documentation.
- Follow Coding Standards.
- Follow Design Language.
- Follow Platform Core Security.
- Protect sensitive information.
- Validate all input.
- Avoid hardcoded secrets.
- Respect tenant isolation.

---

## AI Development Tools

Lovable, Cursor, ChatGPT, GitHub Copilot, Claude, and future AI assistants must:

- Follow this security specification.
- Never bypass permission checks.
- Never bypass tenant isolation.
- Never expose secrets.
- Never hardcode credentials.
- Never implement authentication outside Platform Core.
- Always use the approved Service Layer.
- Respect Role-Based Access Control.
- Respect subscription and module access.

AI-generated code must be reviewed before production deployment.

---

# 37. Security Checklist

Before any feature is released, verify:

Authentication

- Users can authenticate successfully.
- Invalid credentials are rejected.
- Email verification is enforced.
- MFA works where enabled.

Authorization

- Permissions are enforced.
- Unauthorized users cannot access protected resources.
- Module restrictions are respected.

Tenant Isolation

- Users only access their own tenant data.
- Workspace switching reloads context correctly.
- Cross-tenant access is prevented.

Sessions

- Sessions expire correctly.
- Logout clears the session.
- Remember Me works as configured.

Audit

- Critical actions create audit records.
- Security events are logged.
- Audit logs are immutable.

API

- Requests require authentication.
- Input is validated.
- Sensitive errors are hidden.

Files

- Upload validation works.
- File permissions are enforced.

Notifications

- Security notifications are sent where required.
- Sensitive data is not exposed.

---

# 38. Security Acceptance Criteria

Platform Core security is considered complete when:

- Authentication works for all supported providers.
- Authorization is enforced consistently.
- Tenant isolation is verified.
- Row Level Security is implemented where applicable.
- Sessions are managed securely.
- Audit logging is operational.
- Security policies are configurable.
- Sensitive information is protected.
- Feature modules inherit Platform Core security.
- AI-generated implementations comply with this specification.

---

# 39. Future Enhancements

Future versions of Platform Core may include:

Authentication

- Passkeys (WebAuthn)
- Hardware Security Keys
- Enterprise SSO
- SAML
- OpenID Connect

Monitoring

- Security Dashboard
- Threat Detection
- Suspicious Login Detection
- Device Management
- Active Session Viewer

Compliance

- GDPR Support
- Data Retention Policies
- Consent Management
- Data Export
- Data Anonymization

Platform

- API Keys
- Webhooks
- IP Whitelisting
- Tenant-Level Security Policies
- Customer-Managed Encryption Keys (Future)

These enhancements should integrate with the existing security architecture without requiring redesign.

---

# 40. Conclusion

Platform Core Security provides the foundation for securing the entire Business Suite platform.

Every current and future module inherits these security standards.

By centralizing authentication, authorization, tenant isolation, audit logging, and configurable security policies within Platform Core, the platform achieves a consistent, scalable, and maintainable security model.

Security is not a standalone feature—it is a core responsibility that must be considered throughout the design, development, deployment, and operation of Business Suite.
