# ADR-0002: Supabase Postgres as the database and data platform

- **Status:** Proposed
- **Date:** 2026-11-22
- **Related requirements:** FR-02, FR-14, NFR-01, NFR-03, NFR-08, NFR-10

## Context

Tasks are persistent relational data: each task belongs to one user and has typed fields, constraints and case-insensitive title search. The MVP should avoid operating its own database server. The chosen backend is Supabase.

## Decision

Use **Supabase**, with managed **PostgreSQL** as the system of record. Define schema, enums, constraints, indexes, RLS policies and database functions as version-controlled SQL migrations (Supabase CLI). Use the Supabase JavaScript client (`@supabase/supabase-js`) from Angular and Edge Functions. Do not add Prisma for the MVP.

In the single-user MVP (US-09 is a Should-have and follows after the MVP), the tasks table has no `user_id`. Keep RLS enabled with no permissive policies (deny by default) and revoke anonymous/authenticated grants, so the public Data API cannot touch task rows; the Edge Function accesses data with the server-side service-role key, which never reaches the browser. From US-09 on: add `user_id`, enable user-scoping RLS policies that scope task access to `auth.uid() = user_id`, and grant only the operations required. Never expose the Supabase secret/service-role key to Angular. Use the publishable key in the browser, relying on Auth JWTs plus RLS for access control.

## Alternatives considered

| Alternative | Why not chosen |
| --- | --- |
| Self-managed PostgreSQL | More infrastructure and operational work; Supabase already supplies PostgreSQL plus integrated API and Auth. |
| SQLite | File persistence and concurrent hosted access are less suitable. |
| MongoDB or other document stores | The data is relational; foreign keys and constraints are useful. |
| Prisma | Adds a second data-access abstraction when the Supabase client and SQL migrations are sufficient. Could be reconsidered if complex server-side relational workflows justify it. |

## Consequences

**Positive**

- PostgreSQL constraints, foreign keys and indexes protect data below the UI.
- Supabase provides database hosting, generated API, migrations workflow and integration with Auth/RLS.
- `ILIKE` supports case-insensitive substring search.
- Local development can use the Supabase CLI and local stack; tests can run against a local or dedicated test project.

**Negative**

- Correct RLS policies and SQL migrations are essential; mistakes can expose or block data.
- Free-tier limits, inactivity policies, region availability and pricing can change; verify them before choosing hosting.
- Some platform behavior is Supabase-specific, creating vendor dependency.
