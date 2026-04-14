# agent.md — Enterprise GenAI Copilot (Local → AWS)

## 🎯 Objective

Build a **production-grade, agentic GenAI system** for healthcare device troubleshooting that demonstrates:

* Advanced RAG (not basic chatbot)
* Deterministic agent workflows
* Observability + evaluation
* Clean architecture with **local → AWS portability**

---

# 🧠 Core Principles (Non-Negotiable)

1. **System > Model**

   * Focus on architecture, not just LLM calls

2. **Deterministic Workflows**

   * Use graph-based orchestration (no black-box agents)

3. **RAG First, Not Fine-tuning**

   * Retrieval quality is primary lever

4. **Measure Everything**

   * No feature without logging/evaluation

5. **Portable by Design**

   * Every component must support local → cloud swap

---

# 🏗️ Architecture Overview

## Layers

* API Layer (FastAPI)
* Orchestrator (LangGraph workflow)
* Retrieval System (hybrid + rerank)
* LLM Layer (provider abstraction)
* Tool Layer
* Memory Layer
* Guardrails Layer
* Observability + Evaluation

---

# 🔌 Provider Abstraction (MANDATORY)

## Interfaces

### LLMProvider

```python
class LLMProvider:
    def generate(self, prompt: str) -> str: ...
```

### VectorStore

```python
class VectorStore:
    def add(docs): ...
    def search(query): ...
```

### StorageProvider

```python
class StorageProvider:
    def save(file): ...
    def load(id): ...
```

## Mapping

| Local  | Cloud      |
| ------ | ---------- |
| Ollama | Bedrock    |
| Chroma | OpenSearch |
| FS     | S3         |

❗ Never hardcode vendor logic in business layer

---

# 🧠 Agent Workflow Design

Use **graph-based orchestration**:

Nodes:

* Planner
* Retriever
* Reasoner
* Tool Executor
* Critic
* Human Approval (optional)

## Rules

* Each node must be **pure + testable**
* No hidden state
* Explicit inputs/outputs

---

# 📚 Retrieval System (Advanced RAG)

## Requirements

### 1. Chunking

* Section-aware (error codes, procedures)
* 300–800 tokens
* Overlap: 10–15%

### 2. Hybrid Retrieval

* Vector search + BM25
* Weighted merge

### 3. Re-ranking

* Top-20 → Top-5

### 4. Query Rewriting

* Normalize technical queries

---

# 🧾 Prompt Design

## Must Include

* Role definition
* Structured output format
* Instruction to avoid hallucination
* “If not found, say NOT FOUND”

## Output Format

```
Steps:
Warnings:
Tools:
Sources:
Confidence:
```

---

# 🧰 Tooling System

## Tools (initial)

* Error code lookup
* Troubleshooting generator
* Ticket creator (mock)

## Rules

* Tools must be:

  * deterministic
  * idempotent
* Validate inputs before execution

---

# 🧠 Memory Design

| Type      | Purpose         |
| --------- | --------------- |
| Session   | conversation    |
| Knowledge | vector DB       |
| Task      | agent decisions |

---

# 🛡️ Guardrails (CRITICAL)

## Input Guardrails

* Detect prompt injection:

  * ignore instructions like “ignore previous”
* Validate domain:

  * reject medical diagnosis queries
* Sanitize input

## Output Guardrails

* Must be grounded in retrieved context
* If confidence low:

  * respond “NOT FOUND IN MANUAL”
* No hallucinated steps

## Domain Restriction

* Allowed:

  * device troubleshooting
* Not allowed:

  * medical advice
  * diagnosis

---

# 🧪 Evaluation (MANDATORY)

Use:

* RAGAS or custom

## Metrics

* Faithfulness
* Answer relevance
* Context precision

## Dataset

* Minimum 50 queries
* Include edge cases

---

# 📊 Observability

## Logs (JSON)

* request_id
* query
* rewritten_query
* retrieved_docs
* prompt
* response
* latency

## Metrics

* latency
* token usage
* retrieval accuracy
* tool success rate

---

# ⚙️ Performance Guidelines

* Cache embeddings
* Cache frequent queries
* Limit top-k (5–10)
* Avoid unnecessary LLM calls

---

# 🚫 Anti-Patterns (Avoid)

* ❌ Direct LLM calls without retrieval
* ❌ Hardcoding prompts everywhere
* ❌ Mixing business logic with provider logic
* ❌ No evaluation
* ❌ UI-first development

---

# 🧩 Code Quality Standards

## Structure

* Modular folders
* Clear separation of concerns

## Functions

* Small, testable
* No side effects

## Typing

* Use type hints everywhere

## Logging

* Every request must be traceable

---

# ☁️ Cloud Readiness

## Design Rules

* No local-only assumptions
* Use environment configs
* Abstract providers

## AWS Targets

* Bedrock (LLM)
* OpenSearch (vector)
* S3 (storage)
* Lambda/ECS (API)

---

# 🧠 Senior-Level Expectations

The system must demonstrate:

* Tradeoff awareness (latency vs accuracy)
* Failure handling (fallbacks)
* Measurability (evaluation)
* Security awareness (guardrails)
* Scalability thinking

---

# 📦 Deliverables

* Clean GitHub repo
* Architecture diagram
* Demo video
* README with:

  * design decisions
  * tradeoffs
  * limitations

---

# ⚠️ Limitations (Be Explicit)

* No real-time device integration
* Limited dataset (<1GB)
* Local model accuracy constraints
* No clinical validation

---

# 🏁 Definition of Done

* Query → correct retrieval → grounded answer
* Agent workflow executes deterministically
* Logs + evaluation available
* System runs locally end-to-end
* AWS mapping documented

---

# 🔥 Final Guideline

Build like:

> “This will be deployed in production tomorrow”

Not:

> “This is a demo project”

---
