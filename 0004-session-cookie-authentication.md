# ADR-0004: Supabase Auth for user authentication

- **Status:** Proposed
- **Date:** 2026-11-22

- **Related requirements:** FR-15, US-09 (Should), NFR-08
- **Scope:** Post-MVP. Per mvp.md, only Must-have stories are implemented in the MVP. US-09 (accounts) is a Should-have, so the MVP runs as a single-user application without registration, login or per-user data isolation. This ADR defines the authentication approach for the phase in which accounts are introduced.

## Context

US-09 adds registration, login, logout and strict isolation of each user's tasks after the MVP. The earlier custom-cookie draft implemented password storage and server-side sessions in the application. With Supabase selected as the backend, authentication is a built-in platform capability and should not be reimplemented. Introducing accounts also adds `user_id` to the tasks table and activates user-scoping RLS policies (see [ADR-0002](0002-postgresql-as-database.md)).

## Decision

When US-09 is implemented (after the MVP), use **Supabase Auth with email/password**. Angular uses `@supabase/supabase-js` for sign-up, sign-in, sign-out and session restoration. Supabase Auth issues and refreshes JWT-based sessions. Authenticated database requests carry the user's access token; PostgreSQL RLS policies use `auth.uid()` to restrict rows to that user.

- Do not create custom `users` password or `sessions` tables. Reference the Supabase-managed identity through `auth.users.id`; create a separate public profile table only if product requirements need profile fields.
- Do not store access tokens manually in application-managed local storage. Use the Supabase client session handling and review its storage configuration for the browser threat model.
- From US-09 on, require an authenticated user for task Edge Function operations. The function must forward/derive the caller's JWT and use a user-scoped Supabase client so RLS remains active.
- Keep service-role/secret keys server-side only. From US-09 on, never use an elevated client for ordinary user task operations.
- Configure allowed redirect URLs and email confirmation behavior for the deployed Angular origin.

## Alternatives considered

| Alternative | Why not chosen |
| --- | --- |
| Custom server-side sessions and password hashing | Duplicates Supabase Auth and requires custom credential, expiry, revocation and recovery flows. |
| Hand-built JWT authentication | Reimplements token issuance, refresh and account lifecycle without a benefit. |
| Third-party OAuth provider | Adds scope and setup not required by US-09; Supabase Auth can support providers later if desired. |

## Consequences

**Positive**

- Registration, login, logout, token refresh and identity lifecycle are provided by a maintained service.
- RLS can use the authenticated identity directly, including for direct Data API access.
- No custom password storage or session cleanup code.

**Negative**

- Authentication is platform-dependent and requires network access to Supabase.
- JWT session semantics (expiry, refresh, logout) must be tested against the acceptance criteria when US-09 is implemented.

- Supabase Auth configuration (email confirmation, redirect allow-list, password policy, rate limits) becomes part of deployment and security review.
- Future voice clients can use supported token-based authentication, but must not receive privileged service keys.
