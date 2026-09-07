🌐 LLM Gateway

A lightweight, zero-cost production-ready AI gateway engineered to load-balance and route prompts dynamically between multiple free-tier LLM providers using **LiteLLM**, **FastAPI**, and **LangChain**.

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
  "prompt": "Write an optimized Python function to check for palindromes.",
  "alias": "smart-coding"
}
```

**Response Telemetry Structure:**
```json
{
  "success": true,
  "model_deployed": "gemini/gemini-3.8-flash",
  "content": "def is_palindrome(s): ...",
  "metrics": {
    "input_tokens": 42,
    "output_tokens": 128,
    "transaction_cost_usd": 0.00000000
  }
}
```

---

## 🔒 Security & Safety
This system uses a zero-trust model configuration. Prompts requesting access to unauthorized domains, penetration tutorials, or server disruptions trigger an automated `GuardrailViolation` and are instantly terminated at the proxy level
