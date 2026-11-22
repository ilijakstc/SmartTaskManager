# ADR-0001: Angular SPA with Supabase as managed backend

- **Status:** Proposed
- **Date:** 2026-11-22
- **Related requirements:** FR-12, FR-18, FR-19, NFR-04, NFR-10

## Context

The product needs a browser frontend and a backend API. FR-12 assigns all prioritization logic to the backend; the frontend must not calculate priorities. Later AI input and voice-device extensions should be able to use a documented API. The team is small and the architecture must remain explainable within one semester.

## Decision

Use **Angular** as a separate single-page application and **Supabase** as the managed backend platform, subject to the NFR-07 privacy decision. Phase 1 states that no task data is sent to third-party services. A hosted Supabase project processes and stores task data as an infrastructure provider. The team must explicitly decide whether an infrastructure processor is acceptable. If not, do not accept this ADR as written: choose Variant B with a self-hosted backend/database or revise the architecture and document the requirements decision. Supabase provides PostgreSQL, Supabase Auth and the Data API. A Supabase **Edge Function** is the server-side application boundary for task operations that need business logic, especially list-time prioritization. The prioritization algorithm remains a pure, independently tested TypeScript module inside the function code. From US-09 on (accounts are a Should-have, implemented after the MVP), PostgreSQL Row Level Security (RLS) is the final authorization boundary for user-owned rows; the MVP itself is single-user without authentication (see [ADR-0004](0004-session-cookie-authentication.md)). In the MVP, RLS on `tasks` stays deny-all for direct Data API access and the Edge Function uses the server-side service-role key.

Use **Zard UI** for Angular UI components. It is a presentation library, not an application architecture or backend.

## Alternatives considered

| Alternative | Why not chosen |
| --- | --- |
| Self-hosted Node.js/Express modular monolith | Offers more control, but duplicates capabilities Supabase already provides (database API and authentication) and adds a server to maintain. Reconsider only if requirements need a persistent custom server or complex server-side workflows. |
| Server-rendered full-stack monolith | UI and domain logic are not separated by an API, making FR-12 and later clients harder to satisfy. |
| Microservices | Unnecessary distributed-system complexity for this project. |

## Consequences

**Positive**

- Angular and Supabase have an official integration path; Supabase Auth and RLS cover identity and row-level authorization.
- No separately managed Express server or ORM is required for the MVP.
- Edge Functions provide a server-side place for prioritization and future API workflows.
- The REST API remains usable by future clients.

**Negative**

- The team depends on Supabase's platform, quotas, availability and pricing.
- Security depends on correctly configured RLS policies, grants and function authorization; a publishable key is not a substitute for authorization.
- Edge Functions introduce a second runtime environment to test and deploy.
- Supabase's generated Data API is not identical to a custom REST contract; document the actual function routes and table interface.
