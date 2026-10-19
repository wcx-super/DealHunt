# DealHunt

A multi-agent system that scans online deal feeds, estimates each product's true market value, and sends a push notification when it finds a bargain.

## How it works

```
Planning Agent
 ├── Scanner Agent    – pulls DealNews RSS feeds, uses an LLM to pick 5 deals with clear prices
 ├── Ensemble Agent   – estimates the true price of each deal
 │    ├── Preprocessor      – rewrites the description (LiteLLM, default: Ollama llama3.2)
 │    ├── Frontier Agent    – RAG: similar products from ChromaDB + OpenAI model (80%)
 │    └── Specialist Agent  – fine-tuned pricing model hosted on Modal (20%)
 └── Messaging Agent  – sends a Pushover alert if the discount is over $50
```

Deals that have already been surfaced are saved to `memory.json` so the same deal isn't reported twice.

## Setup

Requires Python 3.12+ and [uv](https://docs.astral.sh/uv/).

```bash
uv sync
```

Create a `.env` file:

```
OPENAI_API_KEY=...
HF_TOKEN=...
PUSHOVER_USER=...
PUSHOVER_TOKEN=...
# optional, defaults to ollama/llama3.2
PRICER_PREPROCESSOR_MODEL=...
```

You will also need:
- A ChromaDB vector store at `products_vectorstore/` with a `products` collection (not included in this repo because of its size)
- A Modal app named `pricer-service` that exposes a `Pricer` class
- Ollama running locally if you keep the default preprocessor model

## Usage

Run the web UI, which re-runs the agents every 5 minutes and shows the deals it finds plus a 3D plot of the vector store:

```bash
uv run price_is_right.py
```

Or run a single pass from the command line:

```bash
uv run deal_agent_framework.py
```
