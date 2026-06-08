# InsureClimate AI — Tavily-Powered Agentic Research Tool

> An intelligent, agentic research assistant that fetches, analyzes, scores, and summarizes news articles on **climate risk and insurance**, powered by **Tavily Search**, **Google Gemini**, and **LangChain**.

---

## Overview

InsureClimate AI is a personal/hackathon project built to automate the work of an insurance risk analyst. Given a user query, the system autonomously constructs optimized search queries, fetches real-time articles from credible financial and insurance sources, scores their credibility in parallel, generates structured LLM summaries, evaluates them with ROUGE + cosine similarity, and exports a polished PDF report — all orchestrated through a LangChain agent loop.

---

## Key Features

- **Agentic Query Construction** — Uses Gemini to build Boolean-optimized Tavily search queries (`AND`/`OR` operators) from raw user input, selected categories, and region filters
- **Multi-Category & Region Filtering** — 13 insurance categories (Climate Risk, InsureTech, Reinsurance, Cyber, Agriculture, etc.) and 5 geographic regions
- **Parallel Credibility Scoring** — Articles are scored 1–5 concurrently via `ThreadPoolExecutor` before analysis
- **LLM-Powered Analysis** — Each article is analyzed for: Subject, Impact on Insurance Regulations, Financial Risks & Opportunities, and Geographical/Industry Impact
- **Feedback-Driven Refinement** — Users can provide negative feedback on summaries; the agent regenerates an improved version addressing the critique
- **ROUGE + Cosine Similarity Evaluation** — Every summary is evaluated against source content using ROUGE-1/2/L scores and `all-MiniLM-L6-v2` sentence embeddings for semantic similarity
- **PDF Report Generation** — Full structured report exported via `fpdf2` with article analyses, scores, metadata, and timestamp
- **ConversationBufferMemory** — Agent retains chat history for multi-turn interactions

---

## Architecture & Workflow

```
User Query
    │
    ▼
Query Constructor (Gemini + LangChain Agent)
    │  ─ Boolean query with AND/OR operators
    │  ─ Category terms + Region terms injected
    ▼
Tavily Search API (Advanced Depth)
    │  ─ Domain-restricted: reuters.com, swissre.com,
    │    insurancejournal.com, chubb.com, pwc.com, etc.
    │  ─ Top results fetched with max_results
    ▼
Parallel Credibility Scoring (ThreadPoolExecutor)
    │  ─ Each article scored 1-5 by Gemini
    │  ─ Low-credibility articles filtered out
    ▼
Article Analysis (per article)
    │  ─ Individual LLM summary
    │  ─ ROUGE-1/2/L scoring
    │  ─ Cosine similarity via SentenceTransformer
    ▼
Combined Summary Generation
    │  ─ Cross-article synthesis
    │  ─ Structured: Subject → Regulations → Financials → Geography
    ▼
[Optional] Feedback Loop → Summary Refinement
    ▼
PDF Report Export (fpdf2)
```

---

## Tech Stack

| Component | Technology |
|---|---|
| **LLM** | Google Gemini 1.5 Flash / Pro (`langchain-google-genai`) |
| **Search** | Tavily Search API (`TavilySearchResults`) |
| **Agent Framework** | LangChain (`ZERO_SHOT_REACT_DESCRIPTION` agent) |
| **Memory** | `ConversationBufferMemory` (chat history) |
| **Evaluation** | `rouge-score` + `sentence-transformers` (`all-MiniLM-L6-v2`) + cosine similarity |
| **Parallelism** | `concurrent.futures.ThreadPoolExecutor` |
| **Report Export** | `fpdf2` (PDF generation) |
| **Environment** | Google Colab (Python 3) |

---

## Search Categories

The agent maps 13 configurable topic categories to optimized keyword terms:

| # | Category | Example Terms |
|---|---|---|
| 1 | Climate Risk | `climate risk` |
| 2 | Insurance | `insurance` |
| 3 | InsureTech | `insurance technology` |
| 4 | Policies & Regulations | `insurance policies regulations` |
| 5 | Sustainability / ESG | `sustainability ESG` |
| 6 | Reinsurance & Risk Management | `reinsurance risk management` |
| 7 | Natural Catastrophes | `natural disasters catastrophes` |
| 8 | Market Trends | `insurance market trends financial insights` |
| 9 | Cyber Insurance | `cyber insurance cybersecurity` |
| 10 | Health & Life Insurance | `health insurance life insurance` |
| 11 | Auto & Mobility Insurance | `auto insurance mobility insurance` |
| 12 | Agricultural Insurance | `agriculture insurance climate impact` |
| 13 | Litigation & Claims | `insurance claims litigation` |

---

## Evaluation Metrics

Each generated summary is scored automatically against the source article content:

- **ROUGE-1 / ROUGE-2 / ROUGE-L** — n-gram overlap between summary and source
- **Cosine Similarity** — Semantic similarity using `all-MiniLM-L6-v2` sentence embeddings
- Scores are displayed in the terminal and embedded in the PDF report

---

## Output: PDF Report

The exported `InsureClimateReport.pdf` includes:
- Report title, query, and generation timestamp
- **Combined cross-article summary** with structured insurance risk insights
- **Per-article section** (new page per article) with: title, individual summary, relevance/similarity score, and source URL

---

## Setup & Usage

### Prerequisites
```bash
pip install langchain langchain-google-genai langchain-community tavily-python
pip install sentence-transformers rouge-score fpdf2 requests python-dotenv
```

### API Keys Required
```python
GOOGLE_API_KEY = "your-gemini-api-key"
TAVILY_API_KEY = "your-tavily-api-key"
```

### Run
1. Open `Copy-of-Tavily-Agent.ipynb` in Google Colab
2. Set your API keys in the designated cell
3. Run all cells — the CLI will prompt for:
   - Search query (e.g., `weather insurance USA`)
   - Category selection (comma-separated numbers, max 3)
   - Region selection
   - Sort preference (Latest / Most Relevant / By Source)
4. Review terminal output and collect `InsureClimateReport.pdf`

---

## Sample Queries Tested

- `"Latest research on AI in healthcare from reputable sources"` — Agent used Tavily to surface BMC Medical Education and PubMed articles
- `"weather AND USA AND insurance regulations"` — Agent refined query iteratively to target climate risk + insurance regulatory content
- `"weather insurance USA"` → Optimized to: `("weather" AND "USA") AND ("insurance policies" OR "insurance regulations" OR "climate risk insurance")`

---

## Skills Demonstrated

`LangChain Agents` · `Tavily Search API` · `Google Gemini (1.5 Flash/Pro)` · `Agentic RAG` · `Prompt Engineering` · `Parallel Processing` · `NLP Evaluation (ROUGE + Cosine Similarity)` · `Sentence Transformers` · `PDF Report Generation` · `ConversationBufferMemory`
