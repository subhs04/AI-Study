# Chapter 4 – Output Parsers and Structured Output

## 1. Learning Objectives

You should be able to:

- Explain why LLM output needs parsing.
- Distinguish text output from structured output.
- Understand `StrOutputParser`.
- Understand JSON/schema-based output.
- Use Pydantic as a response contract.
- Design validation and error handling for production.

## 2. Why Output Parsing Exists

LLMs naturally generate text:

```text
The customer wants to cancel the subscription and the request is urgent.
```

A backend application often needs predictable data:

```json
{
  "intent": "CANCEL_SUBSCRIPTION",
  "urgency": "HIGH"
}
```

So the pipeline becomes:

```text
Prompt
  ↓
LLM
  ↓
Parser / Structured Output
  ↓
Validated Application Data
  ↓
Business Logic
```

The important idea is that the LLM should not be allowed to directly control deterministic business logic.
---

## 3. `StrOutputParser`

For simple text pipelines:

```python
from langchain_core.output_parsers import StrOutputParser

parser = StrOutputParser()

chain = prompt | model | parser
result = chain.invoke({"topic": "vector databases"})
```

Conceptually:

```text
AIMessage
   ↓
StrOutputParser
   ↓
str
```

For a trivial application, `response.content` can also be used. The parser becomes particularly useful when composing LCEL pipelines because every stage has an explicit input/output contract.

---

## 4. Structured Output

Suppose a support system needs:

```json
{
  "category": "BILLING",
  "priority": "HIGH",
  "summary": "Customer was charged twice."
}
```

The application should consume fields, not interpret prose.

A useful architecture is:

```text
User Request
    ↓
Prompt Template
    ↓
Chat Model
    ↓
Structured Output
    ↓
Validation
    ↓
Business Logic
```

---

## 5. Pydantic

Pydantic lets Python code define the expected shape.

```python
from pydantic import BaseModel

class SupportTicket(BaseModel):
    category: str
    priority: str
    summary: str
```

This is conceptually similar to a Java DTO:

```java
class SupportTicket {
    String category;
    String priority;
    String summary;
}
```

The key difference is that the LLM is generating the data.

---

## 6. Constrained Values

Free-form strings are weaker than constrained values.

For example:

```python
from enum import Enum

class Priority(str, Enum):
    LOW = "LOW"
    MEDIUM = "MEDIUM"
    HIGH = "HIGH"
```

Then:

```python
class SupportTicket(BaseModel):
    category: str
    priority: Priority
    summary: str
```

Now the application can reject unsupported priority values instead of silently accepting arbitrary text.

---

## 7. Prompting for JSON vs Structured Output

A beginner approach is:

```text
Return ONLY valid JSON.
Do not include markdown.
Do not explain your answer.
```

This may work, but it is still relying heavily on instruction following.

Structured-output mechanisms provide a stronger contract:

```text
Schema
  ↓
Model
  ↓
Structured result
```

Use model/framework-supported structured output when the application depends on reliable fields.

---

## 8. Parsing vs Structured Output

These are related but not identical.

### Output Parser

Transforms or validates model output:

```text
Model
 ↓
Parser
 ↓
Application Object
```

### Structured Output

Defines the desired response structure as part of the model interaction:

```text
Schema
 ↓
Model
 ↓
Structured Result
```

### Structured Output Vs Parsers
⚬	Structured Output (LLM-Level):  Feature of LLM that instead of just spitting out free-form text, the LLM model is forced or guided at the inference level (via JSON mode, constrained decoding, or tool/function calling) to generate **text(still produces text in json)** that conforms to a strict schema. The LLM itself generates the output in the requested structured format.

⚬	Output Parsers (LangChain-Level): These are **client-side post-processing** utilities provided by LangChain (BaseOutputParser). Historically, LLMs only returned plain strings. LangChain output parsers were used to **parse those strings** (often using regex or string manipulation) **into dictionaries or objects**.

⚬	The Modern Convergence: In modern LangChain, these two concepts merge. When you use LangChain’s .with_structured_output(), LangChain leverages the LLM's native structured output capability under the hood and automatically applies an output parser to return a clean Python object.

---

## 9. Example: Support Classification

Input:

```text
"I was charged twice for my monthly subscription."
```

Desired result:

```json
{
  "category": "BILLING",
  "priority": "HIGH"
}
```

Application logic:

```python
if ticket.category == "BILLING":
    create_billing_case(ticket)
```

Notice the boundary:

```text
LLM
 ↓
Structured Contract
 ↓
Business Logic
```

The business layer does not need to understand natural language.

---

## 10. Failure Modes

### Missing fields

Expected:

```json
{
  "category": "BILLING",
  "priority": "HIGH"
}
```

Received:

```json
{
  "category": "BILLING"
}
```

Possible responses:

- Reject.
- Retry.
- Repair the output.
- Apply a safe default where appropriate.

### Wrong data type

Expected:

```json
{"age": 34}
```

Received:

```json
{"age": "thirty four"}
```

Schema validation should catch this.

### Invalid JSON

A model might surround JSON with explanatory prose or markdown. Parsing can fail even though the intended data is obvious to a human.

---

## 11. Syntactic vs Semantic Validation

These are different.

### Syntactic validation

Does the response match the schema?

```text
priority = HIGH
```

### Semantic/business validation

Is the value acceptable for the business?

For example, the model may correctly classify a refund request, but only application policy should determine:

- Whether refunds are allowed.
- Maximum refund amount.
- Whether approval is required.
- Whether the customer is eligible.

Therefore:

```text
LLM classification
      ↓
Schema validation
      ↓
Business validation
      ↓
Action
```

---

## 12. Representation



```text
[Your Application Code]
       │
       ├── 1. Define Schema (Pydantic Model) 
       │         │
       │         ▼
       ├── 2. Bind to Model via LangChain (`.with_structured_output()`)
       │         │
       │         ▼
       └── 3. Invoke LLM (Provider enforces JSON / Tool Call format)
                 │
                 ▼ (Returns raw structured JSON text)
           [Output Parser Layer]
                 │
                 ▼ (Deserializes & validates data)
       [Strongly Typed Pydantic Object]

```

```
OUTPUT PARSER (your commented-out line)
─────────────────────────────────────────────────────────
Prompt + "Respond in JSON like {...}"  ──► LLM ──► free text
                                                     │
        "Sure! Here's the JSON: ```json {...}```"    ▼
                                            PydanticOutputParser
                                            (regex/strip → json → validate)
                                                     │
                                          ✅ object  or  ❌ OutputParserException

STRUCTURED OUTPUT (what your code actually uses)
─────────────────────────────────────────────────────────
Prompt + schema sent as a *tool definition* ──► LLM (forced to call it)
                                                     │
                                       tool call args = JSON for schema
                                                     ▼
                                            Pydantic validation
                                                     │
                                                 ✅ IssueClassifier
```
---

## 13. Best Practices

- Define explicit schemas for important outputs.
- Validate before business logic.
- Prefer constrained values where possible.
- Handle malformed responses explicitly.
- Log failures for debugging.
- Treat LLM output as untrusted input.
- Keep business rules outside prompts.
- Use structured output for classification, routing and tool parameters.

---

## 14. Common Mistakes

### Mistake 1 – String matching

Bad:

```python
if "HIGH" in response:
    ...
```

The model may express the same meaning differently.

### Mistake 2 – Trusting valid JSON

Valid JSON does not guarantee valid business data.

### Mistake 3 – Putting business authorization in the prompt

Avoid:

```text
If the customer is angry, refund them.
```

Prefer:

```text
LLM → identify/classify request
Application → apply refund policy
```

---

## 15. Interview Questions

### Beginner

1. What is an output parser?
2. Why can't an application always consume raw LLM output?
3. What does `StrOutputParser` do?

### Intermediate

4. Why is structured output useful?
5. What is Pydantic?
6. How would you validate an LLM response?
7. What happens when required fields are missing?

### Advanced

8. Why is schema validation important in an agentic system?
9. How would you design retries for malformed output?
10. Where should business validation happen?
11. What is the difference between syntactic and semantic validation?
12. Why is structured output preferable to simply asking for JSON?

---

## 16. Checkpoint Summary

- LLMs naturally produce probabilistic text.
- Applications often need deterministic structures.
- Output parsers transform model responses.
- `StrOutputParser` produces plain text.
- Pydantic defines and validates schemas.
- Structured output creates a stronger application contract.
- Valid JSON is not automatically valid business data.
- Business rules must remain outside the LLM.

---

## 17. Mind Map

```text
Output Handling
│
├── Plain Text
│   └── StrOutputParser
│
├── Structured Output
│   ├── JSON
│   ├── Schema
│   └── Pydantic
│
├── Validation
│   ├── Required fields
│   ├── Data types
│   ├── Enumerations
│   └── Business rules
│
└── Production
    ├── Retry
    ├── Logging
    ├── Error handling
    └── Business validation
```

---

## 18. 2-Minute Revision

Remember this pipeline:

```text
LLM
 ↓
Parse
 ↓
Validate
 ↓
Business Logic
```

And this distinction:

```text
JSON                = data format
Schema              = expected structure
Validation          = does data satisfy the structure?
Business validation = is the data acceptable for the business?
```

### Core principle

> Never allow probabilistic LLM output to directly control deterministic business logic.
