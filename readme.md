# Scarpa RAG Agent 👋
**RAG | Agent Workflows | AI Concepts**

I wanted to get hands-on with AI agent workflows — specifically how Retrieval-Augmented Generation (RAG), prompt classification, and guardrails actually behave in a pipeline.

So I designed one.

This project uses **ChatGPT's Agent Builder** to mock up a workflow capable of answering natural language product queries across Scarpa's lineup — routing each question through safety checks, a classifier, and category-specific vector databases before returning a validated answer.

> **Heads up:** this was built as a learning exercise, not a production agent. The workflow was designed and tested in Agent Builder but never fully wired up end-to-end. The real output of this project lives in [`findings/`](./findings/) — what I learned about how these systems actually think.

---

## 🧭 The Problem

Most AI demos skip the architecture.

They show you the input and the output — but not the decisions happening in between:

- How does the model know *which* data to retrieve?
- What stops it from hallucinating an answer?
- What happens when a prompt is malicious, or just a bad fit?
- And the one I didn't expect to care about as much as I did: *what happens when the model sees too much information at once?*

This project was built to explore those questions through something concrete.

---

## 🧪 The Setup

Scarpa's product catalogs were converted into **Markdown  files** and uploaded into **six separate vector databases** — one per product category.

> Using category-specific databases (rather than one large store) reduces the volume of information the model has to parse at retrieval time. Narrowing the retrieval window before generation is one of the more practical levers for improving answer quality — and understanding *why* that's true is half of what this project ended up being about.

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

## ⚙️ What I Designed

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

Each of these stages exists for a reason — and working through *why* each one is there is where the real learning happened.

---

## 🔑 What I Actually Learned

The architecture diagram above was the starting point. The interesting stuff showed up once I started asking *why* it has to look like that.

**Retrieval-Augmented Generation (RAG)**
Grounding LLM responses in retrieved external data rather than the model's parametric memory. The agent answers from the catalog — not from what the model thinks it knows about Scarpa. Once you internalize that distinction, a lot of LLM behavior starts making more sense.

**Context pollution**
This was the one that surprised me. More retrieved context isn't automatically better — irrelevant or loosely-related chunks can actively *degrade* answer quality by competing with the right information. Sharding by category isn't just an optimization; it's a way of protecting the model from itself.

**Probabilistic retrieval and generation**
LLMs don't "look up" answers, and vector search doesn't "find" them either. Both stages are doing similarity-based probability work under the hood. Treating retrieval as deterministic is one of the easier ways to build something that quietly fails.

**Prompt classification as a routing layer**
Sending the query to the *right* index before retrieval — not searching everything at once — is what makes sharding pay off in the first place.

**Input & output guardrails**
Safety checks at both ends of the pipeline. One screens what comes in; the other verifies the answer didn't drift from the retrieved data. Each guards against a different failure mode.

**Vector database sharding**
Partitioning data by domain so retrieval is precise by design, not by luck.

📓 Detailed observations and what I'd do differently: [`findings/notes.md`](./findings/notes.md)

---

## 🏗️ Tools & Stack

| Tool | Purpose |
|------|---------|
| ChatGPT Agent Builder | Agent design and workflow orchestration |
| Vector Databases (×6) | Category-specific semantic search |
| Markdown  | Structured product catalog format |

---

## 📁 Repository Structure

```text
scarpa-rag-agent/
├── README.md
├── data/
│   └── catalogs/          # Markdown  product catalogs by category
├── agent/
│   └── workflow.md        # Agent configuration and design notes
├── findings/
│   └── notes.md           # What I learned — context pollution, probabilistic retrieval, etc.
└── assets/
    └── architecture.png   # Workflow diagram
```

---

## 🚧 Status

This is a **learning and portfolio project** — built to get hands-on with AI agent design patterns, not to ship a product.

The workflow was designed and partially tested in Agent Builder but was never fully productionized end-to-end. The repo is intentionally framed around the *thinking* behind the architecture — the design choices, the failure modes, and the conceptual takeaways — rather than around a working demo.

If you're here looking for clean code to fork, this isn't that. If you're here to see how someone reasons through a RAG pipeline from first principles, [`findings/notes.md`](./findings/notes.md) is probably where you want to start.

---

> **This repo is less about building a product, and more about understanding how agents actually make decisions.**
