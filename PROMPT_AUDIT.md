# Prompt Pack Audit

Audit date: 2026-05-10

## Scope

Reviewed the repo from `Dhumitech/DHUMI-AI-RESOURCE` `upstream/main`:

- 24 files in `prompts/`
- 47 `SKILL.md` files in `AI-Engineer-planner-Skills/`
- 1 standalone Azure skill in `Agent-Skills/`

The repo is valuable, but the prompt layer needed a shared standard and a few targeted repairs before it could be treated as a maintainable prompt pack.

## Market Standard Signals Used

- Prompt assets should be versioned, templated, and evaluated before publishing.
- Strong prompts separate identity, instructions, examples, context, and output contracts.
- Reasoning models work best with clear direct prompts; do not force visible chain-of-thought.
- Long-context prompts should anchor the task to the supplied data and distinguish data from instructions.
- Tool and MCP prompts need injection handling, schema validation, auth boundaries, and approval gates.
- Production LLM workflows need eval datasets, adversarial cases, and continuous regression checks.

## High-Priority Findings

| Priority | Area | Finding | Action Taken |
|---|---|---|---|
| P0 | Repository docs | README did not explain the repository, quality bar, or how to use the prompt packs. | Expanded README and added a prompt catalog. |
| P0 | Prompt standards | No shared standard for prompt structure, tool use, evals, or safety. | Added `PROMPT_PACK_STANDARD.md`. |
| P0 | XML correctness | `prompts/AI_CONSULTANT.xml` had malformed XML tags and invalid closing tags. | Rewrote as valid XML with safer research and output rules. |
| P1 | Reasoning guidance | Several prompts encouraged visible chain-of-thought or excessive recursive reasoning. | Updated `IDEATION_AGENT.md` and `prompt-engineering/SKILL.md`. |
| P1 | Unrealistic research | `BUSINESS_COACH.md` required "300 hrs" and evidence for every claim without operational limits. | Replaced with a tool-aware, cite-or-label-unknown research protocol. |
| P1 | Base template | `TEMPLATE_PROMPT.md` was an MVP project brief, not a reusable modern prompt template. | Rebuilt it as a current prompt-pack template. |
| P1 | Agent quality | Agent guidance lacked current guardrails around tool permissions, budgets, and prompt injection. | Updated agent architecture and eval skills. |
| P2 | Resources | `RESOURCES.md` mixed older community resources with few official current references. | Updated with official docs and repo maintenance references. |

## Remaining Improvement Backlog

1. Normalize every prompt in `prompts/` to the standard metadata block:
   - name
   - use case
   - inputs
   - output artifact
   - evaluation notes
2. Add golden examples for each prompt pack.
3. Add a lightweight markdown linter or CI check for malformed XML/JSON fenced examples.
4. Split example artifacts from reusable prompt instructions, especially `MVP.md` and `VIBE_PPT.md`.
5. Add a `CHANGELOG.md` for prompt updates and behavioral changes.

## Verification Notes

This PR focuses on high-leverage prompt-pack hygiene rather than rewriting every prompt. It keeps existing repository structure intact and avoids deleting existing assets.

## Sources Consulted

- OpenAI Prompting: https://developers.openai.com/api/docs/guides/prompting
- OpenAI Prompt Engineering: https://developers.openai.com/api/docs/guides/prompt-engineering
- OpenAI Reasoning Best Practices: https://developers.openai.com/api/docs/guides/reasoning-best-practices
- OpenAI Evaluation Best Practices: https://developers.openai.com/api/docs/guides/evaluation-best-practices
- Anthropic Prompting Best Practices: https://platform.claude.com/docs/en/build-with-claude/prompt-engineering/claude-prompting-best-practices
- Google Gemini 3 Developer Guide: https://ai.google.dev/gemini-api/docs/gemini-3
- Model Context Protocol Prompts: https://modelcontextprotocol.io/specification/2025-06-18/server/prompts
- Model Context Protocol Security Best Practices: https://modelcontextprotocol.io/docs/tutorials/security/security_best_practices
