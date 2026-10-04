# Chapter 8 – Agents

## Learning Objectives

After this chapter you should be able to:

- Explain what an agent is.
- Distinguish a chain from an agent.
- Understand tools and tool selection.
- Explain the agent execution loop.
- Understand why agents are useful.
- Recognize when an agent is unnecessary.
- Identify reliability and safety problems.

## 1. What Is an Agent?

A traditional chain follows a predefined sequence:

```text
Input
 ↓
Step 1
 ↓
Step 2
 ↓
Step 3
 ↓
Output
```

An agent dynamically decides what action to take next based on the current state.

```text
Input
 ↓
LLM
 ↓
Choose action
 ↓
Tool
 ↓
Observation
 ↓
LLM
 ↓
Choose next action
 ↓
...
 ↓
Final answer
```

The key difference is dynamic decision-making.

## 2. Chain vs Agent

### Chain

The developer defines the workflow.

```text
Prompt
 ↓
Model
 ↓
Parser
 ↓
Result
```

### Agent

The model chooses which available capability to use.

```text
User
 ↓
Agent
 ├── Search
 ├── Database
 ├── Calculator
 └── API
 ↓
Answer
```

A chain is generally more predictable. An agent is more flexible.

## 3. Why Agents Exist

Suppose a user asks:

> Find the current weather in Kolkata, convert the temperature to Fahrenheit, and explain whether I should carry an umbrella.

The system may need several capabilities. A fixed chain can implement this, but if users can request many different combinations of tasks, dynamically selecting tools becomes useful.

## 4. Agent Architecture

```text
                 ┌─────────────┐
                 │    User     │
                 └──────┬──────┘
                        ↓
                 ┌─────────────┐
                 │    Agent    │
                 │    LLM      │
                 └──────┬──────┘
                        ↓
                 Choose action
                        ↓
        ┌───────────────┼───────────────┐
        ↓               ↓               ↓
     Search          Database         API
        │               │               │
        └───────────────┼───────────────┘
                        ↓
                   Observation
                        ↓
                      Agent
                        ↓
                 Final Response
```

## 5. The Agent Loop

The central concept is:

```text
Decide
  ↓
Act
  ↓
Observe
  ↓
Decide again
  ↓
Act
  ↓
Observe
  ↓
...
  ↓
Finish
```

Modern frameworks may hide the implementation details, but this conceptual loop is essential.

## 6. Tools

Agents normally have access to tools such as:

```text
search_web()
get_customer()
query_database()
send_email()
calculate()
```

The model decides when a tool is appropriate. The application executes the tool.

```text
LLM = decision maker
Tool = controlled capability
```

## 7. Tool Selection

If the available tools are:

```text
weather()
calculator()
search()
```

and the user asks:

> What is 23% of 450?

The appropriate capability is the calculator. The model's job is to select the capability and provide arguments; the application executes it.

## 8. Agent State

An agent needs enough state to understand:

- Original request
- Previous actions
- Tool results
- Current progress
- Remaining task

Conceptually:

```text
Agent State
├── User input
├── Messages
├── Tool calls
├── Tool results
└── Final response
```

State becomes especially important in multi-step workflows.

## 9. Agent vs RAG

RAG:

```text
Question
 ↓
Retrieve
 ↓
Generate
```

Agent:

```text
Question
 ↓
Decide
 ↓
Tool
 ↓
Observe
 ↓
Decide
 ↓
...
```

RAG can itself be a capability exposed to an agent.

For example:

```text
Agent
 ├── Company Knowledge Search
 ├── Customer Database
 └── External Web Search
```

## 10. When to Use an Agent

Agents are useful when:

- The workflow is not known in advance.
- Multiple tools may be required.
- Tool choice depends on the request.
- Tasks require iterative actions.
- The number of possible paths is large.

## 11. When NOT to Use an Agent

Do not use an agent merely because it sounds more advanced.

If the workflow is:

```text
Input
 ↓
Retrieve
 ↓
Prompt
 ↓
LLM
 ↓
Output
```

a chain is usually simpler.

If the workflow is always:

```text
Validate
 ↓
Call API A
 ↓
Call API B
 ↓
Store result
```

write a deterministic workflow.

Agents introduce additional latency, token usage, failure modes and testing complexity.

## 12. Tool Calling

Modern chat models can produce structured tool calls.

Conceptually:

```json
{
  "tool": "get_customer",
  "arguments": {
    "customer_id": "123"
  }
}
```

The application then executes the tool:

```text
LLM
 ↓
Tool Call
 ↓
Application
 ↓
Tool
 ↓
Tool Result
 ↓
LLM
```

The model does not get arbitrary permission to execute application code.

## 13. Safety Boundary

Never assume:

```text
LLM requested action
=
action should execute
```

Instead:

```text
LLM
 ↓
Requested action
 ↓
Validation
 ↓
Authorization
 ↓
Tool execution
```

For destructive actions, the backend may also require confirmation, approval or additional policy checks.

## 14. Agent Reliability

Agents are probabilistic and can fail by:

- Selecting the wrong tool.
- Producing invalid arguments.
- Repeating tool calls.
- Looping.
- Making unsupported assumptions.
- Declaring success prematurely.

Production agents therefore need timeouts, iteration limits, validation, logging, tracing and error handling.

## 15. Example

User:

> Check whether customer 123 has an overdue invoice and send them a reminder.

Possible flow:

```text
Agent
 ↓
get_customer(123)
 ↓
Observation
 ↓
get_overdue_invoices(123)
 ↓
Observation
 ↓
send_reminder(123)
 ↓
Final response
```

A deterministic workflow could implement the same sequence. An agent is justified when the sequence or available actions genuinely vary by request.

## 16. Backend Analogy

Traditional backend:

```text
Controller
 ↓
Service
 ↓
Repository
 ↓
Database
```

Agentic backend:

```text
User
 ↓
Agent / Orchestrator
 ↓
LLM decides
 ↓
Tool
 ↓
External System
```

The major difference is that conventional services follow deterministic code while an agent dynamically chooses actions.

## 17. Best Practices

- Give agents narrowly defined tools.
- Validate tool arguments.
- Set iteration and time limits.
- Apply authorization outside the LLM.
- Log every tool call.
- Make destructive actions explicit.
- Prefer deterministic workflows when possible.
- Keep tools independently testable.

## 18. Common Mistakes

### Using agents everywhere

More autonomy is not automatically better.

### Giving one agent too many tools

Tool selection becomes harder and less predictable.

### Trusting model-generated arguments

Validate them before execution.

### Letting the model authorize actions

Authorization belongs to the application.

### No iteration limit

A faulty agent can loop indefinitely.

## 19. Interview Questions

### Beginner

1. What is an agent?
2. Difference between a chain and an agent?
3. What is a tool?
4. What is tool calling?

### Intermediate

5. Why does an agent need state?
6. How does an agent decide which tool to use?
7. Can RAG be used inside an agent?
8. Why are agents less predictable than chains?

### Advanced

9. How would you secure a tool-using agent?
10. How would you prevent infinite loops?
11. How would you evaluate tool selection?
12. When would you replace an agent with a deterministic workflow?
13. How would you design an agent with many tools?

## 20. Checkpoint Summary

- An agent dynamically chooses actions.
- Tools provide capabilities.
- The agent loop is decide → act → observe → repeat.
- Tool execution belongs to the application, not the LLM.
- Authorization must remain outside the model.
- Agents are powerful but less predictable than deterministic chains.
- Use an agent when dynamic decision-making genuinely adds value.

## 21. Mind Map

```text
Agent
│
├── LLM
│   └── Decision
│
├── Tools
│   ├── Search
│   ├── Database
│   ├── API
│   └── Calculator
│
├── Loop
│   ├── Decide
│   ├── Act
│   ├── Observe
│   └── Repeat
│
├── State
│   ├── Messages
│   ├── Actions
│   └── Results
│
└── Safety
    ├── Validation
    ├── Authorization
    ├── Limits
    └── Logging
```

## 22. 2-Minute Revision

```text
Chain:
Developer decides the workflow.

Agent:
LLM decides the next action.
```

And:

```text
LLM
 ↓
Tool Call
 ↓
Validate
 ↓
Execute Tool
 ↓
Observation
 ↓
LLM
```

The production principle:

> The model can request an action; the application decides whether that action is actually allowed to execute.
