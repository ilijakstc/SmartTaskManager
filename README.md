# Smart Task Manager

**Task management with automatic prioritization**

Smart Task Manager is a web application where users capture their tasks in the browser and immediately get a **suggested priority** for each one. The suggestion is calculated by the system from the task's deadline and importance. Users can accept it or set the priority themselves. The goal is to answer one question quickly and reliably: _"What should I work on next?"_

> Status: **Phase 1 – Requirements engineering.** No code yet. This repository contains the product specification that later phases will implement.

---

## 1. Problem statement

People who juggle several areas of responsibility, such as a job, university courses and personal errands, usually keep their tasks in a to-do list, a notes app or their head. These lists have three recurring problems:

1. **Everything looks equally important.** A flat list doesn't show which task matters most right now, so users spend time re-reading and re-sorting it, or work on whatever is easiest instead of whatever is most pressing.
2. **Priorities go stale.** A task that wasn't urgent last week becomes urgent as its deadline approaches. In a manually sorted list, the order doesn't change unless the user remembers to change it.
3. **Prioritizing is mental overhead.** Deciding priorities for every new task is a small decision that adds up. Many users skip it, and then the list stops being useful.

Existing tools such as Todoist, Microsoft To Do or Notion either leave prioritization entirely to the user or hide it behind paid "smart" features whose logic isn't transparent.

## 2. Solution and value

Smart Task Manager:

- **suggests a priority automatically** when a task is created, based on a transparent, rule-based scoring model (deadline proximity + importance),
- **explains each suggestion** ("High because: due tomorrow, marked as important"), so the user can trust or correct it,
- **keeps priorities up to date** as deadlines approach, without the user re-sorting anything,
- **leaves the final decision to the user**: any suggestion can be overridden manually, and the override is respected.

**Value:** the user opens one list that is already sorted by what matters most and spends less time organizing and more time doing.

## 3. Target users

**Primary target group:** individuals who manage their own workload across several responsibilities and regularly have more tasks than time. Typical examples:

| Persona                        | Situation                                                                                  | Main need                                                   |
| ------------------------------ | ------------------------------------------------------------------------------------------ | ----------------------------------------------------------- |
| **Working student**            | Part-time or full-time job plus courses with assignment deadlines and mandatory attendance | See at a glance which deadline is closest and most critical |
| **Freelancer / self-employed** | Several clients, admin tasks (invoices, tax deadlines), own projects                       | Keep client deadlines and admin duties from slipping        |
| **Busy professional**          | Mix of work tasks and personal errands                                                     | Low-effort capture; a list that sorts itself                |

**Out of scope as target group (for now):** teams that need shared task boards, assignment to others or project-management features. See [docs/mvp.md](docs/mvp.md).

## 4. Project goal

Within the semester, build and demonstrate a working web application (frontend + backend + persistent storage) that covers at least this complete use case:

> A user creates a task with a deadline and an importance level → the system suggests a priority with an explanation → the task appears in a priority-sorted list → the user can accept or override the priority → the user marks the task as done.

Success criteria for the project:

- The MVP use case above works end-to-end in a browser and can be demonstrated live.
- The prioritization logic is deterministic, documented and covered by automated tests.
- Every Must-have requirement in [docs/requirements.md](docs/requirements.md) has acceptance criteria that pass.

## 5. Constraints and assumptions

- **Architecture:** a browser-based frontend and a separate backend with an API and persistent database. The concrete technology stack is decided in Phase 2 (design), once the requirements are stable.
- **No mandatory paid services in the MVP.** The MVP runs without external APIs, so it causes no running costs and works without internet access to third parties.
- **Possible later extensions** with external dependencies, such as LLM-based prioritization via an AI API or voice input through a small hardware device, are documented as _Could-have_ together with their costs and dependencies. They are not part of the MVP.

## 6. Documentation

| Document        | Content                                                                                                |
| --------------- | ------------------------------------------------------------------------------------------------------ |
| requirements.md | User stories, functional and non-functional requirements, prioritization (MoSCoW), acceptance criteria |
| mvp.md          | MVP scope: included and excluded functionality, demo scenario                                          |
| ai-usage.md     | How AI was used in requirements engineering, selected prompts, results and critical evaluation         |
