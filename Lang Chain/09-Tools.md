# Chapter 9 – Tools

## Learning Objectives

After this chapter you should be able to:

- Explain what a tool is.
- Understand how tools are exposed to an LLM.
- Define tool inputs with schemas.
- Understand the tool-calling lifecycle.
- Distinguish tools from ordinary functions.
- Design safe and reliable tools.
- Understand how tools fit into agents.

## 1. What Is a Tool?

A tool is a controlled capability that an LLM can request the application to execute.

Examples:

```text
get_weather()
search_documents()
get_customer()
query_database()
calculate()
send_email()
```

A useful tool normally has:

1. Name
2. Description
3. Input schema
4. Implementation

## 2. Function vs Tool

An ordinary function might be:

```python
def calculate_tax(amount):
    ...
```

A tool additionally exposes metadata that allows the model to understand what the capability does and what arguments it accepts.

Conceptually:

```text
Function
+
Name
+
Description
+
Input Schema
=
Tool
```

## 3. Tool Schema

Example:

```python
class CustomerInput(BaseModel):
    customer_id: str
```

The tool can be described as:

```text
get_customer

Retrieve customer profile information using the customer's unique ID.
```

The model can then produce a structured request:

```json
{
  "tool": "get_customer",
  "arguments": {
    "customer_id": "123"
  }
}
```

## 4. Tool Calling Lifecycle

```text
User
 ↓
Prompt
 ↓
LLM
 ↓
Tool Call
 ↓
Application
 ↓
Validate Arguments
 ↓
Authorize
 ↓
Execute Tool
 ↓
Tool Result
 ↓
LLM
 ↓
Final Answer
```

The model requests the tool. The application executes it.

## 5. Why This Boundary Matters

Consider:

```python
delete_account(account_id)
```

The model should not have unrestricted ability to execute arbitrary application code.

Instead:

```text
LLM
 ↓
"delete_account(123)"
 ↓
Application validation
 ↓
Authorization
 ↓
Execution
```

This creates an important security boundary.

## 6. Tool Descriptions

Descriptions influence tool selection.

Poor:

```text
get_data()
```

Better:

```text
get_customer(customer_id)

Retrieve customer profile information using the customer's unique ID.
Use this when the user asks about an existing customer's account.
```

A useful description should communicate what the tool does, when it should be used, what its inputs mean and what it returns.

## 7. Input Schemas

Use structured schemas rather than ambiguous strings.

Example:

```python
class WeatherInput(BaseModel):
    city: str
    country: str
```

Instead of:

```text
weather("Kolkata, India")
```

the model can provide:

```json
{
  "city": "Kolkata",
  "country": "India"
}
```

Structured arguments are easier to validate.

## 8. Validation

Never trust tool arguments simply because they came from a model.

Validate:

```text
Required fields
Data types
Allowed values
Ranges
Identifiers
Permissions
```

Schema validation may establish that an amount is numeric. Business validation must establish whether the amount is actually allowed.

## 9. Read vs Write Tools

### Read Tool

```text
get_customer()
search_orders()
query_balance()
```

Usually lower risk.

### Write Tool

```text
create_order()
send_email()
delete_account()
transfer_money()
```

Higher risk.

Write tools may require confirmation, authorization, approval, idempotency and audit logging.

## 10. Idempotency

Suppose an agent calls:

```text
send_email()
```

A timeout occurs after the email provider accepts the request. A retry could send a duplicate email.

Important write operations should therefore use idempotency mechanisms where appropriate:

```text
Request ID
   ↓
Check previous execution
   ↓
Execute once
```

This is standard backend engineering applied to AI systems.

## 11. Tool Errors

Tools can fail because of:

- Database outage
- API timeout
- Invalid identifier
- Permission denial
- Rate limiting

The agent should receive a useful structured result when appropriate rather than an uncontrolled infrastructure exception.

Example:

```json
{
  "success": false,
  "error": "CUSTOMER_NOT_FOUND"
}
```

The model can then decide whether to ask the user for clarification, try another safe action or stop.

## 12. Tool Result

A tool result becomes part of the agent's context.

```text
Tool:
get_customer(123)

Result:
Customer = Rahul
Status = ACTIVE
```

The agent can then use the observation when deciding what to do next.

## 13. Tool Granularity

Too broad:

```text
manage_customer()
```

The model may struggle to understand the possible operations.

Too narrow:

```text
get_customer_name()
get_customer_city()
get_customer_email()
get_customer_status()
```

This creates unnecessary tool-selection complexity.

A useful tool normally represents a meaningful capability, such as:

```text
get_customer_profile()
```

## 14. Tool Composition

A complex task may require several tools:

```text
get_customer()
      ↓
get_orders()
      ↓
calculate_refund()
      ↓
create_refund()
```

An agent may orchestrate these calls.

But if the sequence is always identical, a deterministic backend workflow is usually easier to test and control.

## 15. Tools and Agents

Tools are capabilities. Agents are decision-makers.

```text
Agent
│
├── Search Tool
├── Database Tool
├── Calculator Tool
└── Email Tool
```

The agent determines which capability to request.

## 16. Tools and RAG

A retrieval operation can itself be exposed as a tool:

```text
search_company_policy(question)
```

An agent can call it when it needs internal company information.

```text
Agent
├── Company Policy Search
├── Customer Database
└── External Search
```

This is one way RAG becomes a capability inside an agentic system.

## 17. Tool Security

Treat tool security like API security.

For each tool ask:

```text
Who can call it?
What inputs are allowed?
What data can it access?
What side effects can it cause?
What should be logged?
Does it require approval?
```

Never rely on a prompt such as:

```text
"Only authorized users may use this tool."
```

Authorization must be enforced by the application.

## 18. Tool Execution Architecture

```text
                  ┌─────────────┐
                  │    User     │
                  └──────┬──────┘
                         ↓
                  ┌─────────────┐
                  │     LLM     │
                  └──────┬──────┘
                         ↓
                    Tool Call
                         ↓
                ┌─────────────────┐
                │ Application      │
                │ Validation       │
                │ Authorization    │
                └────────┬────────┘
                         ↓
                     Tool Code
                         ↓
                 External System
                         ↓
                    Tool Result
                         ↓
                        LLM
```

The application layer is the security and reliability boundary.

## 19. Backend Analogy

A tool is similar to a controlled API/service capability exposed to an orchestrator.

```text
REST API
 ↓
Controller
 ↓
Service
 ↓
Repository
```

Agentic system:

```text
LLM
 ↓
Tool Call
 ↓
Tool Adapter
 ↓
Service/API
```

The tool should expose a controlled capability into the existing backend rather than contain the entire business system.

## 20. Best Practices

- Give tools precise names.
- Write useful descriptions.
- Define explicit input schemas.
- Validate every argument.
- Enforce authorization outside the LLM.
- Separate read and write capabilities.
- Add timeouts.
- Handle failures explicitly.
- Make important write operations idempotent.
- Log tool calls and results.
- Keep tools independently testable.

## 21. Common Mistakes

### Vague tool descriptions

The model cannot reliably select a poorly described tool.

### Trusting model arguments

LLM-generated arguments are untrusted input.

### Exposing dangerous operations directly

Destructive operations need additional controls.

### Too many tools

Tool selection becomes harder as the tool set grows.

### Turning every function into a tool

Only expose capabilities the model genuinely needs.

## 22. Interview Questions

### Beginner

1. What is a tool?
2. How is a tool different from a function?
3. What is tool calling?
4. Why does a tool need a schema?

### Intermediate

5. How does an LLM select a tool?
6. Where is the tool actually executed?
7. Why are descriptions important?
8. What is the difference between a tool and a retriever?
9. What happens when a tool fails?

### Advanced

10. How would you secure write tools?
11. How would you handle duplicate tool execution?
12. How would you design an agent with many tools?
13. How would you audit tool usage?
14. How would you handle sensitive data returned by tools?

## 23. Checkpoint Summary

- A tool exposes a controlled capability to an LLM/agent.
- A tool has a name, description, input schema and implementation.
- The model requests a tool; the application executes it.
- Tool inputs must be validated.
- Authorization belongs to the application.
- Write tools require stronger controls than read tools.
- Tool design directly affects agent reliability.

## 24. Mind Map

```text
Tools
│
├── Definition
│   ├── Name
│   ├── Description
│   ├── Schema
│   └── Implementation
│
├── Lifecycle
│   ├── Model requests
│   ├── Validate
│   ├── Authorize
│   ├── Execute
│   └── Return result
│
├── Types
│   ├── Read
│   └── Write
│
├── Reliability
│   ├── Timeout
│   ├── Retry
│   ├── Idempotency
│   └── Error handling
│
└── Security
    ├── Authentication
    ├── Authorization
    ├── Validation
    └── Audit
```

## 25. 2-Minute Revision

```text
Tool = Controlled capability exposed to the model
```

Lifecycle:

```text
LLM
 ↓
Tool Call
 ↓
Validate
 ↓
Authorize
 ↓
Execute
 ↓
Result
 ↓
LLM
```

The key production rule:

> A tool call requested by an LLM is a request, not permission to execute an action.

For a backend engineer, think of tools as carefully controlled adapters that expose existing application capabilities to an AI orchestrator.
