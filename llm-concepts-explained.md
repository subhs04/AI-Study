# LLM & AI Engineering Concepts — Explained Simply

---

## 1. LLMs / Foundation Models

An LLM (Large Language Model) is a neural network trained on massive amounts of text to predict "what word comes next" given the words before it. Do this billions of times across the internet's worth of text, and the model ends up learning grammar, facts, reasoning patterns, code syntax, and style — all as a side effect of getting really good at next-word prediction.

"Foundation model" is the broader term: a large model trained on broad data that can be *adapted* to many downstream tasks (chat, coding, summarization, image generation) rather than being built for one narrow job. GPT, Claude, Gemini, Llama — all foundation models, and the "LLM" ones specialize in text.

Think of it like a very well-read colleague who's read almost everything ever written but has no memory of your specific conversation until you tell them.

**One-liner:** An LLM is a next-word-prediction engine trained on huge amounts of text, general enough to be reused across many tasks.

---

## 2. Tokens

Models don't read words — they read **tokens**, which are chunks of text (sometimes a whole word, sometimes a piece of one, like "un", "believ", "able" for "unbelievable"). Text gets split into tokens before it hits the model, and the model's output also comes out token-by-token.

Why it matters practically: pricing, context limits, and speed are all measured in tokens, not words or characters. Roughly 1 token ≈ ¾ of an English word.

**One-liner:** Tokens are the model's "words" — text gets chopped into these units for both input and output.

---

## 3. Context Window

The context window is the total amount of text (measured in tokens) the model can "see" at once — your prompt, any documents you pasted, the conversation history, and its own response, all combined.

If the context window is 200K tokens and your conversation + documents exceed that, the oldest stuff gets dropped or truncated. It's like short-term memory with a hard cap — nothing "sticks" beyond it unless it's re-fed in.

**One-liner:** The context window is the model's working memory limit — everything it can consider at once, measured in tokens.

---

## 4. Temperature

Temperature controls how "random" or "safe" the model's word choices are when generating text. At temperature 0, the model almost always picks the single most likely next token — deterministic, repeatable, conservative. At higher temperature (say 0.8–1.0), it's more willing to pick less-likely tokens — more creative, more varied, but also more prone to going off the rails.

Use low temperature for factual/code tasks where you want consistency; higher temperature for brainstorming, creative writing, varied outputs.

**One-liner:** Temperature is the randomness dial — low = predictable and safe, high = creative and varied.

---

## 5. Embeddings

An embedding is a way of converting text (a word, sentence, or document) into a list of numbers (a vector) such that texts with similar *meaning* end up with similar numbers — mathematically "close" to each other in that number-space.

This is powerful because it lets you compare meaning, not just exact words. "The car broke down" and "The vehicle stopped working" will have embeddings that are close together even though they share almost no words.

**One-liner:** Embeddings turn text into numbers that capture meaning, so similar meanings end up mathematically close together.

---

## 6. Vector Databases

A vector database stores embeddings (those number-lists) and is optimized to answer one question fast: "given this new vector, which stored vectors are most similar to it?"

This is the backbone of semantic search — instead of matching keywords, you embed a query, search the vector DB for the closest stored embeddings, and retrieve the original text those embeddings came from. Examples: Pinecone, Weaviate, Chroma, pgvector.

**One-liner:** A vector database is a search engine for meaning — it finds the closest-matching embeddings, not the closest-matching keywords.

---

## 7. Inference

Inference is simply the act of *running* a trained model to get an output — as opposed to *training* it, which is the (much more expensive, one-time-ish) process of teaching it in the first place.

Every time you send Claude a prompt and get a response back, that's one inference call. "Inference cost" and "inference latency" are what you pay/wait for at request time, distinct from the sunk cost of training the model originally.

**One-liner:** Inference is using an already-trained model to produce an output — the "running" phase, not the "learning" phase.

---

## 8. Prompting

Prompting is how you instruct the model — the input text you craft to get the output you want. Since the model has no fixed "mode," the prompt is your entire interface: role instructions, examples, constraints, formatting requests, all live in the prompt.

"Prompt engineering" is the practice of structuring that input deliberately — giving clear instructions, examples (few-shot), step-by-step reasoning cues, or explicit output format requirements — to reliably get better results, since a vague prompt gets a vague or inconsistent answer.

**One-liner:** Prompting is how you tell the model what you want — the better structured the ask, the better and more reliable the answer.

---

## 9. Structured Output

By default, an LLM produces free-flowing prose. Structured output means constraining the model to return data in a specific machine-readable format — most commonly JSON matching a schema you define (e.g., `{"name": string, "amount": number, "date": string}`).

This matters for anything that needs to be *parsed by code* downstream — your Java service can't reliably regex-parse prose, but it can trivially parse guaranteed-valid JSON. Modern APIs support this natively (you pass a schema, the model is constrained to emit only valid instances of it).

**One-liner:** Structured output forces the model's answer into a fixed, parseable format (like JSON) instead of free-text prose.

---

## 10. Function / Tool Calling

Tool calling lets the model do more than talk — it can request that *your code* run a specific function (a "tool") with specific arguments, then use the result to continue answering. The model itself never executes code; it just says "call `get_weather(city="Kolkata")`" in a structured way, your system runs that function, and feeds the result back to the model.

This is how LLMs get access to live data, calculators, databases, or external APIs — anything outside their training data or reasoning ability alone. It's the mechanism underneath most "agentic" behavior and MCP servers you've been exploring.

**One-liner:** Tool calling lets the model ask your code to run a specific function and hand back the result, extending it beyond pure text generation.

---

## 11. Hallucination

Hallucination is when the model confidently states something false — a fabricated fact, a made-up citation, a nonexistent API method — because it's still just predicting "plausible next text," not verifying truth against a database. Nothing in its core mechanism guarantees accuracy; fluency and correctness are separate things that just usually correlate.

It's more likely when: the topic is obscure/recent (outside training data), the question presumes something false, or there's no grounding data (RAG/tools) to check against.

**One-liner:** Hallucination is the model generating fluent, confident text that is factually wrong — because it predicts plausible language, not verified truth.

---

## 12. RAG (Retrieval-Augmented Generation)

RAG is an architecture pattern to reduce hallucination and add up-to-date/private knowledge: before the model answers, you *retrieve* relevant documents (usually via embeddings + a vector database) and *inject* them into the prompt as context, so the model answers grounded in that retrieved text rather than purely from memory.

Flow: user question → embed the question → search vector DB for similar chunks → stuff those chunks into the prompt → model answers using that context. It's how you get an LLM to accurately answer questions about your company's internal docs, which it was never trained on.

**One-liner:** RAG fetches relevant real documents and feeds them to the model as context before it answers, grounding the response in actual data.

---

## 13. Fine-Tuning

Fine-tuning takes an already-trained foundation model and continues training it on a smaller, specialized dataset to shift its behavior — its tone, its domain vocabulary, its output format — without training a model from scratch (which costs millions of dollars).

Contrast with RAG: RAG gives the model *new information* at query time without changing the model's weights; fine-tuning changes the model's *weights themselves* to change its behavior/style permanently. Often used together — fine-tune for style/format, RAG for facts.

**One-liner:** Fine-tuning further trains an existing model on specialized data to change its behavior or style permanently, rather than just feeding it context.

---

## 14. SFT vs Reinforcement Fine-Tuning

Both are stages typically applied *after* the initial giant "predict next word" pretraining, to make a raw model actually useful and safe as an assistant:

- **SFT (Supervised Fine-Tuning):** You show the model curated example pairs — "here's a prompt, here's the ideal response a human wrote" — and train it to mimic that style of answer. Straightforward imitation learning.
- **Reinforcement Fine-Tuning (RLHF / RL-based):** Instead of mimicking one fixed "correct" answer, the model generates multiple candidate responses, a reward signal (often from a human-preference-trained reward model, or increasingly, verifiable task success) scores which ones are better, and the model is nudged via reinforcement learning to produce more of the higher-scoring style over time.

SFT teaches "imitate this good example." RL fine-tuning teaches "here's a scoring signal — improve toward it," which lets the model get better than any single human demonstration since it's optimizing against a reward rather than copying a fixed answer.

**One-liner:** SFT teaches the model to imitate curated example answers; RL fine-tuning teaches it to optimize toward a reward/preference signal, refining beyond any single example.

---

## 15. Agents

An "agent" is an LLM wired into a loop where it can plan, call tools, observe results, and decide the next step — repeatedly — rather than just answering once. Instead of "prompt in, text out," it's "goal in → model reasons about what to do → calls a tool → gets a result → reasons again → calls another tool → ... → final answer/action out."

This is what turns an LLM from "a really smart autocomplete you chat with" into "something that can actually go do a multi-step task" — search the web, read a file, write code, run it, check the output, fix a bug, and report back — largely autonomously within guardrails you set.

**One-liner:** An agent is an LLM in a loop — reasoning, calling tools, observing results, and deciding the next step — to complete multi-step tasks rather than answer once.

---

## Quick-Recall Cheat Sheet (all one-liners together)

| Concept | One-liner |
|---|---|
| **LLM / Foundation Model** | A next-word-prediction engine trained on huge text, general enough for many tasks. |
| **Tokens** | The model's "words" — text chunks used for both input and output. |
| **Context Window** | The model's working-memory limit, measured in tokens. |
| **Temperature** | The randomness dial — low = predictable, high = creative. |
| **Embeddings** | Numbers that capture meaning — similar meaning = close numbers. |
| **Vector Database** | A search engine for meaning, not keywords. |
| **Inference** | Running an already-trained model to get an output. |
| **Prompting** | How you instruct the model — better structure, better answer. |
| **Structured Output** | Forcing the answer into a fixed, parseable format like JSON. |
| **Function/Tool Calling** | Model asks your code to run a function and hand back the result. |
| **Hallucination** | Confident, fluent, but factually wrong output. |
| **RAG** | Fetch real documents, feed as context, ground the answer in fact. |
| **Fine-Tuning** | Further training a model on specialized data to permanently shift behavior. |
| **SFT vs RL Fine-Tuning** | SFT = imitate curated examples; RL = optimize toward a reward signal. |
| **Agents** | An LLM in a loop — reason, act, observe, repeat — for multi-step tasks. |
