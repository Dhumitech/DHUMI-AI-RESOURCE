# DHUMI AI Resource

Prompt packs, agent planning templates, and AI engineering skills used by Dhumi teams.

This repository is organized for practical reuse:

- `prompts/` contains role prompts and reusable prompt templates.
- `AI-Engineer-planner-Skills/` contains Codex-style skills for AI product planning, implementation, testing, security, deployment, and documentation.
- `Agent-Skills/` contains standalone operational skill notes.

## Start Here

1. Read `PROMPT_AUDIT.md` for the current quality review and improvement priorities.
2. Use `PROMPT_PACK_STANDARD.md` before adding or editing any prompt.
3. Browse `prompts/README.md` to select the right prompt pack for a task.
4. Treat prompts as product assets: version them, test them, and keep assumptions visible.

## Prompt Quality Baseline

Every production-ready prompt should define:

- Identity: the role, scope, and audience.
- Task: the exact outcome the model should produce.
- Context: the facts, source material, and boundaries available to the model.
- Constraints: tone, safety, privacy, tool-use, and model-specific limits.
- Output contract: Markdown, XML, JSON, tables, files, or schemas.
- Evaluation: acceptance criteria, failure cases, and regression checks.

## Maintenance Rules

- Prefer concise, direct instructions over broad persona claims.
- Use Markdown sections or XML tags to separate instructions from user data.
- Avoid asking models to expose chain-of-thought. Ask for concise rationale, checks, or final conclusions instead.
- For tool-using prompts, define allowed tools, approval gates, retry limits, and what to do when data is missing.
- Cite sources for market, competitor, legal, financial, or current-events claims.
