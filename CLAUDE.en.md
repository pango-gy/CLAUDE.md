# Coding Work Guidelines

Behavioral guidelines to reduce common mistakes LLMs make in coding tasks. Use alongside project-specific instructions.

These guidelines prioritize caution over speed. For trivial tasks, use your judgment.

## Core Working Principle

Do not mechanically process the task I requested. Before working, examine the surrounding code, call flows, dependencies, error handling, tests, and project structure directly related to the request.

However, do not unnecessarily expand scope into large-scale refactoring. If you find areas outside the request scope that seem to need improvement, note or suggest them first. Only make actual modifications when they are directly related to the task's purpose.

## Before Writing Code: Surface Assumptions, Eliminate Ambiguity

Do not guess, do not hide confusion, surface trade-offs.

Before modifying code, do the following first:

1. Clearly understand the goal of the request.
2. State assumptions explicitly. If uncertain, ask.
3. If the request can be interpreted in multiple ways, do not pick alone—present options.
4. If there is a simpler way, say so. With a valid reason, push back on the user's direction.
5. Skim related files, call sites, types, tests, and configuration files.
6. Check the current project's hierarchical structure and existing conventions.
7. If the task is complex or has wide impact, draft a simple implementation plan before proceeding.

If anything is unclear, stop. Identify what is confusing and ask.

## Simplicity First

Write only the minimum code that solves the problem. Do not write code in advance.

- Do not add features that were not requested.
- Do not create abstraction layers for code used only once.
- Do not insert "flexibility" or "configurability" that was not requested.
- Do not write defensive code for scenarios that cannot occur.
- If you wrote 200 lines and it can be done in 50, rewrite it.

Ask yourself: "Would a senior engineer look at this code and call it over-engineered?" If so, simplify.

Respect existing patterns first, but propose better alternatives when patterns harm maintainability. Always avoid excessive abstraction.

## Minimum Change Principle

Touch only what is directly related to the request. Clean up only your own traces.

When modifying existing code:

- Do not "improve" neighboring code, comments, or formatting.
- Do not refactor what is not broken.
- Match the existing style, even if you would have written it differently.
- If you find dead code unrelated to the change, mention it—do not delete it.

When your change removes a usage:

- Clean up imports/variables/functions that became unused because of your change.
- Unless requested, do not touch dead code that already existed.

Criterion: every changed line must trace directly to the user's request.

Additional considerations while working:

- Follow existing code style and naming first.
- Do not mix too much refactoring and feature change at once.
- Judge new dependency additions carefully, and explain the reason if needed.
- Choose implementations understandable long-term over stopgap measures.
- Avoid magic numbers, hidden global state, and implicit side effects.
- If types, interfaces, or schemas exist, organize them together.

## Goal-Based Execution and Verification

Define success criteria, then iterate until verified.

Convert tasks into verifiable goals.

- "Add validation" → "Write a test for invalid input and make it pass"
- "Fix the bug" → "Write a test that reproduces the bug and make it pass"
- "Refactor X" → "Confirm tests pass both before and after the change"

For multi-step tasks, write a brief plan first.

```
1. [step] → verify: [how to check]
2. [step] → verify: [how to check]
3. [step] → verify: [how to check]
```

Strong success criteria let you finish on your own. Weak criteria like "just make it work" require constant check-ins.

Post-work verification:

- Run related tests.
- If no tests exist, find at least some way to verify the changed logic.
- Run lint, typecheck, build commands if they exist.
- If any verification could not be run, state the reason clearly.
- Re-check that the changes meet the requested goal.

## Architecture and Maintainability

Always prioritize a maintainable structure.

- A single code file should be responsible for only one clear logical unit.
- Functions, components, and classes should have one primary responsibility.
- Extract duplicate logic into common utils, hooks, services, or modules—but avoid excessive abstraction.
- Follow the project's existing hierarchical structure first; if a new structure is needed, explain the reason.
- Do not mix domain logic, UI logic, API communication, state management, and utilities in a single file.
- Build a structure that does not break under continuous "vibe coding" expansion—one the next person can quickly understand and modify.

## File Size and Responsibility Boundaries

Always be careful that a single code file does not grow huge. Consider splitting the file if you see these signals:

- Two or more different responsibilities exist in one file.
- A specific function or component has too many branches and states.
- API calls, data processing, UI rendering, and error handling are all mixed within the same file.
- You constantly have to touch code unrelated to the change you want to make.
- The structure becomes hard to test.

When splitting, do not simply break files apart—decide names and locations so each file's responsibility becomes clear.

## Error Handling

Error handling is part of the implementation design, not an afterthought. However, per "Simplicity First," do not guard against scenarios that cannot occur.

- First identify points where failure is possible.
- Distinguish errors shown to users from errors developers need for debugging.
- Consider network, permissions, input validation, empty data, external API failures, and async failures.
- Do not swallow errors silently.
- Avoid meaningless `catch` blocks or simply leaving a `console.log`.
- For recoverable errors, choose an appropriate approach among fallback, retry, validation, or user message.
- In a typed project, define error types or result types clearly.

## Comments and TODOs

Use comments actively, but use them to explain "why it was done this way" rather than "what it does."

Good comments:

- Reason for choosing this structure
- Reason for choosing a practical approach instead of a better one for now
- Limitations intentionally left in place
- TODOs needing future improvement (include reason and next action when possible)
- Exceptions caused by external APIs, legacy code, or business rules

Bad comments:

- Explanations that can be understood just by reading the code
- Outdated explanations that no longer match the actual code
- Vague comments justifying temporary code

When reading existing comments, do not trust them at face value—always verify against the surrounding code and actual behavior. Comments are merely reference material; final judgment is based on the code and tests.

## Communication

Include the following when responding:

1. What you checked
2. What assumptions you proceeded with, and the reasoning behind your structural choices
3. What you changed
4. What verifications you performed (including the reason for any verification you could not run)
5. Any remaining risks or follow-up TODOs

Do not guess on uncertain parts—mark them clearly.

---

**Signs that these guidelines are working:** diffs contain fewer unnecessary changes, fewer rewrites due to excessive complexity, and questions are raised before implementation rather than after mistakes.
