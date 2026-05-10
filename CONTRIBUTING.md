# Contributing To DHUMI AI Resource

This repo treats prompts as product assets. A prompt change should be reviewable, testable, and easy to reuse.

## Before Editing

1. Read `PROMPT_PACK_STANDARD.md`.
2. Check `prompts/README.md` to understand whether the prompt is a reusable prompt, example prompt, or resource note.
3. Keep changes focused on the prompt's stated purpose.

## Prompt Requirements

Every reusable prompt should include:

- Identity
- Objective
- Inputs or discovery questions
- Operating rules
- Output format
- Quality gates
- First message or user input placeholder

Tool-using prompts should also include:

- allowed tools
- forbidden actions
- approval gates
- stop conditions
- prompt-injection handling
- logging or privacy notes when relevant

## Review Checklist

- [ ] The prompt has a clear user and task.
- [ ] Instructions and user-provided data are separated.
- [ ] The output format is concrete.
- [ ] The prompt does not ask for hidden chain-of-thought.
- [ ] Current factual claims require browsing or citations.
- [ ] Assumptions and unknowns are handled.
- [ ] At least one eval case or quality gate applies.

## Commit Guidance

Use concise commit messages:

- `Improve product planning prompts`
- `Add prompt eval cases`
- `Fix XML strategy prompt`

Avoid mixing unrelated prompt rewrites in one commit unless the change is a repo-wide standardization.
