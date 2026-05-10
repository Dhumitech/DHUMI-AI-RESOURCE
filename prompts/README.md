# Prompt Catalog

Use this catalog to select the right prompt pack and understand its output contract.

| Prompt | Use Case | Main Output |
|---|---|---|
| `AI_CTO.md` | Plan an application with a CTO-style discovery flow. | `masterplan.md` |
| `PRD.md` | Product discovery and product requirements. | `prd.md` |
| `PRODUCT_MANAGER.md` | Lightweight app planning and PRD creation. | `prd.md` |
| `ARD.md` | AI agent discovery and agent requirement planning. | `ARD.md` |
| `n8n_CONSULTANT.md` | n8n workflow and agentic automation planning. | n8n build plan and JSON blueprint |
| `AI_CMO.md` | Organic social strategy planning. | `growthplan.md` |
| `AI_CONSULTANT.xml` | Competitive strategy and growth analysis. | XML strategy report |
| `BUSINESS_COACH.md` | Evidence-backed business growth audit. | Growth plan and roadmap |
| `IDEA_ANALYSIS.md` | Ranking and improving startup or product ideas. | Idea scorecard and roadmap |
| `IDEATION_AGENT.md` | Iterative ideation without exposing hidden reasoning. | Idea evolution summary |
| `LANDINGPAGE_GENERATOR.md` | Landing page planning for beginners. | `masterplan.md` |
| `OGILVY_COPY_REVIEWER.md` | Landing page copy audit and rewrite. | Scorecard and rewritten copy |
| `OPT.md` | Beginner-friendly chatbot planning using OPT. | Assignment masterplan |
| `BROWSER_USE.md` | Browser-use agent planning from documentation. | Beginner implementation plan |
| `CLAUDE_INSTRUCTION.md` | Claude-style planning and review workflow. | Task plan file guidance |
| `DEEPSEEK.md` | Next.js assistant behavior prompt. | Implementation guidance |
| `MVP.md` | Example RAG MVP implementation plan. | Example plan |
| `TEMPLATE_PROMPT.md` | Canonical reusable prompt template. | Prompt draft |
| `VIBE_PPT.md` | Example reveal.js slide deck prompt. | Slide deck specification |
| `SELF_DISCOVERY.md` | Guided self-discovery assessment. | Personal insight report |
| `RESOURCES.md` | Prompt learning and reference links. | Resource index |

## Selection Rules

- Use `PRD.md` or `PRODUCT_MANAGER.md` for product requirements.
- Use `ARD.md` for AI agents with tools and memory.
- Use `n8n_CONSULTANT.md` when the implementation target is n8n.
- Use `TEMPLATE_PROMPT.md` as the base for new prompt packs.
- Use `PROMPT_PACK_STANDARD.md` from the repo root before modifying prompts.

## Review Checklist

Before publishing a prompt, confirm:

- The task and output artifact are named.
- Required inputs are clear.
- The prompt states whether browsing, citations, or tools are required.
- The output format is valid Markdown, XML, or JSON.
- Hidden reasoning is not requested.
- Evaluation criteria or test cases are included for production use.
