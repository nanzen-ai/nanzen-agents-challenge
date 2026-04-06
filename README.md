# Agent Technical Challenge

A multi-agent system that analyzes account data and produces PDF reports.
Your job is to make it work well.

---

## The Problem

**Meridian Health** (account `MERID-001`) is a healthcare-tech customer whose
contract renewal is approaching. The account manager needs a **risk assessment
report** that synthesizes billing, product usage, and support data into
actionable findings.

The system has the pieces: agents that can read CSV data and generate PDFs.
But the current implementation is rough — the agent wastes steps, sometimes
hallucinates numbers, and the architecture has blind spots.

**Your goal: make the system produce a useful, accurate risk report for
Meridian Health.**

---

## Before the Session

Clone the repo. Read the code. Come with questions.

```bash
git clone <repo-url>
cd agent-challenge
make install

# Configure your LLM provider (see .env.example)
cp .env.example .env
# Edit .env with your MODEL_ID, API_KEY, and API_BASE
set -a && source .env && set +a

# Try running it
make run ARGS="--task billing_summary"
```

### Environment Variables

| Variable | Required | Description |
|----------|----------|-------------|
| `MODEL_ID` | Yes | Model identifier (e.g. `accounts/fireworks/models/minimax-m2p5`, `qwen3.5-35b`) |
| `API_KEY` | No | API key for your provider (not needed for local servers) |
| `API_BASE` | No | Base URL (e.g. `http://localhost:8080/v1` for llama.cpp) |

Works with any OpenAI-compatible API: Fireworks, Together, OpenRouter, OpenAI,
Ollama, llama.cpp, etc. See `.env.example` for examples.

### What to look at

The codebase is small. Read it however you like. The key pieces:

- `src/challenge/agent.py` — the `ActorAgent` wraps a
  [smolagents CodeAgent](https://huggingface.co/docs/smolagents) that writes
  and executes Python to use tools
- `src/challenge/tools/` — `CSVReaderTool` reads data, `PDFReportTool`
  generates reports
- `src/challenge/tasks.py` — task definitions that drive the agents
- `src/challenge/runner.py` — spawns agents and collects results
- `data/*.csv` — the raw data the agents work with

Come with observations, questions, or opinions about the code and the data.
The questions you ask tell us as much as the code you write.

---

## Live Session

We'll work through this together. There's no fixed checklist — we'll go
where the conversation leads. But here's the shape of what we'll explore:

### Make it work

The agent currently struggles to complete tasks cleanly. Start here:
get the system to produce a report for the Meridian renewal. Pay attention
to what the agent actually does — where it gets stuck, what it misses,
whether the numbers it produces are trustworthy.

### Make it right

Once the agent produces output, look at what it finds. Does the report
surface the things that actually matter for a renewal decision? Would you
trust this report if you were the account manager walking into a
negotiation?

### Make it better

If we get here, we'll talk about what's missing from the architecture.
Some threads to pull on:

- How would you know if the agent's output is any good without reading it?
- What happens when three agents run in parallel and all hit the same data?
- What would you change if the CSVs had 500k rows instead of 500?
- Is a CodeAgent the right abstraction here, or would you structure this
  differently?

We're not looking for you to solve all of these — we want to see how you
think about them.

---

## Project Structure

```
agent-challenge/
├── README.md
├── pyproject.toml
├── Makefile                 # install, style, test, run
├── .env.example             # LLM provider config template
├── data/                    # CSV data (shared context)
│   ├── accounts.csv
│   ├── billing.csv
│   ├── product_usage.csv
│   ├── support_tickets.csv
│   ├── crm_interactions.csv
│   ├── emails.csv
│   ├── contracts.csv
│   └── purchase_orders.csv
├── output/                  # Generated PDF reports (gitignored)
├── src/
│   └── challenge/
│       ├── agent.py         # ActorAgent
│       ├── runner.py        # Task runner
│       ├── tasks.py         # Task definitions
│       └── tools/
│           ├── csv_reader.py
│           └── pdf_report.py
└── tests/
    └── test_tools.py
```

---

## Commands

```bash
make install              # Install dependencies
make test                 # Run tests
make style                # Format with ruff
make run                  # Run all tasks
make run ARGS="--task X"  # Run a specific task
make run ARGS="--list"    # List available tasks
make run ARGS="--parallel"  # Run tasks concurrently
```
