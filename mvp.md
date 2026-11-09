# MVP Scope – Smart Task Manager

## 1. MVP goal

The MVP proves the core idea: **a task list that prioritizes itself in a transparent way, while the user stays in control.**

It covers exactly one complete, demonstrable use case:

> **Capture → get a priority suggestion → work from a sorted list → override if needed → complete.**

Around this core, the MVP includes what makes it usable as a real personal tool: a private user account, deleting tasks, and filtering and searching the list. Everything else is deliberately left out so the MVP can be finished, tested and demonstrated reliably within the semester.

## 2. Included in the MVP

All **Must-have** stories from [requirements.md](requirements.md):

| Story | Function                                                          | Why it's essential                                                                            |
| ----- | ----------------------------------------------------------------- | --------------------------------------------------------------------------------------------- |
| US-01 | Create a task (title, optional description, due date, importance) | Without capture there is nothing to prioritize.                                               |
| US-02 | Automatic, rule-based priority suggestion with explanation        | The core value of the product.                                                                |
| US-03 | Open tasks sorted by effective priority                           | Turns the suggestion into an answer to "what next?".                                          |
| US-04 | Manual override and reset to suggestion                           | Users must stay in control; without this, wrong suggestions make the app unusable.            |
| US-05 | Mark task as done (with undo)                                     | Completes the use case; the list has to shrink as work gets done.                             |
| US-06 | Edit a task, with the suggestion recalculated                     | Deadlines and importance change, and the prioritization has to react.                         |
| US-07 | Delete a task (with confirmation)                                 | Mistaken or irrelevant entries must be removable, otherwise the list stops being trustworthy. |
| US-08 | Filter by priority and status, search by title                    | With a growing list, users need to find a specific task and review completed ones.            |

Plus the Must-have non-functional requirements: NFR-01 (performance), NFR-02 (usability), NFR-03 (reliability), NFR-04 (testability), NFR-05 (determinism), NFR-07 (privacy), NFR-08 (security) and NFR-10 (cost).

**Technical scope of the MVP**

- Web frontend running in the browser
- Backend with an HTTP API containing the prioritization logic (FR-12)
- Persistent database
- Automated tests for the prioritization logic and the API
- **Multi-user operation with accounts:** registration, login and logout; every user sees only their own tasks (see section 4)

## 3. Excluded from the MVP

| Function                                                     | Priority | Reason for exclusion                                                                                                                                                                                          |
| ------------------------------------------------------------ | -------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| Hint when the suggestion exceeds the manual priority (US-10) | Should   | Valuable refinement of US-04, but the override works without it.                                                                                                                                              |
| Effort estimate in the score (US-11)                         | Could    | Needs a rule that is validated with users first; risk of over-engineering the model.                                                                                                                          |
| AI-based natural-language input and prioritization (US-12)   | Could    | External dependency (AI API) with per-request costs, an API key to manage, and non-deterministic output, which conflicts with NFR-05 for the core logic. A good extension once the rule-based base is stable. |
| Voice input via a home device (US-13)                        | Could    | Needs hardware (Raspberry Pi + USB speakerphone, roughly €250–400 one-off), wake-word and speech-to-text setup. Large effort that is independent of the core idea.                                            |
| Reminders / notifications (US-14)                            | Could    | Requires scheduling and possibly e-mail/push infrastructure.                                                                                                                                                  |
| Team features, mobile apps, calendar sync                    | Won't    | Different product scope (collaboration / platforms).                                                                                                                                                          |

## 4. Key scope decisions

**Rule-based instead of AI-based prioritization.** A deterministic scoring model can be explained to the user ("High because due tomorrow"), tested exhaustively and costs nothing to run. An AI model would be harder to test and more expensive, and its output is harder to justify. AI is planned as an _addition_ (US-12) for natural-language input, not as a replacement for the transparent core.

**Date-only deadlines.** Due times (hours/minutes) would complicate urgency rules and the UI. Date-only covers most real deadlines (assignments, invoices, appointments). Due times can be added later if user feedback shows they are needed.

## 5. Demo scenario (definition of a successful MVP)

The MVP is complete when the following scenario runs end-to-end in the browser, and all acceptance criteria of US-01 to US-08 pass:

1. Create **"Submit course assignment"**: due tomorrow, importance HIGH. It is suggested as **HIGH**, with the explanation "due tomorrow, importance high".
2. Create **"Buy birthday present"**: due in 5 days, importance MEDIUM. It is suggested as **MEDIUM** and appears below the first task.
3. Create **"Read article on testing"**: no due date, importance LOW. It is suggested as **LOW** and appears at the bottom.
4. Override "Read article on testing" to **HIGH** manually. It moves to the top area and is marked as "set manually".
5. Edit "Buy birthday present" so it's due tomorrow. Its suggestion is recalculated to **HIGH** (score 55) and it moves up.
6. Reset "Read article on testing" to the suggestion. It returns to **LOW**.
7. Mark "Submit course assignment" as done. It disappears from the list. Undo brings it back, and marking it done again removes it.
8. Set the status filter to **Done**: only "Submit course assignment" is shown. Clear the filters, search for "birthday": only "Buy birthday present" is shown. Clear the filters again.
9. Create **"Old idea"**, choose delete, cancel (the task stays), then delete and confirm. It is gone, also after a reload.

## 6. After the MVP (planned order)

1. **Should-have:** US-10 hint when the suggestion exceeds the manual priority
2. **Could-haves**, if time remains: US-09 user account → US-12 AI-assisted input → US-14 reminders → US-11 effort → US-13 voice device
