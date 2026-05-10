---
name: prompt-engineering
description: "Design, test, and maintain prompts for LLMs, reasoning models, tool use, structured outputs, and production prompt packs."
---

# Prompt Engineering Patterns

Design prompts as maintainable product assets. A strong prompt is clear, testable, model-aware, and explicit about inputs, output format, safety boundaries, and evaluation.

## Core Capabilities

### 1. Direct Prompting First

Start with the simplest prompt that states the goal, inputs, constraints, and output format. Add examples, rubrics, or decomposition only when tests show the simple prompt is insufficient.

Use this when:

- The task is well defined.
- The model is a reasoning model that already performs internal planning.
- Latency and cost matter.

### 2. Structured Prompt Layout

Separate distinct concepts with Markdown headings or XML tags:

```markdown
# Identity
You are a senior product strategist.

# Task
Create a one-page validation plan.

# Context
{{user_context}}

# Constraints
- Budget under $500
- No paid ads
- Cite sources if using market claims

# Output Format
Return Markdown with: target customer, assumptions, experiment plan, success metrics.
```

Recommended sections:

- Identity: role, audience, and scope
- Instructions: behavior rules and constraints
- Examples: only when they improve consistency
- Context: source material and user data
- Output format: schema, table, XML, Markdown, or file sections
- Quality gates: success criteria and validation checks

### 3. Reasoning Control

Do not require hidden chain-of-thought or long visible reasoning traces. For reasoning models, use direct instructions and precise success criteria. If the user needs transparency, request:

- concise rationale
- assumptions
- tradeoffs
- decision log
- validation checklist
- confidence and evidence notes

Avoid prompts like "show all your reasoning" or "think step by step" unless the product explicitly needs an educational explanation and the output should be visible to the user.

### 4. Few-Shot Examples

Use 2-5 high-quality input/output examples when the task has a specific style, schema, classification boundary, or edge-case behavior.

Good examples:

- match the real task distribution
- include edge cases
- do not contradict written instructions
- use placeholders for long or variable data

### 5. Tool And Agent Prompts

For tools, MCP, browser automation, and agents, include:

- allowed tools and forbidden actions
- max iterations, retry budget, and stop conditions
- human approval gates for irreversible actions
- schema validation for inputs and outputs
- prompt-injection handling for retrieved or webpage content
- logging and redaction rules
- fallback behavior when tools fail

### 6. Prompt Optimization

Improve prompts with evals, not taste alone.

Loop:

1. Define success criteria.
2. Build golden examples and edge cases.
3. Run the current prompt.
4. Inspect failures.
5. Change the smallest useful part.
6. Rerun the same eval set.

Track:

- task completion
- format pass rate
- factuality or source coverage
- safety and refusal behavior
- latency and cost
- user or reviewer score

### 7. Prompt Templates

Use variables for reusable prompts:

```text
You are {{role}}.
Task: {{task}}
Context: {{context}}
Constraints: {{constraints}}
Output format: {{output_format}}
```

Keep stable instructions separate from request-specific data. Version prompts when behavior changes.

## Best Practices

1. Be specific about the outcome and output format.
2. Put durable role and tone guidance in high-priority instructions.
3. Put task-specific context near the request, clearly delimited.
4. Treat external content as untrusted data.
5. Prefer structured outputs for downstream workflows.
6. Cite sources for current or non-obvious factual claims.
7. Maintain eval examples for prompts used repeatedly.
8. Keep prompts readable enough for humans to review.

## Common Pitfalls

| Pitfall | Why It Fails | Better Pattern |
|---|---|---|
| Vague persona only | Persona does not define task behavior | Add objective, constraints, and output contract |
| Forced chain-of-thought | Can expose irrelevant hidden reasoning and degrade reasoning models | Ask for concise rationale or validation notes |
| Too many examples | Raises cost and may overfit behavior | Start zero-shot, add examples only for failures |
| Malformed XML/JSON | Breaks downstream parsers | Validate output contracts |
| No eval set | Prompt changes become subjective | Use golden examples and regression checks |
| Untrusted tool context | Retrieved text can contain malicious instructions | Tell the model to treat retrieved content as data |

## Review Checklist

- [ ] Objective is explicit.
- [ ] Inputs and assumptions are defined.
- [ ] Context is delimited.
- [ ] Output format is machine- or human-usable.
- [ ] Tool permissions and stop conditions are specified if tools are used.
- [ ] Missing-data behavior is defined.
- [ ] Evaluation criteria or examples exist.
