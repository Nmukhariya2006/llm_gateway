# 🌐 LLM Gateway

A lightweight, zero-cost production-ready AI gateway engineered to load-balance, cache, secure, and route prompts dynamically between multiple free-tier LLM providers using **LiteLLM**, **FastAPI**, and **LangChain**.

---

## ✨ Features

* 🔀 **Smart Load Balancing:** Alternates queries across free-tier endpoints using a `simple-shuffle` routing strategy.
* ⚡ **Ultra-Fast Local Caching:** Intercepts identical queries via an in-memory caching engine to deliver sub-millisecond responses at zero extra token cost.
* 🛡️ **Topic Guardrails:** Built-in pattern protection that proactively scans and drops malicious/unsafe prompts before they hit your API connections.
* 💰 **Financial Auditing Core:** Embedded token transaction telemetry tracking exact input usage, output sizes, and computed dollar metrics.

---

## 🛠️ Model Registry Pool

The gateway manages an active pool targeting top-tier open weight and flash models within their zero-cost operational thresholds:

| Router Alias | Underlying LLM | Provider | Use Case Optimization |
| :--- | :--- | :--- | :--- |
| `fast-cheap` | `gemini-3.1-flash-lite` | Google AI Studio | High RPM, low-latency lookups |
| `smart-coding` | `gemini-3.8-flash` | Google AI Studio | Code parsing, multi-step logic |
| `balanced` | `llama-3.3-70b-versatile` | Groq Platform | Factual reasoning, complex conversations |

---

## ⚡ Architectural Deep Dives

### 1. In-Memory Caching Engine
To prevent hitting strict free-tier rate limits and eliminate unnecessary pricing transactions, the gateway integrates a localized `RAM Cache` architecture. 
* **Cache Hit Strategy:** When an incoming query is parsed, its cryptographic signature (Model + Prompt Payload) is checked against local memory cache storage before hitting external servers.
* **Cost Efficiency:** A cache hit skips network transit entirely, costing **\$0.00** in tokens and protecting your provider rate quotas from getting exhausted.

### 2. Sub-Millisecond Latency Matrix
By serving repeated requests straight out of local RAM rather than triggering an external web request to Google or Groq over the internet, response delivery speeds drop down to an instant **`0.0000s`** runtime footprint.

```text
 [Client Request] ──> [LLM Gateway] ──> (Cache Hit?) ── YES ──> [Instant local RAM Response] (<0.001s)
                           │
                          NO
                           └──> (Live Cloud API Call) ────────> [External Server Transit]  (1.5s - 3.0s)
```

### 3. Proactive Security Guardrails
The gateway implements a rigorous intercept strategy via LiteLLM's `input_callback` hooks to enforce compliance parameters:
* **Pre-Execution Scanning:** User queries are thoroughly analyzed and scrubbed *before* a network payload is generated or sent to cloud networks.
* **Malicious Intent Interception:** Unsafe terminology or harmful strings (e.g., `"hack"`, `"exploit"`) trigger an immediate `GuardrailViolation` exception at the root level.
* **Instant Rejection:** The application blocks execution instantly, keeping your provider history completely clean and protecting backend data loops.

---

## 🚀 Quick Start Guide

### 1. Installation
Clone the repository and install the frozen dependencies inside your virtual environment:

```bash
git clone https://github.com
cd llm_gateway
pip install -r requirements.txt
```

### 2. Environment Setup
Create a `.env` file in the root directory and securely load your platform access keys:

```text
# Google AI Studio Credentials
GOOGLE_API_KEY=AIzaSyYourKeyHere

# Groq Platform Credentials
GROQ_API_KEY=gsk_YourKeyHere
```

### 3. Initialize the Gateway Server
To launch the backend API server over standard local proxies, run:

```bash
uvicorn main:app --reload
```

---

## 📡 API Endpoint Architecture

### `POST /v1/chat`
Routes a single execution prompt to your model pool alias.

**Request Payload Structure:**
```json
{
  "prompt": "Explain RAG in one sentence.",
  "alias": "fast-cheap"
}
```

**Response Telemetry Structure (Live Call vs. Cached Call):**
```json
{
  "success": true,
  "model_deployed": "gemini/gemini-3.1-flash-lite",
  "content": "Retrieval-Augmented Generation blends external search documents with an LLM...",
  "metrics": {
    "input_tokens": 12,
    "output_tokens": 24,
    "latency_sec": 0.0, 
    "transaction_cost_usd": 0.00000000,
    "cache_status": "HIT"
  }
}
```
