# ADR-0005: Deploy Angular frontend separately from Supabase

- **Status:** Proposed
- **Date:** 2026-11-22
- **Related requirements:** NFR-08, NFR-10; [ADR-0001](0001-modular-monolith-with-separate-spa.md), [ADR-0004](0004-session-cookie-authentication.md)

## Context

Supabase is a hosted backend platform with its own API and Auth endpoints. Angular is a static single-page application and can be hosted independently. Unlike the previous custom-cookie design, Supabase Auth uses JWT-based sessions, so same-origin hosting is not an architectural requirement.

## Decision

Build the **Angular application as static assets** and deploy it to a static web host. Deploy Supabase database migrations and Edge Functions to the Supabase project. Configure any required allowed origins/CORS for Edge Functions. From US-09 on (accounts are a Should-have and follow after the MVP), also configure the Angular origin in the Supabase Auth redirect settings. Use HTTPS in production.

Development uses the Angular CLI dev server and a local Supabase CLI stack (or a dedicated development Supabase project). No Docker image containing both frontend and backend is required.

## Alternatives considered

| Alternative | Why not chosen |
| --- | --- |
| Serve Angular files from a custom backend | There is no custom application server in the chosen stack; adding one solely to serve static files defeats the managed-backend choice. |
| One container with frontend and API | Supabase is a managed platform and cannot be packaged into the application's Docker image. |
| Same-origin reverse proxy | Possible, but adds routing and deployment configuration without being necessary for JWT-based Supabase access. |

## Consequences

**Positive**

- Frontend and backend can be deployed independently.
- Static hosting is simple and can be inexpensive; Supabase handles database, Auth and Edge Functions.
- No custom backend process is needed just to serve the Angular build.

**Negative**

- There are separate deployment/configuration surfaces: frontend hosting and Supabase.
- Cross-origin requests must be configured correctly; restrict origins rather than using permissive CORS.
- Auth redirect URLs and production environment configuration must stay synchronized.
- Frontend availability and Supabase availability are separate dependencies.
