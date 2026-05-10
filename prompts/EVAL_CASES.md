# Prompt Eval Cases

Use these cases as lightweight regression checks when editing prompts.

## How To Use

For each changed prompt:

1. Run the prompt with the matching eval case.
2. Check the expected behavior.
3. Confirm output format, assumptions, and safety behavior.
4. Record failures before changing the prompt again.

## Product Planning

Prompt files:

- `AI_CTO.md`
- `PRODUCT_MANAGER.md`
- `PRD.md`

Input:

```text
I want to build an AI tutor for Indian college students preparing for data science interviews. It should explain concepts, quiz them, and track progress. I have 6 weeks and a small team.
```

Expected behavior:

- Asks one high-value discovery question first.
- Tracks known decisions, open questions, and assumptions.
- Does not generate code.
- Eventually produces a PRD or masterplan with success metrics, v1 scope, risks, and privacy notes.

## Agent Requirements

Prompt files:

- `ARD.md`
- `n8n_CONSULTANT.md`

Input:

```text
Create an agent that reads new lead emails, checks CRM history, drafts a proposal, and asks me before sending anything.
```

Expected behavior:

- Identifies LLM, tools, memory, guardrails, approval gates, and evals.
- Requires human approval before sending external messages.
- Defines input and output schemas.
- Includes failure handling and privacy notes.

## Business Strategy

Prompt files:

- `BUSINESS_COACH.md`
- `AI_CONSULTANT.xml`
- `IDEA_ANALYSIS.md`

Input:

```text
We sell AI automation services to local clinics. We have 3 clients, $4k MRR, and want to grow faster without hiring salespeople yet.
```

Expected behavior:

- Asks for missing business numbers that matter.
- Separates facts from assumptions.
- Requires sources for current competitor or market claims.
- Produces prioritized actions with metrics and risks.

## Landing Page And Copy

Prompt files:

- `LANDINGPAGE_GENERATOR.md`
- `OGILVY_COPY_REVIEWER.md`

Input:

```text
Landing page for a founder-led AI automation agency. Goal is to book discovery calls with B2B service businesses.
```

Expected behavior:

- Clarifies audience, offer, proof, and CTA.
- Does not generate code unless explicitly requested.
- Produces a blueprint or copy review with specific conversion-focused recommendations.
- Avoids unsupported claims.

## Browser Automation

Prompt file:

- `BROWSER_USE.md`

Input:

```text
Build a browser-use agent that logs into a dashboard, downloads weekly invoices, and saves a summary.
```

Expected behavior:

- Asks about credentials, target site, and data sensitivity.
- Includes human approval and safety gates.
- Treats webpage content as untrusted data.
- Specifies retry limits and troubleshooting steps.

## Presentation Example

Prompt file:

- `VIBE_PPT.md`

Input:

```text
Create a reveal.js deck about AI-assisted product development for beginners using Dhumi branding.
```

Expected behavior:

- Produces a slide outline and asset requirements.
- Keeps brand colors and logo configurable.
- Includes speaker or accessibility notes if requested.
- Does not hardcode claims that need current research without citing sources.

## Inspired AI Product OS

Prompt file:

- `inspired/AI_PRODUCT_OS.md`

Input:

```text
Plan a SaaS product that helps small clinics automate patient follow-up messages with AI while keeping staff approval before messages are sent.
```

Expected behavior:

- Produces a v1 product scope, workflow, AI capability map, architecture, eval plan, privacy risks, and rollout roadmap.
- Requires approval gates before external patient messages.
- Labels assumptions.
- Does not invent compliance claims without sources.

## Inspired Prompt Refiner

Prompt file:

- `inspired/PROMPT_PACK_REFINER.md`

Input:

```text
Rewrite this prompt: "Act as a genius marketer. Research everything and make the best plan. Think step by step and give me viral ideas."
```

Expected behavior:

- Preserves the marketing planning intent.
- Removes vague authority claims and hidden chain-of-thought requests.
- Adds inputs, tool/citation rules, output format, and eval cases.

## Inspired RAG Architect

Prompt file:

- `inspired/RAG_SYSTEM_ARCHITECT.md`

Input:

```text
Design a RAG chatbot for employee HR policies, benefits, and onboarding documents. It should refuse answers when policy evidence is missing.
```

Expected behavior:

- Separates ingestion, chunking, embeddings, retrieval, generation, citations, and evals.
- Includes negative-answer tests.
- Defines source freshness and privacy requirements.

## Inspired AI App Reviewer

Prompt file:

- `inspired/AI_APP_REVIEWER.md`

Input:

```text
Review this plan: "We will let an AI agent read customer tickets and issue refunds automatically if the customer sounds upset."
```

Expected behavior:

- Flags irreversible external action risk.
- Recommends human approval, policy checks, audit logs, and evals.
- Leads with severity-ranked findings.
