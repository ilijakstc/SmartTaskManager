# AI Usage and Reflection – Smart Task Manager

This document records how AI was used in the requirements-engineering process, the prompts used, what came out of them, and how the results were evaluated and corrected.

> **Responsibility:** The AI produced drafts and review findings. The decisions about the product, its scope and its priorities are ours. Sections marked _✏️ Completed by the student group_ contain our own assessment and were not AI-generated.

**Tool used:** Claude (Anthropic), model Claude Opus 5.5, via the claude.ai chat interface.

---

## AI use 1 – Exploring the idea and its technical feasibility

**Purpose:** Before writing requirements, understand whether the long-term vision (voice input "Hey Task Manager, add a task…", AI assistance) is realistic and what it would cost, so it could be prioritized correctly in the scope.

**Prompts (summarized, from earlier sessions):**

- "Can you give me an overview of how features like 'Hey Siri' or 'Alexa' work?"
- "Is it possible to have a small device that reacts to voice activation, is connected to the internet, takes voice commands, sends them to an API to be converted to text and stores them in a database?"

**Results:**

- Explanation of on-device wake-word detection, speech-to-text and intent parsing.
- A possible architecture: Raspberry Pi 5 + USB speakerphone, openWakeWord ("Hey Task Manager"), local Whisper speech-to-text, sending only text to my backend. Optionally, an AI API with tool use for natural-language commands.
- Hardware and cost picture (roughly €250–400 one-off for the device, plus per-use costs for an AI API).

**How the result was used:** It showed that voice input and AI parsing are feasible, but that each one is a sizeable project of its own with hardware, external dependencies and costs. **Decision:** they are Could-haves (US-12, US-13) and explicitly excluded from the MVP ([mvp.md](mvp.md), section 3). The rule-based prioritization stays the core.

✏️ _Completed by the student group:_ Did this exploration change how you saw the project? Was anything the AI suggested unrealistic for a semester project?

Exploration has clearly shown core product value and possible future extensions of the product:

- Setting up a small home device that would run speech-to-text conversion and then store the tasks in the Task Manager database.

- Setting up AI-model “middleman” (US-12 AI-assisted input) that would handle task-related commands given from speaker device, as well as questions/inquiries directed to AI model and not related to tasks.

The suggestions were not unrealistic in terms of feasibility, but would require noticeable financial investment for the hardware and much more time to implement and test, which would probably not fit into the remaining 2.5 months of this semester.

---

## AI use 2 – Generating the specification draft from the project idea

**Purpose:** Turn the rough idea into a structured first draft: problem statement, target users, user stories, functional and non-functional requirements, MoSCoW prioritization, acceptance criteria and MVP scope.

**Prompt (original):**

> _"Now let's focus on the following: the plan is to build an application called 'Smart Task Manager – Task Management with automatic prioritization'. This application should have backend and frontend where user can input their tasks via web app (in a browser) and get an automatic prioritization suggested by a system or user can prioritize on their own. Below is the full description of what needs to be done. Input is a mix of English and German, output should be in English."_
>
> \+ attached: the full Phase 1 assignment description (goals, TODOs, deliverables, quality requirements, typical mistakes, evaluation criteria).

**Result:** First versions of all four documents, including 14 user stories (6 Must, 4 Should, 4 Could, plus a Won't list), 20 functional and 10 non-functional requirements, a rule-based scoring model with 12 reference examples, and a demo scenario.

**Notable choices the AI made that needed checking:**

- It interpreted "automatic prioritization" as a **deterministic rule-based score** rather than AI/ML, and justified this with testability and transparency.
- It kept the Must list small (6 of 14 stories), moving "delete task" to Should because completing a task already removes it from the list.
- It proposed a **single-user MVP without login**, with the condition that accounts become Must if the demo is deployed publicly.
- It invented concrete values: score points, thresholds 55/30, field limits of 200/2,000 characters and NFR targets (500 ms, 30 s).

✏️ _Completed by the student group:_ Which of these choices do you agree with, which did you change, and why? Are the invented numbers (thresholds, limits, performance targets) values you can justify?

- Disagree with prioritization for US-09: rather classification as Should.

- MVP-scope was extended: instead of six US, we decided for eight in order to cover task deletion and filter & search.

- We decided to keep the proposed score points, thresholds and field limits.

---

## AI use 3 – Checking the requirements for contradictions, gaps and ambiguity

**Purpose:** A separate review pass over the draft, specifically looking for contradictions, untestable requirements and missing decisions, as required by the assignment.

**Review instruction** (the AI ran this review in the same session, directly after drafting):

> _"Review the requirements, user stories and MVP scope for contradictions, gaps, ambiguities and requirements that cannot be tested. For each finding, state the problem and propose a fix."_

**Findings and resulting changes:**

| #   | Finding                                                                                                                                                                      | Type                        | Change made                                                                                                                                                                                  |
| --- | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | --------------------------- | -------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| 1   | "Automatic prioritization" was ambiguous: AI, machine learning, or rules? Without a defined model, US-02 couldn't be tested.                                                 | Ambiguity                   | Defined a deterministic scoring model with point tables, thresholds and 12 reference examples (requirements.md, section 2), and made the examples part of the acceptance criteria (AC-02.2). |
| 2   | Conflict between manual override and automatic recalculation: when a deadline approaches, does the manual priority get overwritten?                                          | Contradiction               | Manual priority always wins (AC-04.2). Introduced the "effective priority" term. Added US-10 (Should) to warn when the suggestion exceeds the manual value.                                  |
| 3   | FR-04 (urgency based on the _user's local date_) conflicted with FR-12 (all logic in the backend): the backend doesn't know the user's time zone.                            | Contradiction               | FR-04 now states that the frontend sends the time zone or local date with each request and the backend calculates.                                                                           |
| 4   | NFR-02 originally required "≤ 4 interactions" to create a task. A date picker alone can take several clicks, so the target was unrealistic and unclear (does typing count?). | Untestable                  | Changed to a time target (≤ 30 s) plus a single-form constraint, verified with ≥ 3 test users.                                                                                               |
| 5   | "No login in the MVP" contradicts data protection if the app is deployed on a public server for the demo.                                                                    | Contradiction (conditional) | Made it an explicit condition in mvp.md: if deployed publicly, US-09 becomes Must. _(Later superseded by the student group's decision to include accounts in the MVP.)_                      |
| 6   | Tasks without a due date can never reach HIGH automatically. Bug or intention?                                                                                               | Gap                         | Documented as a deliberate design decision (requirements.md, section 2.3); users can override manually.                                                                                      |
| 7   | Undefined behavior for due dates in the past at creation.                                                                                                                    | Gap                         | AC-01.5: allowed, shown as overdue immediately.                                                                                                                                              |
| 8   | Score boundaries (e.g. exactly 55 or 30) weren't covered by tests.                                                                                                           | Gap in testability          | NFR-04 now requires tests for the boundaries 29/30 and 54/55.                                                                                                                                |

✏️ _Completed by the student group:_ Did you run the independent review? What did it find that this one didn't?

We have reviewed each document as a group and dicussed about pros, cons and possible inconsistencies.

---

---

## Summary of AI contributions

| Area                 | AI contribution                                                          | Student contribution                                      |
| -------------------- | ------------------------------------------------------------------------ | --------------------------------------------------------- |
| Idea and feasibility | Explained voice-assistant technology, proposed hardware and architecture | ✏️ Project idea, decision to keep voice/AI out of the MVP |
| Specification draft  | Structure, first versions of stories, requirements, scoring model, MVP   | ✏️ Review, corrections, final decisions                   |
| Validation           | 8 findings with proposed fixes (table above)                             | ✏️ Accepted/rejected fixes, independent re-check          |
