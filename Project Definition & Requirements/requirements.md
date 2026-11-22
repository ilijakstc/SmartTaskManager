# Requirements – Smart Task Manager

This document contains the user stories, the prioritization model, the functional and non-functional requirements and their MoSCoW prioritization. Every Must-have user story has testable acceptance criteria.

**Prioritization scheme (MoSCoW)**

| Label                     | Meaning in this project                                                                       |
| ------------------------- | --------------------------------------------------------------------------------------------- |
| **Must**                  | Without it the MVP use case does not work. Part of the MVP.                                   |
| **Should**                | Important, planned for the semester after the MVP is complete. Not required for the MVP demo. |
| **Could**                 | Nice to have; only if time remains, or a later extension.                                     |
| **Won't (this semester)** | Explicitly out of scope; documented so the boundary is clear.                                 |

---

## 1. Glossary

| Term                   | Definition                                                                                                              |
| ---------------------- | ----------------------------------------------------------------------------------------------------------------------- |
| **Task**               | A unit of work with at least a title. Can have a description, due date and importance.                                  |
| **Importance**         | How much the task matters to the user, independent of time: `LOW`, `MEDIUM`, `HIGH`. Set by the user; default `MEDIUM`. |
| **Due date**           | Optional calendar date (no time of day) by which the task should be done.                                               |
| **Priority score**     | Number from 0 to 80 calculated by the system from due date and importance (see section 2).                              |
| **Suggested priority** | Priority level derived from the score: `LOW`, `MEDIUM` or `HIGH`.                                                       |
| **Manual priority**    | Priority level set explicitly by the user. Overrides the suggestion.                                                    |
| **Effective priority** | The priority used for sorting and display: the manual priority if set, otherwise the suggested priority.                |
| **Active task**        | A task with status `OPEN` (not done).                                                                                   |
| **User account**       | A registered user identified by e-mail address and password. Every task belongs to exactly one user account.            |
| **Today**              | The current calendar date in the user's local time zone (as reported by the browser).                                   |

---

## 2. Prioritization model (MVP)

The automatic prioritization is **rule-based and deterministic**: the same task on the same day always gets the same suggestion. This makes it explainable to the user and testable. AI-based prioritization is a Could-have (US-12).

### 2.1 Score calculation

**Score = Urgency points + Importance points** (range 0–80)

| Due date relative to today       | Urgency points |
| -------------------------------- | -------------- |
| Overdue (due date before today)  | 50             |
| Due today or tomorrow (0–1 days) | 40             |
| Due in 2–3 days                  | 30             |
| Due in 4–7 days                  | 20             |
| Due in more than 7 days          | 10             |
| No due date                      | 0              |

| Importance         | Importance points |
| ------------------ | ----------------- |
| `HIGH`             | 30                |
| `MEDIUM` (default) | 15                |
| `LOW`              | 0                 |

### 2.2 Score → suggested priority

| Score | Suggested priority |
| ----- | ------------------ |
| 55–80 | `HIGH`             |
| 30–54 | `MEDIUM`           |
| 0–29  | `LOW`              |

### 2.3 Reference examples (also used as test cases)

| #   | Importance | Due         | Score        | Suggested |
| --- | ---------- | ----------- | ------------ | --------- |
| E1  | HIGH       | tomorrow    | 40 + 30 = 70 | HIGH      |
| E2  | MEDIUM     | tomorrow    | 40 + 15 = 55 | HIGH      |
| E3  | LOW        | tomorrow    | 40 + 0 = 40  | MEDIUM    |
| E4  | HIGH       | in 3 days   | 30 + 30 = 60 | HIGH      |
| E5  | HIGH       | in 7 days   | 20 + 30 = 50 | MEDIUM    |
| E6  | MEDIUM     | in 5 days   | 20 + 15 = 35 | MEDIUM    |
| E7  | LOW        | in 5 days   | 20 + 0 = 20  | LOW       |
| E8  | HIGH       | no due date | 0 + 30 = 30  | MEDIUM    |
| E9  | MEDIUM     | no due date | 0 + 15 = 15  | LOW       |
| E10 | LOW        | overdue     | 50 + 0 = 50  | MEDIUM    |
| E11 | MEDIUM     | overdue     | 50 + 15 = 65 | HIGH      |
| E12 | MEDIUM     | in 30 days  | 10 + 15 = 25 | LOW       |

**Design decisions:**

- An overdue task with low importance only reaches `MEDIUM` (E10). Overdue unimportant tasks shouldn't push important ones off the top.
- A task without a due date can be at most `MEDIUM` (E8). Without a deadline the system has no urgency signal, and the user can still override it manually.
- The thresholds are a first version. They are configurable constants in code (NFR-05), so they can be adjusted after user feedback without changing the logic.

### 2.4 Sorting of the task list

Active tasks are sorted by:

1. Effective priority: `HIGH` → `MEDIUM` → `LOW`
2. Score, descending
3. Due date, ascending (tasks without a due date last)
4. Creation time, ascending (older first)

---

## 3. User stories

| ID    | User story                                                                                                                                                                                                    | Priority              |
| ----- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | --------------------- |
| US-01 | As a user, I want to create a task with a title and optionally a description, due date and importance, so that I can capture everything I need to do in one place.                                            | **Must**              |
| US-02 | As a user, I want the system to suggest a priority for each task with a short explanation, so that I don't have to decide priorities myself and can understand why a task ranks where it does.                | **Must**              |
| US-03 | As a user, I want to see my open tasks in a list sorted by priority, so that I immediately know what to work on next.                                                                                         | **Must**              |
| US-04 | As a user, I want to override the suggested priority and later reset it to the suggestion, so that I stay in control when I know better than the system.                                                      | **Must**              |
| US-05 | As a user, I want to mark a task as done, so that my list only shows what is still open.                                                                                                                      | **Must**              |
| US-06 | As a user, I want to edit a task's title, description, due date and importance, so that I can correct mistakes and react to changes, with the priority suggestion updating accordingly.                       | **Must**              |
| US-07 | As a user, I want to delete a task, so that I can remove tasks that are no longer relevant.                                                                                                                   | **Must**              |
| US-08 | As a user, I want to filter my tasks by priority and status (open/done) and search them by title, so that I can find tasks quickly when the list gets long.                                                   | **Must**              |
| US-09 | As a user, I want to register and log in with my own account, so that my tasks are private and available from any browser.                                                                                    | Should                |
| US-10 | As a user, I want to be notified in the list when the system's suggestion is higher than my manual priority, so that tasks I down-prioritized don't silently become urgent.                                   | Should                |
| US-11 | As a user, I want to enter an estimated effort (small/medium/large) for a task, so that the prioritization can take into account how early I need to start.                                                   | Could                 |
| US-12 | As a user, I want to describe a task in natural language (e.g. "call the tax advisor next Tuesday, urgent") and have an AI fill in the fields and suggest a priority, so that capturing tasks is even faster. | Could                 |
| US-13 | As a user, I want to add tasks by voice through a small device at home ("Hey Task Manager, add a task…"), so that I can capture tasks without opening the browser.                                            | Could                 |
| US-14 | As a user, I want to get a reminder for tasks due today, so that I don't miss deadlines.                                                                                                                      | Could                 |
| —     | Team features (sharing tasks, assigning tasks to others), native mobile apps, calendar synchronization                                                                                                        | Won't (this semester) |

---

## 4. Acceptance criteria (Must-have stories)

Format: _Given – When – Then_. Each criterion is meant to become at least one automated or manual test.

### US-01 – Create task

- **AC-01.1** Given I am on the task list, when I enter a title and submit, then the task is saved with status `OPEN`, importance `MEDIUM` and no due date, and it appears in the list without a page reload.
- **AC-01.2** Given I fill in title, description, due date and importance, when I submit, then all four values are saved and shown in the task's details.
- **AC-01.3** Given the title is empty or only whitespace, when I submit, then the task is not saved and a message says that a title is required.
- **AC-01.4** Given the title is longer than 200 characters or the description longer than 2,000 characters, when I submit, then the task is not saved and a message states the limit.

### US-02 – Priority suggestion with explanation

- **AC-02.1** Given a task is created or changed, when it is saved, then the system calculates its score and suggested priority according to section 2 of this document.
- **AC-02.2** Given the reference examples E1–E12 (section 2.3), when their suggested priorities are calculated, then every result matches the table.
- **AC-02.3** Given a task in the list, when I look at it, then I see its suggested priority and a one-line explanation naming the factors, e.g. "High – due tomorrow, importance high".
- **AC-02.4** Given a task due in 4 days with importance `HIGH` (suggested `MEDIUM`, score 50), when two days pass and I open the list, then the task is shown as `HIGH` (score 60) without any user action.

### US-03 – Prioritized list

- **AC-03.1** Given several open tasks, when I open the task list, then they are ordered according to the sorting rules in section 2.4.
- **AC-03.2** Given a task is created, edited, completed or its priority is overridden, when the change is saved, then the list order updates without a page reload.
- **AC-03.3** Given tasks marked as done exist, when I open the default list view, then done tasks are not shown.
- **AC-03.4** Given I have no open tasks, when I open the list, then I see an empty state with a hint how to create a task.

### US-04 – Manual override

- **AC-04.1** Given a task with a suggested priority, when I select a different priority, then that manual priority becomes the effective priority, the list is re-sorted, and the task is visibly marked as "set manually".
- **AC-04.2** Given a task with a manual priority, when its suggested priority changes (because of time passing or an edit), then the effective priority stays the manual one.
- **AC-04.3** Given a task with a manual priority, when I choose "use suggestion", then the manual priority is removed and the suggested priority becomes effective again.
- **AC-04.4** Given a task with a manual priority, when I reload the page, then the manual priority is still in effect.

### US-05 – Mark as done

- **AC-05.1** Given an open task, when I mark it as done, then its status changes to `DONE`, the completion date is stored and it disappears from the default list.
- **AC-05.2** Given a task I just marked as done, when I choose "undo" within the confirmation message, then it returns to `OPEN` and reappears in the list at its sorted position.

### US-06 – Edit task

- **AC-06.1** Given an existing task, when I change title, description, due date or importance and save, then the changes are stored and shown.
- **AC-06.2** Given a task without manual priority, when I change its due date or importance, then the suggested priority and explanation are recalculated on save.
- **AC-06.3** Given I clear the title while editing, when I save, then the change is rejected with the same validation as AC-01.3, and the previous values are kept.

### US-07 – Delete task

- **AC-07.1** Given an existing task, when I choose "delete", then a confirmation dialog is shown before anything is deleted.
- **AC-07.2** Given the confirmation dialog, when I confirm, then the task is permanently removed from the database and disappears from the list without a page reload.
- **AC-07.3** Given the confirmation dialog, when I cancel, then the task remains unchanged.
- **AC-07.4** Given I deleted a task, when I reload the page, then the task does not reappear.

### US-08 – Filter and search

- **AC-08.1** Given tasks with different effective priorities, when I filter by a priority (e.g. `HIGH`), then only tasks with that effective priority are shown, still sorted according to section 2.4.
- **AC-08.2** Given the status filter with the options `Open` (default), `Done` and `All`, when I select `Done`, then only completed tasks are shown, sorted by completion date (newest first).
- **AC-08.3** Given a task titled "Call TAX advisor", when I search for "tax", then the task is shown (case-insensitive substring match on the title only).
- **AC-08.4** Given a priority filter, a status filter and a search term are set at the same time, when the list is shown, then only tasks matching all of them are displayed.
- **AC-08.5** Given no task matches the current filters and search, when the list is shown, then a "No tasks match" message is displayed together with an option to clear all filters.
- **AC-08.6** Given filters or a search term are active, when I choose "clear filters", then the default view is restored (status `Open`, all priorities, no search term).

---

## 5. Functional requirements

| ID    | Requirement                                                                                                                                                                                                                                                         | Story    | Priority |
| ----- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | -------- | -------- |
| FR-01 | The system shall allow creating a task with title (required, 1–200 characters after trimming), description (optional, ≤ 2,000 characters), due date (optional, calendar date) and importance (`LOW`/`MEDIUM`/`HIGH`, default `MEDIUM`).                             | US-01    | Must     |
| FR-02 | The system shall persist tasks in a database so that they survive restarts of the backend and browser reloads.                                                                                                                                                      | US-01    | Must     |
| FR-03 | The system shall calculate a priority score and suggested priority for every open task according to section 2.                                                                                                                                                      | US-02    | Must     |
| FR-04 | The system shall determine urgency relative to the user's current local date, so that suggested priorities update as time passes without user action. The backend calculates the score; the frontend passes the user's time zone (or local date) with each request. | US-02    | Must     |
| FR-05 | The system shall display, for each task, the suggested priority and a short explanation naming the urgency and importance factors that produced it.                                                                                                                 | US-02    | Must     |
| FR-06 | The system shall display open tasks sorted according to section 2.4.                                                                                                                                                                                                | US-03    | Must     |
| FR-07 | The system shall allow setting a manual priority on a task, which then becomes the effective priority.                                                                                                                                                              | US-04    | Must     |
| FR-08 | The system shall allow removing a manual priority, restoring the suggested priority as effective.                                                                                                                                                                   | US-04    | Must     |
| FR-09 | The system shall visually distinguish manually set priorities from suggested ones.                                                                                                                                                                                  | US-04    | Must     |
| FR-10 | The system shall allow marking a task as done (and undoing this), storing the completion date.                                                                                                                                                                      | US-05    | Must     |
| FR-11 | The system shall allow editing all user-entered fields of a task with the same validation rules as on creation.                                                                                                                                                     | US-06    | Must     |
| FR-12 | The backend shall provide all task operations through an HTTP API that the frontend uses; the frontend contains no prioritization logic of its own.                                                                                                                 | US-01–06 | Must     |
| FR-13 | The system shall allow permanently deleting a task after a confirmation step.                                                                                                                                                                                       | US-07    | Must     |
| FR-14 | The system shall allow filtering tasks by effective priority and by status (`Open` default, `Done`, `All`), and searching by title (case-insensitive substring); filters and search are combined with AND.                                                          | US-08    | Must     |
| FR-15 | The system shall support registration (e-mail + password, min. 8 characters), login and logout; each task belongs to one user, and each user can only see and change their own tasks.                                                                               | US-09    | Should   |
| FR-16 | The system shall mark tasks whose suggested priority is higher than their manual priority.                                                                                                                                                                          | US-10    | Should   |
| FR-17 | The system shall allow an optional effort estimate that influences the score (rule to be defined if implemented).                                                                                                                                                   | US-11    | Could    |
| FR-18 | The system shall accept a natural-language task description, send it to an external AI API, and pre-fill the task form with the extracted fields for the user to confirm.                                                                                           | US-12    | Could    |
| FR-19 | The system shall accept task creation requests from an authenticated voice device via the API.                                                                                                                                                                      | US-13    | Could    |
| FR-20 | The system shall show a reminder for tasks due today when the user opens the app (in-app; e-mail/push optional).                                                                                                                                                    | US-14    | Could    |

---

## 6. Non-functional requirements

| ID     | Category                       | Requirement (measurable)                                                                                                                                                                                                                                           | Priority |
| ------ | ------------------------------ | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ | -------- |
| NFR-01 | Performance                    | With 500 tasks stored, the task list API responds in ≤ 500 ms (95th percentile) and the list is fully rendered in ≤ 1 s, measured on the development machine.                                                                                                      | Must     |
| NFR-02 | Usability                      | A first-time user can create a task with title, due date and importance in ≤ 30 seconds, without instructions, and all task fields are on a single form (no multi-step wizard). Verified with at least 3 test users.                                               | Must     |
| NFR-03 | Reliability / data integrity   | No saved task is lost or altered when the backend restarts. Invalid input is rejected by the backend with a descriptive error (HTTP 4xx), never stored.                                                                                                            | Must     |
| NFR-04 | Testability / maintainability  | The prioritization logic is implemented as an isolated, side-effect-free component with ≥ 90 % branch coverage by automated unit tests, including all reference examples E1–E12 and all score boundaries (29/30, 54/55). The current date is injectable for tests. | Must     |
| NFR-05 | Transparency / configurability | Prioritization is deterministic: identical task data and date always produce the same score. Point values and thresholds are defined as named constants in one place.                                                                                              | Must     |
| NFR-06 | Compatibility                  | The frontend works in the current versions of Chrome, Firefox and Edge on desktop, and is usable (no horizontal scrolling) at a 375 px wide viewport.                                                                                                              | Should   |
| NFR-07 | Privacy                        | In the MVP no task data is sent to third-party services. If AI features (US-12) are added, only the text entered for that feature is sent, and the user is informed in the UI.                                                                                     | Must     |
| NFR-08 | Security                       | Passwords are stored only as salted hashes (e.g. bcrypt), all API endpoints except registration/login require authentication, and a user cannot read or modify another user's tasks (verified by automated tests).                                                 | Must     |
| NFR-09 | Accessibility                  | All Must-have functions are operable with the keyboard only, and priority is never conveyed by color alone (label or icon in addition).                                                                                                                            | Should   |
| NFR-10 | Cost                           | The MVP runs without paid external services. Any Could-have feature that causes costs documents its expected monthly cost before implementation.                                                                                                                   | Must     |

---

## 7. Traceability (Must-have)

| Story | Functional requirements | Acceptance criteria | Related NFRs   |
| ----- | ----------------------- | ------------------- | -------------- |
| US-01 | FR-01, FR-02, FR-12     | AC-01.1 – AC-01.5   | NFR-02, NFR-03 |
| US-02 | FR-03, FR-04, FR-05     | AC-02.1 – AC-02.4   | NFR-04, NFR-05 |
| US-03 | FR-06                   | AC-03.1 – AC-03.4   | NFR-01         |
| US-04 | FR-07, FR-08, FR-09     | AC-04.1 – AC-04.4   | NFR-09         |
| US-05 | FR-10                   | AC-05.1 – AC-05.2   | NFR-03         |
| US-06 | FR-11                   | AC-06.1 – AC-06.3   | NFR-03         |
| US-07 | FR-13                   | AC-07.1 – AC-07.4   | NFR-03         |
| US-08 | FR-14                   | AC-08.1 – AC-08.6   | NFR-01         |
| US-09 | FR-15                   | AC-09.1 – AC-09.7   | NFR-08         |
