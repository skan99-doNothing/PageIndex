# PageIndex — CLAUDE.md

## What This Project Does

PageIndex is a **reasoning-based RAG (Retrieval-Augmented Generation)** system that converts long professional documents (PDFs and Markdown) into hierarchical tree structures for intelligent, vectorless retrieval.

Key characteristics:
- **No vector database** — uses LLM reasoning instead of embedding/semantic similarity search
- **No chunking** — documents are indexed into natural, hierarchical sections mirroring a table of contents
- **Reasoning-based retrieval** — simulates how a human expert navigates a complex document via tree search
- **Explainable** — every retrieval result is traceable to a specific page/section
- Achieved **98.7% accuracy on FinanceBench** (financial document Q&A benchmark)

The output is a JSON tree structure that can be used as the index for downstream RAG pipelines.

---

## Tech Stack

| Layer | Technology |
|---|---|
| Language | Python 3 |
| LLM abstraction | [litellm](https://github.com/BerriAI/litellm) 1.82.0 (supports OpenAI, Anthropic, etc.) |
| PDF extraction | pymupdf 1.26.4, PyPDF2 3.0.1 |
| Config | pyyaml 6.0.2 |
| Env vars | python-dotenv 1.1.0 |
| Async | asyncio (built-in) |
| Examples | Jupyter notebooks |
| Default model | `gpt-4o-2024-11-20` (configurable via `pageindex/config.yaml`) |

---

## Installation & Running Locally

### 1. Install dependencies

```bash
pip3 install --upgrade -r requirements.txt
```

### 2. Set up environment variables

Create a `.env` file in the project root:

```bash
CHATGPT_API_KEY=your_openai_api_key_here
# or equivalently:
OPENAI_API_KEY=your_openai_api_key_here
```

To use a different LLM (e.g. Claude), update `pageindex/config.yaml`:

```yaml
model: anthropic/claude-sonnet-4-6
```

and set the appropriate API key (`ANTHROPIC_API_KEY`).

### 3. Run

**Process a PDF:**
```bash
python3 run_pageindex.py --pdf_path /path/to/document.pdf
```

**Process a Markdown file:**
```bash
python3 run_pageindex.py --md_path /path/to/document.md
```

Output is written to `results/{document_name}_structure.json`.

### Common optional flags

| Flag | Default | Description |
|---|---|---|
| `--model` | `gpt-4o-2024-11-20` | LLM model to use |
| `--toc-check-pages` | `20` | Pages to scan for TOC (PDF only) |
| `--max-pages-per-node` | `10` | Max pages per tree node (PDF only) |
| `--max-tokens-per-node` | `20000` | Max tokens per node (PDF only) |
| `--if-add-node-summary` | `yes` | Generate summaries for each node |
| `--if-thinning` | `no` | Remove redundant nodes (Markdown only) |

---

## Folder Structure

```
PageIndex/
├── run_pageindex.py          # CLI entry point — parses args, calls core library
├── requirements.txt          # Python dependencies
├── CHANGELOG.md              # Version history
│
├── pageindex/                # Core library module
│   ├── __init__.py           # Public exports
│   ├── config.yaml           # Default configuration (model, limits, flags)
│   ├── page_index.py         # PDF processing: TOC detection, tree building, verification
│   ├── page_index_md.py      # Markdown processing: header extraction, tree building
│   └── utils.py              # Shared utilities: LLM calls, token counting, PDF extraction, logging
│
├── cookbook/                 # Jupyter notebook examples
│   ├── pageindex_RAG_simple.ipynb       # Minimal vectorless RAG example
│   ├── vision_RAG_pageindex.ipynb       # Vision-based RAG (page images, no OCR)
│   ├── agentic_retrieval.ipynb          # Advanced agentic retrieval
│   └── pageIndex_chat_quickstart.ipynb  # Chat platform quickstart
│
├── tutorials/                # Strategy guides
│   ├── tree-search/          # Tree traversal strategies & prompts
│   └── doc-search/           # Multi-document search strategies
│
├── tests/
│   ├── pdfs/                 # Sample PDF documents for testing
│   └── results/              # Expected JSON tree output fixtures
│
└── scripts/                  # GitHub automation (issue management)
```
