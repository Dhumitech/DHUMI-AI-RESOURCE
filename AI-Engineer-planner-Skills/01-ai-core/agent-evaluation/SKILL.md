---
name: agent-evaluation
description: "Test, benchmark, and evaluate AI agents. Use when setting up quality metrics, latency thresholds, accuracy checks, or regression tests for any agent or LLM-powered system."
---

# Agent Evaluation

## Role

Evaluate LLM agents as probabilistic systems. The same input can produce different outputs, and correctness is often a rubric rather than a single exact string.

The goal is not a perfect pass rate on a tiny benchmark. The goal is to catch regressions, measure real task quality, expose unsafe behavior, and guide prompt/model/tool changes with evidence.

## Capabilities

- Behavioral regression testing
- Golden dataset design
- Rubric-based grading
- Tool-call correctness checks
- Safety and prompt-injection tests
- Latency, cost, and reliability metrics
- Production feedback loops

## Evaluation Layers

### 1. Unit-Like Prompt Tests

Use small fixed cases to check:

- instruction following
- output schema
- refusal or safety behavior
- missing-data behavior
- tone and formatting

### 2. Tool And Workflow Tests

Check:

- correct tool selection
- valid tool arguments
- bounded retries
- recovery from tool failures
- human approval gates
- no use of forbidden tools

### 3. Scenario Tests

Use representative end-to-end tasks with realistic context, edge cases, and adversarial inputs.

### 4. Production Monitoring

Track:

- task completion rate
- user correction rate
- gate pass rate
- latency and cost
- tool error rate
- escalation rate
- safety incident rate

## Dataset Design

Include:

- typical cases
- edge cases
- malformed inputs
- missing data
- conflicting instructions
- prompt-injection attempts in retrieved content
- high-risk tool-use scenarios
- known historical failures

Keep test data separate from training examples and prompt examples to avoid leakage.

## Grading Patterns

### Deterministic Checks

Use for:

- JSON/XML validity
- required fields
- allowed enum values
- exact labels
- tool-call schemas

### Rubric Checks

Use for:

- usefulness
- factuality
- completeness
- source support
- tone
- risk handling

### Pairwise Comparison

Use when comparing two prompt versions or model versions and the output is nuanced.

## Anti-Patterns

- Single-run testing only
- Happy-path-only tests
- Exact string matching for open-ended answers
- Benchmarks unrelated to production tasks
- No adversarial tests
- No cost or latency budgets
- Optimizing for the grader instead of the user task

## Sharp Edges

| Issue | Severity | Solution |
|---|---|---|
| Agent passes benchmark but fails production | high | Build evals from real production tasks and user corrections |
| Same test flips pass/fail | high | Run multiple samples and track distributions |
| Prompt update breaks tool calls | high | Add schema and tool-argument tests |
| Prompt injection succeeds via retrieved content | critical | Add adversarial retrieved-context tests |
| Test data appears in prompts | critical | Separate examples, evals, and training data |
| Metric is easy to game | medium | Use multi-dimensional rubrics and human review samples |

## Minimal Eval Plan Template

```markdown
# Eval Objective
What behavior are we measuring?

# Dataset
- 10 typical cases
- 5 edge cases
- 5 adversarial cases

# Metrics
- Format pass rate >= 98%
- Task success >= 85%
- Unsafe action rate = 0
- P95 latency <= target

# Regression Rule
Rerun this eval whenever the prompt, model, tool schema, retrieval source, or output contract changes.
```

## Related Skills

Works well with: `prompt-engineering`, `ai-agents-architect`, `ai-reliability`, `langfuse`, `mcp-builder`.
