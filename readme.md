# Scarpa AI Agent 👋
**RAG | Agent Workflows | AI Concepts**

I wanted to get hands-on with AI agent workflows — specifically how Retrieval-Augmented Generation (RAG), prompt classification, and guardrails actually behave in a real pipeline.

So I built one.

This project uses **ChatGPT's Agent Builder** to design a working agent capable of answering natural language product queries across Scarpa's full lineup — routing each question through safety checks, a classifier, and category-specific vector databases before returning a validated answer.

---

## 🧭 The Problem

Most AI demos skip the architecture.

They show you the input and the output — but not the decisions happening in between:

- How does the model know *which* data to retrieve?
- What stops it from hallucinating an answer?
- What happens when a prompt is malicious, or just a bad fit?

This project was built to explore those questions through something concrete.

---

## 🧪 The Setup

Scarpa's product catalogs were converted into **JSON files** and uploaded into **six separate vector databases** — one per product category.

> Using category-specific databases (rather than one large store) reduces the volume of information the model must parse. Narrowing the retrieval window before generation improves answer accuracy.

### 📦 Product Categories

| Category | Example Queries |
|----------|----------------|
| 🏃 Trail Running | "What's the drop on the Golden Gate 2?" |
| 🧗 Approach | "Which shoe is best for mixed terrain?" |
| 🪨 Climbing | "Is the Instinct S2 aggressive?" |
| ⛰️ Mountaineering | "How does the Ribelle compare to the Phantom?" |
| ⛷️ Skiing | "What's the flex index on the Maestrale RS?" |
| 👟 Lifestyle | "What's a versatile everyday Scarpa option?" |

---

## ⚙️ How It Works

```
User Prompt
    │
    ▼
[Guardrail] ── Fail ──► Blocked
    │ Pass
    ▼
[Classifier] ── Routes to one of six product categories
    │
    ▼
[Vector DB Search] ── Queries the matching category database
    │
    ▼
[Guardrail] ── Fail ──► Response withheld
    │ Pass
    ▼
Validated Answer → User
```

### 🔍 Step-by-Step

| Step | Component | What It Does |
|------|-----------|--------------|
| 1 | **Prompt entry** | User submits a natural language question |
| 2 | **Input guardrail** | Screens for PII, jailbreak attempts, and harmful SQL injections |
| 3 | **Classification** | Routes the prompt into one of six product categories |
| 4 | **Vector DB search** | Queries the category-specific database for relevant product data |
| 5 | **Output guardrail** | Checks the generated answer for faithfulness to retrieved data |
| 6 | **Delivery** | Validated response is returned to the user |

---

## 🔑 Key Concepts Explored

**Retrieval-Augmented Generation (RAG)**
Grounding LLM responses in retrieved external data rather than model memory — so the agent answers from the catalog, not from what it thinks it knows about Scarpa.

**Prompt classification**
Routing queries to the most relevant data source *before* retrieval, rather than searching everything at once.

**Input & output guardrails**
Safety checks at both ends of the pipeline — one to screen incoming prompts, one to verify the answer didn't drift from the retrieved data.

**Vector database sharding**
Partitioning data by domain so retrieval is precise by design, not by luck.

---

## 🏗️ Tools & Stack

| Tool | Purpose |
|------|---------|
| ChatGPT Agent Builder | Agent design and workflow orchestration |
| Vector Databases (×6) | Category-specific semantic search |
| JSON | Structured product catalog format |

---

## 📁 Repository Structure

```text
scarpa-ai-agent/
├── README.md
├── data/
│   └── catalogs/          # JSON product catalogs by category
├── agent/
│   └── workflow.md        # Agent configuration and logic notes
├── findings/
│   └── notes.md           # Observations on agent behavior
└── assets/
    └── architecture.png   # Workflow diagram
```

---

## 🚧 Status

This is a learning and portfolio project — built to get hands-on with AI agent design patterns.

The agent is not deployed publicly. Notes on agent behavior and retrieval quality will continue to evolve as more queries are tested.

---

> **This repo is less about building a product, and more about understanding how agents actually make decisions.**
