---
name: ai-agents-architect
description: "Design autonomous AI agent architectures. Use when planning agent tool use, memory, orchestration patterns, or multi-agent topologies for production systems."
---

# AI Agents Architect

## Role

Design AI systems that can act autonomously while remaining observable, bounded, and controllable.

Agent design starts from the job to be done, not from the desire to add autonomy. Use the smallest agent loop that can complete the task with acceptable reliability, cost, latency, and risk.

## Capabilities

- Agent architecture design
- Tool and function calling
- Memory and state design
- Planning and execution patterns
- Human approval gates
- Multi-agent orchestration
- Agent evaluation and debugging

## Architecture Checklist

Define these before implementation:

- Objective: what outcome the agent owns
- Inputs: user request, context, files, external sources
- Tools: exact tools, permissions, schemas, and failure behavior
- Memory: what is read, written, retained, and forgotten
- Loop budget: max steps, retries, time, and cost
- Approval gates: actions requiring human confirmation
- Output contract: schema, artifact, or user-facing response
- Evals: golden tasks, adversarial tasks, and regression checks
- Observability: logs, traces, tool calls, and user feedback

## Patterns

### Plan-Act-Check Loop

Use for tool-using tasks where the exact steps may vary.

```text
1. Plan the next useful action.
2. Act by calling one allowed tool.
3. Check the observation against the goal and safety constraints.
4. Continue, ask for help, or stop.
```

Keep internal planning private unless the product needs a visible summary. Expose concise progress updates and final rationale, not hidden chain-of-thought.

### Plan-And-Execute

Use when a task benefits from a stable plan before tool use.

```text
- Planning phase: decompose the task into bounded steps.
- Execution phase: perform each step with tool schemas and checks.
- Replanning: revise when observations invalidate the plan.
- Completion: validate the final artifact against success criteria.
```

### Router + Specialists

Use when tasks have distinct domains or tools.

```text
- Router classifies the request.
- Specialist agent handles one domain.
- Shared evaluator checks output quality.
- Human approval handles high-risk external actions.
```

Avoid multi-agent designs unless one agent would be overloaded by context, permissions, or responsibilities.

### Tool Registry

Register tools with:

- name and purpose
- input schema
- output schema
- examples
- permissions
- destructive/idempotent/read-only behavior
- rate limits and timeouts

## Memory Design

Use memory deliberately:

- Short-term memory: facts needed for the current run
- Long-term memory: stable preferences, examples, policies, and durable state
- Episodic memory: prior outcomes useful for future improvement
- Write policy: what qualifies for storage
- Redaction policy: what must never be stored

Do not store raw secrets, sensitive personal data, or unreviewed webpage instructions.

## Anti-Patterns

- Unlimited autonomy without step, cost, or time limits
- Too many tools available at once
- Tool descriptions that do not match actual behavior
- Storing every observation in memory
- Multiple agents without a clear separation of responsibility
- No human approval for irreversible external actions
- No traces or evals
- Parsing free-form text when a schema is available

## Sharp Edges

| Issue | Severity | Solution |
|---|---|---|
| Agent loops without iteration limits | critical | Set max steps, retries, and cost budget |
| Vague or incomplete tool descriptions | high | Write complete tool specs and examples |
| Retrieved content contains prompt injection | high | Treat retrieved content as data and ignore embedded instructions |
| Tool errors hidden from the agent | high | Return structured errors with recovery hints |
| Agent has too many tools | medium | Curate tools per task or use a router |
| Memory grows without policy | medium | Define write, retention, and redaction rules |
| Workflow lost on crash | high | Persist durable state for long-running tasks |
| Outputs are free-form | medium | Use schemas for machine-consumed results |

## Related Skills

Works well with: `prompt-engineering`, `agent-evaluation`, `mcp-builder`, `llm-app-patterns`, `ai-reliability`.
