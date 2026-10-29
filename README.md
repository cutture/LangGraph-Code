# LangGraph-Code

Hands-on LangGraph notebooks, from a first two-node graph up to the parts an agent needs before it goes to production: guardrails, agent evaluation and an LLM gateway. The `Agentic-Chatbot-using-LangGraph` folder turns those ideas into a working Streamlit chatbot.

## Highlights

### Guardrails ([`13_guardrails.ipynb`](13_guardrails.ipynb))

- Deterministic guardrails (keyword and regex checks) next to model-based ones (an LLM classifies input as safe or unsafe), with the trade-offs of each.
- LangChain `PIIMiddleware` that redacts emails and card numbers before they reach the model and blocks messages containing API keys.
- `HumanInTheLoopMiddleware` that pauses the agent before `send_email` or `delete_records` and resumes on an approve or reject decision.
- Custom before-agent and after-agent guardrails for input filtering and output checks.

### Agent evaluation ([`AI_Agent_Evaluation.ipynb`](AI_Agent_Evaluation.ipynb))

A small course-sales agent with three tools, scored on five dimensions:

| Dimension | What it checks |
| --- | --- |
| Answer correctness | The final answer contains the expected result |
| Tool selection | The agent called the right tools, in any order |
| Trajectory | The tools were called in the expected order |
| Latency | Time per run |
| Safety | The agent did not leak a planted secret or invent prices |

It also includes an LLM-as-a-judge example for open-ended answers.

### LLM gateway with LiteLLM ([`llm_gateway.ipynb`](llm_gateway.ipynb))

- One `completion()` call across OpenAI, Anthropic, Gemini and Groq.
- Automatic fallbacks when a provider fails, demonstrated with a deliberately broken model name.
- Per-call cost tracking, in-memory caching, and a `Router` that maps aliases such as `fast-cheap` and `smart-coding` to providers.
- Load balancing across several API keys, with routing strategies such as least-busy.

## Workflow notebooks

| Notebook | Pattern |
| --- | --- |
| [`1_Temperature_Conversion_workflow`](1_Temperature_Conversion_workflow.ipynb) | Basic state, nodes and edges (no LLM) |
| [`2_Simple_QA_LLM_Workflow`](2_Simple_QA_LLM_Workflow.ipynb) | Single LLM node |
| [`3_Prompt_Chaining_Workflow`](3_Prompt_Chaining_Workflow.ipynb) | Prompt chaining: outline, then blog post |
| [`4_Employee_analytics_Workflow`](4_Employee_analytics_Workflow.ipynb) | Parallel branches that merge into a summary |
| [`5_Essay_workflow`](5_Essay_workflow.ipynb) | Parallel LLM evaluators with structured output |
| [`6_Content_Moderation_Workflow`](6_Content_Moderation_Workflow.ipynb) | Conditional routing: approve, flag or reject |
| [`7_Review_workflow`](7_Review_workflow.ipynb) | Sentiment-based routing with structured output |
| [`8_Iterative_Workflows`](8_Iterative_Workflows.ipynb) | Generate, evaluate, optimise loop |
| [`9_Persistence`](9_Persistence.ipynb) | Checkpointers, state history and time travel |
| [`10_HITL`](10_HITL.ipynb) | Human-in-the-loop with `interrupt` and `Command` |
| [`11_subgraphs`](11_subgraphs.ipynb) / [`12_subgraph_shared`](12_subgraph_shared.ipynb) | Subgraphs with separate and shared state |
| [`Chatbot_workflow`](Chatbot_workflow.ipynb) | Chatbot graph with message history |

## Agentic chatbot

[`Agentic-Chatbot-using-LangGraph/`](Agentic-Chatbot-using-LangGraph) builds the same chatbot in stages, each with its own Streamlit app:

| App | Adds |
| --- | --- |
| `app_simple.py` | Streaming chat |
| `app_thread.py`, `app_db.py` | Multiple conversations, persisted in SQLite |
| `app_tool.py` | Tool calling: Tavily web search, weather, stock price and a calculator |
| `app_rag.py` | Answers from a PDF indexed in FAISS |
| `app_hitl.py` | Human approval (`interrupt`) before the agent buys stock |

The deployable version, with Docker and a GitHub Actions pipeline to AWS EC2, is in [cutture/Agentic-Chatbot-using-LangGraph](https://github.com/cutture/Agentic-Chatbot-using-LangGraph).

## Setup

Requires Python 3.11 or newer.

```bash
git clone https://github.com/cutture/LangGraph-Code.git
cd LangGraph-Code
python -m venv venv
# Windows: venv\Scripts\activate    macOS/Linux: source venv/bin/activate
pip install -r requirements.txt jupyter
```

Create a `.env` file in the project root. Each notebook only needs the keys for the providers it uses:

```env
OPENAI_API_KEY=...
GROQ_API_KEY=...
GOOGLE_API_KEY=...
ANTHROPIC_API_KEY=...
```

`.env` is in `.gitignore`, so keys are never committed.

```bash
jupyter notebook
```

To run a chatbot app:

```bash
cd Agentic-Chatbot-using-LangGraph
pip install -r requirements.txt
streamlit run app_simple.py
```

## Stack

LangGraph, LangChain 1.x, LiteLLM, OpenAI, Groq, Google Gemini, Anthropic, FAISS, SQLite, Streamlit, Pydantic.
