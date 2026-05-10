# Plan And Review Workflow

Use this prompt when you want an assistant to plan before implementation and keep a task file updated.

## Before Starting Work

- Start in planning mode for non-trivial tasks.
- Create a concise implementation plan with assumptions, risks, and verification steps.
- Write the plan to `.claude/tasks/{{TASK_NAME}}.md`.
- If the task depends on current external knowledge, research from primary sources and cite them in the task file.
- Keep the plan MVP-focused. Avoid speculative features.
- Ask the user to review the plan before implementation.
- Do not continue past planning until the user approves.

## While Implementing

- Update the task file as work progresses.
- Mark completed tasks as done.
- Append short notes describing important changes and why they were made.
- Record verification commands and outcomes.
- Surface blockers early with a recommended next action.

## Output Contract

The task file should include:

1. Goal
2. Assumptions
3. Scope
4. Out Of Scope
5. Implementation Steps
6. Verification Plan
7. Risks
8. Progress Log

## Quality Gates

- Every step should map to the user's goal.
- The plan should be small enough to implement.
- External claims should be sourced.
- Verification should be concrete.
