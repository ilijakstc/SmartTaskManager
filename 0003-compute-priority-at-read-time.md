# ADR-0003: Compute the suggested priority when tasks are read

- **Status:** Proposed
- **Date:** 2026-11-22
- **Related requirements:** FR-03, FR-04, FR-06, US-02, AC-02.4, NFR-01, NFR-04, NFR-05

## Context

The suggested priority depends on the due date relative to today. AC-02.4 requires that a task becomes `HIGH` as its deadline approaches "without any user action". There are two ways to achieve this: store the score and keep it up to date, or calculate it whenever it is needed.

The prioritization must be deterministic and testable (NFR-04, NFR-05), and urgency is relative to the user's local date (FR-04), which the backend does not know by itself.

## Decision

**Do not store** score, suggested priority or explanation. Store only the user's inputs (`importance`, `due_date`, `manual_priority`). Whenever tasks are returned, a Supabase Edge Function calculates the score for each task with a pure function `evaluate(input, today)`, where `today` is determined once per request from the `X-Time-Zone` header.

Task list requests work in two steps: the Edge Function uses a server-side Supabase client to filter by status and title search (in the MVP with the service-role key, since there is no authentication; a caller-scoped client with RLS applies once accounts exist, US-09); it then scores, filters by effective priority and sorts according to requirements.md section 2.4.

## Alternatives considered

| Alternative | Why not chosen |
| --- | --- |
| Store the score and priority in columns, update them in a nightly job | Needs a scheduler. The "nightly" moment is not the same for every time zone (FR-04). Stored values can be wrong between runs and after a failed job. Two sources of truth for the same fact. |
| Store the score and recalculate on every read to refresh it | Combines the costs of both options without removing the need to recalculate. |
| Compute in SQL (e.g. a view or generated expression) | Moves the business rule into the database, outside the independently unit-testable TypeScript module required by NFR-04, and makes the constants harder to keep in one place (NFR-05). |

## Consequences

**Positive**

- AC-02.4 holds by construction; there is no stale priority and no background job.
- One implementation of the rules, in one pure module, tested with the injectable date (E1–E12, boundaries 29/30 and 54/55).
- Changing a threshold needs no data migration (NFR-05).
- The same rules apply to every client of the API.

**Negative**

- Sorting and filtering by priority cannot use a database index; the Edge Function processes all tasks that match the SQL filters. This is acceptable for the 500 tasks in NFR-01 but must be verified by a benchmark. If it fails, add pagination or reduce the scored set.
- The `priority` filter on the `Done` view uses a priority computed for today, which has little meaning for completed tasks (open question R5 in architecture.md).
- Every list request does scoring work again; there is no cached result.
