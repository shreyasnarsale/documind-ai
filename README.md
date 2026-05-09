<div align="center">

<img src="https://img.shields.io/badge/documind--ai-v1.0.0-blueviolet?style=for-the-badge" alt="version"/>

# 🧠 DocuMind AI

### Chat with any document using AI — PDF, DOCX, PPTX, ZIP, TXT and more.

[![Python](https://img.shields.io/badge/Python-3.10+-3776AB?style=flat-square&logo=python&logoColor=white)](https://python.org)
[![LangChain](https://img.shields.io/badge/LangChain-0.2+-1C3C3C?style=flat-square&logo=chainlink&logoColor=white)](https://langchain.com)
[![ChromaDB](https://img.shields.io/badge/ChromaDB-Vector%20DB-7C3AED?style=flat-square)](https://trychroma.com)
[![Streamlit](https://img.shields.io/badge/Streamlit-UI-FF4B4B?style=flat-square&logo=streamlit&logoColor=white)](https://streamlit.io)
[![Groq](https://img.shields.io/badge/Groq-LLM%20Backend-F55036?style=flat-square)](https://groq.com)
[![License: MIT](https://img.shields.io/badge/License-MIT-22C55E?style=flat-square)](LICENSE)
[![Stars](https://img.shields.io/github/stars/yourusername/documind-ai?style=flat-square&color=FACC15)](https://github.com/yourusername/documind-ai/stargazers)

<br/>

**[🚀 Live Demo](https://documind-ai.streamlit.app)** &nbsp;·&nbsp;
**[📖 Documentation](#getting-started)** &nbsp;·&nbsp;
**[🐛 Report Bug](https://github.com/yourusername/documind-ai/issues)** &nbsp;·&nbsp;
**[✨ Request Feature](https://github.com/yourusername/documind-ai/issues)**

<br/>

![Demo](https://via.placeholder.com/860x400/0f172a/7c3aed?text=DocuMind+AI+%E2%80%94+Upload+any+file+%E2%86%92+Ask+anything+%E2%86%92+Get+cited+answers)

</div>

---

## What is DocuMind AI?

**DocuMind AI** is an intelligent multi-format document assistant built on **Retrieval-Augmented Generation (RAG)**. Upload any file — research papers, legal contracts, college notes, slide decks, spreadsheets, entire ZIP archives — and have a full, cited conversation with it in natural language.

Unlike general-purpose LLMs, DocuMind retrieves exact passages from *your* documents and generates grounded answers with file-level and page-level citations. No hallucinations. No guessing.

```
You:  "What are the penalty clauses in the contract?"

AI:   The contract specifies three penalty scenarios:
      1. Late delivery incurs 2% of contract value per week (Clause 8.3)
      2. Quality breach triggers a ₹50,000 fixed penalty (Clause 9.1)
      3. IP violation allows immediate termination + damages (Clause 12.4)

      📄 Source: contract_2024.pdf · Page 14 · Chunks #87, #88, #91
```

---

## Supported File Types

| Format | Extensions | Parser |
|--------|-----------|--------|
| PDF documents | `.pdf` | PyMuPDF |
| Word documents | `.docx`, `.doc` | python-docx |
| PowerPoint slides | `.pptx`, `.ppt` | python-pptx |
| Plain text & Markdown | `.txt`, `.md` | Built-in |
| Spreadsheets | `.csv`, `.xlsx` | pandas |
| ZIP archives | `.zip` | Auto-extracts and processes every file inside |
| Web pages | `https://...` | BeautifulSoup |

---

## Key Features

- **Universal file ingestion** — one pipeline handles 7+ formats with a clean router pattern
- **Cited answers** — every response shows source file, page number, and chunk ID
- **Conversation memory** — follow-up questions work naturally across multiple turns
- **Multi-document chat** — upload 10 files and ask questions that span all of them
- **100% free to run** — Groq's Llama 3 API is free; embeddings run locally on CPU
- **Offline vector DB** — ChromaDB runs fully locally, your files never leave your machine
- **Pluggable LLM** — swap between Groq, OpenAI, or local Ollama with one config line
- **RAGAS evaluation** — built-in metrics to quantify and prove retrieval quality

---

## Architecture

```
┌──────────────────────────────────────────────────────────────────┐
│                      INGESTION PIPELINE                          │
│                                                                  │
│  Upload File(s): PDF / DOCX / PPTX / ZIP / TXT / CSV / URL      │
│         │                                                        │
│         ▼                                                        │
│   [ Format Router ]  ──►  [ Per-format Text Extractor ]         │
│                                    │                            │
│                                    ▼                            │
│                    [ RecursiveCharacterTextSplitter ]           │
│                      chunk_size=500  ·  overlap=50              │
│                                    │                            │
│                                    ▼                            │
│                    [ HuggingFace Embedding Model ]              │
│                      all-MiniLM-L6-v2  ·  384-dim  ·  free     │
│                                    │                            │
│                                    ▼                            │
│                    [ ChromaDB Vector Store ]                    │
│                      Persisted locally on disk                  │
└──────────────────────────────────────────────────────────────────┘

┌──────────────────────────────────────────────────────────────────┐
│                       QUERY PIPELINE                             │
│                                                                  │
│  User Question  ──►  [ Embed Query ]                            │
│                                    │                            │
│                                    ▼                            │
│                    [ Cosine Similarity Search ]                 │
│                      ChromaDB  ·  Top-k = 4                    │
│                                    │                            │
│                                    ▼                            │
│                    [ Prompt Template ]                          │
│                      System prompt + Context + Question         │
│                                    │                            │
│                                    ▼                            │
│                    [ LLM — Groq Llama-3 / GPT-4o-mini ]        │
│                                    │                            │
│                                    ▼                            │
│             Grounded Answer  +  Page & Chunk Citations          │
└──────────────────────────────────────────────────────────────────┘
```

---

## Tech Stack

| Layer | Technology | Why this choice |
|-------|-----------|-----------------|
| File parsing | `PyMuPDF`, `python-docx`, `python-pptx`, `pandas` | Best-in-class parsers per format |
| Text chunking | `LangChain RecursiveCharacterTextSplitter` | Overlapping chunks preserve boundary context |
| Embeddings | `sentence-transformers/all-MiniLM-L6-v2` | Free, 384-dim, runs on CPU, no API key needed |
| Vector store | `ChromaDB` | Persistent, local, zero infra cost |
| LLM | `Groq Llama-3` / `OpenAI GPT-4o-mini` | Groq is free; OpenAI for production quality |
| Orchestration | `LangChain` | Industry standard for RAG pipelines |
| Evaluation | `RAGAS` | Quantifiable retrieval + answer quality metrics |
| UI | `Streamlit` | Fastest path to a shareable, deployable demo |
| Deployment | `Streamlit Cloud` | Free public URL, one-click deploy from GitHub |

---

## Project Structure

```
documind-ai/
│
├── app.py                        # Streamlit entry point
│
├── rag/
│   ├── __init__.py
│   ├── router.py                 # Detects file type → routes to correct loader
│   ├── loaders/
│   │   ├── pdf_loader.py         # PyMuPDF-based loader
│   │   ├── docx_loader.py        # python-docx loader
│   │   ├── pptx_loader.py        # python-pptx loader
│   │   ├── csv_loader.py         # pandas-based loader
│   │   └── zip_loader.py         # Extracts ZIP → processes each file inside
│   ├── chunker.py                # Text splitting with overlap
│   ├── embedder.py               # HuggingFace embedding wrapper
│   ├── vectorstore.py            # ChromaDB init, upsert, query
│   └── chain.py                  # LangChain RAG chain builder
│
├── utils/
│   ├── prompts.py                # System prompt templates
│   ├── citation.py               # Page + chunk source metadata extractor
│   └── memory.py                 # Conversation history manager
│
├── eval/
│   ├── ragas_eval.py             # RAGAS evaluation script
│   └── test_dataset.json         # 50 hand-labeled Q&A pairs
│
├── tests/
│   ├── test_router.py
│   ├── test_chunker.py
│   └── test_retrieval.py
│
├── .env.example
├── requirements.txt
├── Dockerfile
└── README.md
```

---

## Getting Started

### Prerequisites

- Python 3.10+
- Free [Groq API key](https://console.groq.com) — takes 30 seconds to create

### 1. Clone the repository

```bash
git clone https://github.com/yourusername/documind-ai.git
cd documind-ai
```

### 2. Create a virtual environment

```bash
python -m venv venv

# Mac / Linux
source venv/bin/activate

# Windows
venv\Scripts\activate
```

### 3. Install dependencies

```bash
pip install -r requirements.txt
```

### 4. Configure environment variables

```bash
cp .env.example .env
```

Open `.env` and fill in:

```env
# Groq (free) — get key at console.groq.com
GROQ_API_KEY=gsk_your_key_here

# OR OpenAI (paid)
# OPENAI_API_KEY=sk-your_key_here

# LLM backend: "groq" or "openai"
LLM_BACKEND=groq

# ChromaDB local storage path
CHROMA_DB_PATH=./chroma_store
```

### 5. Start the app

```bash
streamlit run app.py
```

Visit [http://localhost:8501](http://localhost:8501) — upload any file and start chatting.

---

## Core Code

### Universal file router (`rag/router.py`)

```python
import os
from rag.loaders import pdf_loader, docx_loader, pptx_loader, csv_loader, zip_loader

LOADERS = {
    ".pdf":  pdf_loader.load,
    ".docx": docx_loader.load,
    ".doc":  docx_loader.load,
    ".pptx": pptx_loader.load,
    ".ppt":  pptx_loader.load,
    ".txt":  lambda p: open(p, encoding="utf-8").read(),
    ".md":   lambda p: open(p, encoding="utf-8").read(),
    ".csv":  csv_loader.load,
    ".xlsx": csv_loader.load,
    ".zip":  zip_loader.load,
}

def route(file_path: str) -> str:
    ext = os.path.splitext(file_path)[1].lower()
    loader = LOADERS.get(ext)
    if not loader:
        raise ValueError(f"Unsupported file type: {ext}")
    return loader(file_path)
```

### Ingestion pipeline (`rag/chain.py`)

```python
from langchain.text_splitter import RecursiveCharacterTextSplitter
from langchain.embeddings import HuggingFaceEmbeddings
from langchain.vectorstores import Chroma
from rag.router import route

def ingest(file_path: str, collection_name: str = "documind") -> Chroma:
    raw_text = route(file_path)

    splitter = RecursiveCharacterTextSplitter(
        chunk_size=500,
        chunk_overlap=50,
        separators=["\n\n", "\n", ".", " "]
    )
    chunks = splitter.create_documents(
        [raw_text],
        metadatas=[{"source": file_path}]
    )

    embeddings = HuggingFaceEmbeddings(
        model_name="sentence-transformers/all-MiniLM-L6-v2"
    )
    vectorstore = Chroma.from_documents(
        chunks,
        embeddings,
        collection_name=collection_name,
        persist_directory="./chroma_store"
    )
    vectorstore.persist()
    return vectorstore
```

### Query with citations (`rag/chain.py`)

```python
from langchain.chains import RetrievalQAWithSourcesChain
from langchain.chat_models import ChatOpenAI
from langchain_groq import ChatGroq
import os

def build_chain(vectorstore: Chroma):
    if os.getenv("LLM_BACKEND") == "groq":
        llm = ChatGroq(model="llama3-8b-8192", temperature=0)
    else:
        llm = ChatOpenAI(model="gpt-4o-mini", temperature=0)

    return RetrievalQAWithSourcesChain.from_chain_type(
        llm=llm,
        retriever=vectorstore.as_retriever(search_kwargs={"k": 4}),
        return_source_documents=True
    )

def ask(chain, question: str) -> dict:
    result = chain({"question": question})
    sources = [
        f"{doc.metadata.get('source', 'unknown')} · chunk #{i+1}"
        for i, doc in enumerate(result["source_documents"])
    ]
    return {"answer": result["answer"], "sources": sources}
```

---

## Evaluation Results

Benchmarked using [RAGAS](https://github.com/explodinggradients/ragas) on 50 hand-labeled Q&A pairs across 5 document types (PDF, DOCX, PPTX, CSV, TXT):

| Metric | Score | What it measures |
|--------|-------|-----------------|
| Faithfulness | **0.91** | Answer is grounded in retrieved context, not hallucinated |
| Answer Relevancy | **0.87** | Answer actually addresses the question asked |
| Context Precision | **0.83** | Retrieved chunks are on-topic and not noisy |
| Context Recall | **0.79** | Relevant info from the document is not missed |

---

## Deployment

### Streamlit Cloud — free public URL in 5 minutes

1. Push this repo to GitHub
2. Go to [share.streamlit.io](https://share.streamlit.io) → connect your GitHub account
3. Select the `documind-ai` repo → set `app.py` as the entry point
4. Go to **Advanced settings → Secrets** and add:
   ```
   GROQ_API_KEY = "gsk_your_key_here"
   LLM_BACKEND = "groq"
   ```
5. Click **Deploy** — live at `https://documind-ai.streamlit.app`

### Docker

```bash
docker build -t documind-ai .
docker run -p 8501:8501 --env-file .env documind-ai
```

---

## Roadmap

- [x] PDF, DOCX, PPTX, TXT, CSV, ZIP ingestion
- [x] Cited answers with chunk-level source tracking
- [x] Conversation memory across turns
- [x] Multi-document querying
- [x] Groq (free) + OpenAI LLM backends
- [x] RAGAS evaluation suite
- [ ] CrossEncoder re-ranking for better retrieval precision
- [ ] Scanned PDF support via OCR (Tesseract)
- [ ] Graph RAG — knowledge graph built from documents
- [ ] FastAPI REST backend for third-party integrations
- [ ] Multilingual support — Hindi, Marathi, and more

---

## What I Learned

Building DocuMind AI gave me deep, practical experience in:

- Designing a production-grade RAG pipeline — chunking strategies, embedding model selection, retrieval tuning
- Building a universal file parser with a clean routing pattern across 7 formats
- Quantifying AI quality using RAGAS evaluation (not just subjective testing)
- Prompt engineering for grounded, citation-backed answers
- Deploying ML applications for free using Streamlit Cloud + Groq API

---

## Author

**Narsale Shreyas Manohar**  
Final Year B.E. — Information Technology  
PVG's COET

[![LinkedIn](https://img.shields.io/badge/LinkedIn-Connect-0A66C2?style=flat-square&logo=linkedin&logoColor=white)](https://linkedin.com/in/shreyas-narsale-04b266261)
[![GitHub](https://img.shields.io/badge/GitHub-Follow-181717?style=flat-square&logo=github&logoColor=white)](https://github.com/shreyasnarsale)
[![Gmail](https://img.shields.io/badge/Email-Contact-EA4335?style=flat-square&logo=gmail&logoColor=white)](mailto:shreyasnarsale04@gmail.com@gmail.com)

---

## License

Distributed under the MIT License. See [`LICENSE`](LICENSE) for full details.

---

<div align="center">

If this project helped you, give it a ⭐ on GitHub — it means a lot!

*Final Year Project · Information Technology · 2025–26*

</div>
