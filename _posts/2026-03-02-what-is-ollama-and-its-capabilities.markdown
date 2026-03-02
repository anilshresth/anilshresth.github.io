---
layout: post
title: My experience with ollama
date: 2026-03-02 10:00:00 +0000
description: A deep dive into Ollama — the tool that brings powerful open-source AI models to your local machine, covering integrations, web search, thinking, and streaming capabilities.
img: ollama-post.jpg
fig-caption: Local AI, no cloud required
tags: [AI, Ollama, LLM, LocalAI, OpenSource]
---

If you have been following the artificial intelligence space, you have probably heard the growing buzz around **running AI models locally**. No cloud subscriptions, no data leaving your machine, no rate limits — just raw inference power on your own hardware. At the center of this movement is **Ollama**, a tool that has quietly become the go-to platform for self-hosted large language models (LLMs).

## What Is Ollama?

Ollama is an open-source tool that lets you download, run, and manage large language models directly on your local machine — whether that is macOS, Linux, or Windows. Think of it as a package manager for AI models, similar to how `apt` or `brew` handles software packages, but instead of installing apps, you pull entire neural networks.

With a single command like:

```bash
ollama run llama3.2
```

You can be chatting with a capable language model within minutes — no API key, no internet connection required after the initial download.

Ollama wraps the underlying inference engine (powered by `llama.cpp`) with a clean command-line interface and a local REST API, making it easy to embed into your own applications or use standalone from the terminal.

---

## Why Use Ollama?

### Privacy First
All computation happens on your hardware. Your prompts, your documents, your conversations — none of it ever touches an external server. This is critical for developers working with sensitive data, healthcare applications, legal documents, or anything where data sovereignty matters.

### Cost Efficiency
Once you have the model downloaded, inference is completely free. No per-token billing. No monthly caps. Run as many requests as your hardware allows.

### Offline Capability
Ollama works entirely offline after the initial model download. This makes it ideal for air-gapped environments, embedded systems, or simply working on a plane.

### Developer-Friendly API
Ollama exposes a local REST API that is **OpenAI-compatible**, meaning you can swap out your existing OpenAI calls with minimal code changes and point them at `http://localhost:11434` instead.

---

## Current Model Ecosystem (2026)

The Ollama model library has grown dramatically. Some of the most popular and capable models available today include:

### General Purpose
- **Llama 3.3 (70B)** — Meta's flagship open-weight model, strong across reasoning, coding, and conversation.
- **Mistral and Mixtral** — Fast, efficient models from Mistral AI known for their speed-to-quality ratio.
- **Gemma 3** — Google's open model family, available in 2B, 9B, and 27B parameter sizes.
- **Phi-4** — Microsoft's small but surprisingly capable model, great for resource-constrained hardware.
- **Command R+** — Cohere's retrieval-augmented model, optimized for grounded responses.

### Coding Specialists
- **DeepSeek Coder V2** — Exceptional at code generation and debugging.
- **Qwen2.5-Coder** — Alibaba's coding-focused model, highly competitive with commercial alternatives.
- **CodeLlama** — Meta's code-specialized Llama variant.

### Vision and Multimodal
- **LLaVA** — Combines language and vision understanding.
- **Moondream** — An ultra-lightweight vision model for edge devices.
- **Llama 3.2 Vision** — Meta's multimodal addition to the Llama 3 family.

### Embedding Models
- **nomic-embed-text** — High-quality text embeddings for semantic search and RAG pipelines.
- **mxbai-embed-large** — A strong alternative for embedding workloads.

You can browse all available models at the Ollama model library and pull them with:

```bash
ollama pull <model-name>
```

---

## Integration Capabilities

One of Ollama's biggest strengths is how well it plugs into the broader AI tooling ecosystem.

### LangChain and LlamaIndex
Both of the most popular RAG frameworks have native Ollama integrations. You can build full retrieval-augmented generation pipelines — indexing your own documents and querying them with a local LLM — without touching a commercial API.

```python
from langchain_ollama import OllamaLLM

llm = OllamaLLM(model="llama3.2")
response = llm.invoke("Explain transformer architecture in simple terms.")
print(response)
```

### Open WebUI
Open WebUI is a polished, self-hosted chat interface that connects directly to Ollama. It gives you a ChatGPT-like browser experience backed entirely by your local models. It supports multi-model conversations, conversation history, document uploads, and image analysis.

### Anything LLM
A full-stack application that layers document ingestion, vector storage, and chat on top of Ollama — ready to deploy as a private knowledge base for your team.

### Continue (VS Code Extension)
The Continue extension for VS Code integrates directly with Ollama, giving you a local Copilot-style assistant inside your editor. Code completions, inline edits, and chat — all running privately on your machine.

### Dify and n8n
Both popular workflow automation and LLM orchestration platforms now support Ollama as a provider, letting you build complex agentic pipelines without cloud dependencies.

### Direct API Access
Ollama's REST API is simple and powerful:

```bash
curl http://localhost:11434/api/chat -d '{
  "model": "llama3.2",
  "messages": [
    { "role": "user", "content": "What is the speed of light?" }
  ]
}'
```

---

## Web Search with Ollama

Out of the box, Ollama models don't have internet access — they are static snapshots of training data. However, the ecosystem has built robust solutions for grounding model responses with live web data.

### Tool Calling + Search APIs
Modern Ollama models support **tool calling** (also known as function calling), where the model can request data from external tools. By connecting a search API — like Brave Search, SearXNG, or DuckDuckGo — you can build a lightweight agent loop:

1. User asks a question.
2. Model decides to call the `web_search` tool.
3. Your code fetches live results.
4. Results are injected back into the context.
5. Model generates a grounded answer.

### Perplexica
Perplexica is an open-source Perplexity alternative that uses Ollama under the hood. It combines SearXNG for web retrieval with a local model for synthesis, giving you a private, search-augmented AI assistant.

### Open WebUI Web Search Integration
Open WebUI has built-in web search support. You configure a search backend and the interface automatically retrieves and cites sources when the model needs fresh information.

---

## Thinking Capabilities

The concept of **extended thinking** — where a model reasons step-by-step before producing a final answer — has become a major differentiator in 2025–2026. Several models available through Ollama now support this.

### DeepSeek R1
DeepSeek R1 is a reasoning-focused model that produces explicit `<think>...</think>` blocks before its final response. This chain-of-thought reasoning dramatically improves accuracy on math, logic, and complex multi-step problems. It is available in multiple sizes on Ollama, from 1.5B all the way to 70B parameters.

```bash
ollama run deepseek-r1:14b
```

### QwQ
Qwen's reasoning model that similarly produces extended internal monologue before settling on an answer. It performs competitively with much larger models on benchmarks like MATH and GPQA.

### Ollama's Thinking Token Support
Ollama now surfaces thinking tokens through its API, so you can build interfaces that show the model's reasoning process live — or strip it out if you only want the final answer. This is particularly useful when debugging model behavior or building transparent AI systems.

---

## Streaming Capabilities

Streaming is a first-class feature in Ollama. Rather than waiting for the entire response to be generated, tokens are emitted in real-time — creating the familiar typewriter effect you see in commercial chat interfaces.

### CLI Streaming
The Ollama CLI streams by default. Output appears token-by-token as soon as the model begins generating.

### API Streaming
The REST API streams via newline-delimited JSON (NDJSON). Each chunk is a partial response that your client can render immediately:

```bash
curl http://localhost:11434/api/generate -d '{
  "model": "llama3.2",
  "prompt": "Write a short poem about the ocean.",
  "stream": true
}'
```

Each line of the response looks like:

```json
{"model":"llama3.2","response":"Waves","done":false}
{"model":"llama3.2","response":" crash","done":false}
{"model":"llama3.2","response":" endlessly","done":false}
...
{"model":"llama3.2","response":"","done":true}
```

### Python Streaming with the Ollama SDK
The official Python library makes streaming trivial:

```python
import ollama

for chunk in ollama.chat(
    model="llama3.2",
    messages=[{"role": "user", "content": "Tell me about black holes."}],
    stream=True
):
    print(chunk["message"]["content"], end="", flush=True)
```

### Async Streaming
For web applications, Ollama pairs well with async Python frameworks like FastAPI:

```python
from fastapi import FastAPI
from fastapi.responses import StreamingResponse
import ollama

app = FastAPI()

@app.get("/chat")
async def stream_chat(prompt: str):
    async def generate():
        async for chunk in await ollama.AsyncClient().chat(
            model="llama3.2",
            messages=[{"role": "user", "content": prompt}],
            stream=True
        ):
            yield chunk["message"]["content"]
    return StreamingResponse(generate(), media_type="text/plain")
```

---

## Hardware Requirements

Ollama runs on CPU, GPU, and Apple Silicon. Performance varies significantly based on model size and your hardware:

| Model Size | Minimum VRAM | Recommended For |
|---|---|---|
| 1B – 3B | CPU only | Edge devices, testing |
| 7B – 9B | 6–8 GB VRAM | Daily use on mid-range GPUs |
| 13B – 14B | 10–12 GB VRAM | Power users |
| 30B – 70B | 24–48 GB VRAM | High-end workstations |

Apple Silicon (M1/M2/M3/M4) users have an advantage here — the unified memory architecture allows the GPU to access all system RAM, making it practical to run 30B+ models on a MacBook Pro.

For NVIDIA users, Ollama automatically detects and uses CUDA. For AMD, ROCm support continues to improve with each release.

---

## Getting Started

Installing Ollama takes about 60 seconds:

**macOS / Linux:**
```bash
curl -fsSL https://ollama.com/install.sh | sh
```

**Windows:**
Download the installer from the official Ollama website.

Then pull and run your first model:

```bash
ollama pull llama3.2
ollama run llama3.2
```

You are now running a state-of-the-art language model locally. No account. No API key. No monthly bill.

---

## The Bigger Picture

Ollama represents a significant shift in how developers and researchers interact with AI. The open-source model ecosystem — driven by Meta, Mistral, Alibaba, Google, and the broader research community — has closed the gap with proprietary models at an astonishing pace.

For developers, this means the ability to build AI-powered applications without coupling to a commercial provider. For individuals, it means genuine AI privacy. For organizations, it means on-premise deployment without the compliance headaches of sending data to external APIs.

Whether you are building a personal knowledge base, a private coding assistant, a document analysis pipeline, or just experimenting with the latest open-weight models, Ollama is the fastest path from zero to a running local AI stack.

The models will only get better. The hardware will only get faster. And with tools like Ollama lowering the barrier to entry, local AI is no longer just for researchers — it is for everyone.
