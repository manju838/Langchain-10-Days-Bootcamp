# LangChain in 10 Days

A beginner bootcamp that runs entirely on **free** AI APIs.

No paid account and no GPU. If you can write a `for` loop, you can do this.
Days 9 and 10 use Docker to run the vector database, which is one command.

> This course lives inside a larger training repo. Every command below runs
> from **this** folder, `2026/langchain-10-days`, not from the repo root.

---

## The ten days

Each day is one slide deck and one notebook, about 60 to 90 minutes.

| Day | Title | What it covers | You end up with |
|---|---|---|---|
| 1 | Your First AI Program | `ChatOpenRouter`, `invoke`, the reply object, tokens and cost, temperature, `max_tokens` | A script that answers a question |
| 2 | Roles: System, Human, Assistant | The model sees a list of messages, what a long system message costs, `pretty_print` | The same assistant with three personalities |
| 3 | Prompt Templates | `ChatPromptTemplate`, placeholders, the `KeyError`, literal braces, `partial`, `MessagesPlaceholder` | A reusable email-reply writer |
| 4 | Chains: The Pipe | The pipe operator, `StrOutputParser`, `invoke` and `batch` and `stream`, fallbacks | A summariser chain |
| 5 | Structured Output | Pydantic models, `with_structured_output`, `Literal`, why optional fields come back empty | A review reader that returns an object |
| 6 | Memory | Checkpointers, `thread_id`, tokens growing every turn, `trim_messages`, summarisation, `SqliteSaver` | A chatbot that remembers |
| 7 | Tools | `@tool`, the schema the model sees, `bind_tools`, `ToolMessage`, a live web search | An assistant with a calculator and web search |
| 8 | Your First Agent | `create_agent`, reading the trace, what an agent costs, `ModelCallLimitMiddleware` | An agent that plans two steps |
| 9 | Documents, Chunks and Vectors | Splitting, embeddings, similarity by hand, Qdrant, metadata filters, where vector search fails | A search that works by meaning |
| 10 | RAG with RAGWire | One config file, ingest, retrieve, citations, refusal, `top_k`, an agent that decides when to search | A RAG assistant over two real 10-K filings that cites its pages |

Days 1 to 8 need nothing installed beyond Python. Day 9 downloads a small
embedding model, about 130 MB, and runs it on your CPU. Days 9 and 10 need
Docker for the vector database.

---

## Setup, once

### 1. Get a free API key

Go to **[openrouter.ai/keys](https://openrouter.ai/keys)**, sign in, and
create a key. It starts with `sk-or-`.

OpenRouter gives you access to many models through one key. The models
this course uses end in `:free`, so you are not charged.

> Free models have a daily limit. If a cell suddenly starts failing with a
> **429**, you have hit it. Wait, or switch to another `:free` model. It is
> not your code.

From **Day 7** you also need a second free key, for the web search tool:
**[ollama.com/settings/keys](https://ollama.com/settings/keys)**. Days 1 to 6
do not use it, so you can leave it until then.

### 2. Install Python packages

This project uses **[uv](https://docs.astral.sh/uv/)**. If you do not have it:

```bash
winget install astral-sh.uv
```

On macOS or Linux:

```bash
curl -LsSf https://astral.sh/uv/install.sh | sh
```

Then, from the project folder, one command does everything:

```bash
uv sync
```

That creates `.venv`, installs the right Python if you do not have it, and
installs every package at the exact version recorded in `uv.lock`. You do not
need to activate anything.

Run things with `uv run`, which uses that environment without you activating
anything:

```bash
uv run jupyter notebook notebooks
```

### 3. Save your keys

Copy `.env.example` to a new file called `.env`, and paste your keys in:

```
OPENROUTER_API_KEY=sk-or-...your key...
OLLAMA_API_KEY=...your key...
```

`.env` is listed in `.gitignore`, so it will never be committed. **Never
paste a key into a notebook cell**, and never print one.

### 4. Start the vector database (needed from Day 9)

Days 1 to 8 need nothing here. Days 9 and 10 store vectors in **Qdrant**, which
runs in Docker. Install [Docker Desktop](https://www.docker.com/products/docker-desktop/),
then from the project folder:

```bash
docker compose up -d
```

Check it came up:

```bash
docker compose ps
```

You can also open **http://localhost:6333/dashboard** in a browser and see the
collections as you create them. To stop it later, `docker compose down`. Your
data stays in a Docker volume, so starting it again keeps everything.

### 5. Register the kernel, once

So that Jupyter runs this environment and not some other Python on your
machine:

```bash
uv run python -m ipykernel install --user --name lc10 --display-name "Python (langchain-10-days)"
```

### 6. Start Jupyter

```bash
uv run jupyter notebook notebooks
```

Open `Day01_First_AI_Program.ipynb`, choose **Python (langchain-10-days)** from
the Kernel menu, and run the first cell.

---

## How to use each day

1. Open `slides_pdf/DayNN_Slides.pdf`. Read the pictures.
2. Open `notebooks/DayNN_*.ipynb` and run the cells one at a time.

The slides hold the diagrams and the explanation. The notebooks hold the
code. Neither repeats the other.

`slides_pdf/` also has three longer talks that go past the ten days:
**Graph Engineering**, **Loop Engineering** and **Memory Engineering**. They
are background reading, not part of any day.

---

## What is in this folder

```
langchain-10-days/
├── README.md               this file
├── pyproject.toml          every package, pinned
├── .env.example            copy to .env, add your free keys
├── docker-compose.yml      Qdrant, for Days 9 and 10
├── notebooks/
│   ├── Day01_*.ipynb ... Day10_*.ipynb
│   ├── config/             the Day 10 RAG pipeline, in YAML
│   ├── data/               three short documents, for Day 9
│   └── filings/            two real 10-K PDFs, for Day 10
└── slides_pdf/             one deck per day
```

---

## If something breaks

| What you see | What it means |
|---|---|
| `OPENROUTER_API_KEY not found` | Your `.env` is missing, or it is not in the `notebooks` folder |
| `401` | The key is wrong. Copy it again, with no spaces |
| `429` | You hit the free per-minute limit. Wait a minute and run the cell again |
| `ModuleNotFoundError` | Jupyter is on the wrong kernel. Pick **Python (langchain-10-days)**, or run `uv sync` again |
| Day 7 web search fails to authenticate | `OLLAMA_API_KEY` is missing from `.env`. It is a separate free key |
| Day 9 is slow the first time | It is downloading a small embedding model. Once only |
| Every HTTPS call hangs until it times out | You are behind a TLS-inspecting proxy. `uv sync` installs `truststore`, which fixes it |
| `Connection refused` on port 6333 | Qdrant is not running. `docker compose up -d` from the project folder |
| `docker: command not found` | Docker Desktop is not installed, or not started |

---

KGP Talkie · Laxmi Kant Tiwari
[YouTube](https://www.youtube.com/@KGPTalkie) ·
[LinkedIn](https://www.linkedin.com/in/laxmimerit) ·
[kgptalkie.com](https://kgptalkie.com)
