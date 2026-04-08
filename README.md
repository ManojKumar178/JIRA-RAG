# JIRA-RAG — Multi-Modal RAG System for Jira Intelligence

A production-grade RAG (Retrieval-Augmented Generation) system that delivers intelligent, context-aware answers over Jira data and mixed-format attachments. Built with a Django + FastAPI hybrid architecture for scalability, clean separation of concerns, and high-performance AI processing.

---

## Overview

Most Jira knowledge is buried — in ticket descriptions, comments, PDFs, images, and Word docs. This system makes it queryable using natural language.

It combines **hybrid search** (vector + keyword) with **LLM-powered answering** to retrieve relevant context from Jira entities and attachments, then generate accurate, grounded responses.

---

## Architecture

```
React (User Query)
      ↓
Django (API Gateway & Middleware)
      ↓
FastAPI (Multi-Modal RAG Engine)
      ↓
Weaviate (Vector DB) + Jira REST API + Document Store
      ↓
OpenAI (Embeddings + LLM Answering)
      ↓
Django → React (Response)
```

**Django** acts as the single entry point — handles auth, validation, request forwarding, and response normalization. Frontend never talks to FastAPI directly.

**FastAPI** handles all heavy async workloads — RAG pipeline execution, Jira integration, document processing, vector search, and LLM inference.

---

## Key Features

- **Hybrid Search** — Combines vector similarity and keyword search for more accurate retrieval
- **Multi-Modal Ingestion** — Processes Jira tickets, PDFs, images, Word docs, and metadata
- **Jira Integration** — Fetches issues, projects, sprints, and attachments via Jira REST API
- **LLM-Powered Q&A** — OpenAI models for embeddings and answer generation (split for cost efficiency)
- **Decoupled Architecture** — Django gateway keeps frontend backend-agnostic and secure
- **Async Pipeline** — FastAPI handles concurrent AI workloads without blocking

---

## Tech Stack

| Layer | Tech |
|---|---|
| Frontend | React |
| API Gateway | Django REST Framework |
| AI Engine | FastAPI |
| RAG Framework | LangChain |
| Vector Database | Weaviate (tested: Pinecone, ChromaDB) |
| Embeddings | OpenAI Embeddings API |
| LLM | OpenAI GPT (via LangChain) |
| Jira Integration | Jira REST API |
| Document Processing | PyMuPDF, python-docx, Pillow |
| Data | Pandas, NumPy |

---

## API Endpoints

### `POST /api/jira-data/`
Fetches and processes Jira insights.

**Flow:** React → Django → FastAPI → Jira REST API → Django → React

FastAPI handles Jira auth, issue/sprint fetching, and data transformation.

### `POST /api/rag-query/`
Handles natural language queries over Jira data and attachments.

**Flow:** React → Django → FastAPI (RAG pipeline) → Django → React

FastAPI executes retrieval, context enrichment, and LLM inference.

---

## RAG Pipeline

```
User Query
    ↓
Query Embedding (OpenAI)
    ↓
Hybrid Search — Vector + Keyword (Weaviate)
    ↓
Context Retrieval — Jira tickets, PDFs, images, docs
    ↓
Prompt Construction + LLM Inference (OpenAI)
    ↓
Grounded Answer
```

---

## Setup & Installation

```bash
# Clone the repo
git clone https://github.com/ManojKumar178/JIRA-RAG.git
cd JIRA-RAG

# Install Python dependencies
pip install -r requirements.txt

# Set environment variables
cp .env.example .env
# Add: OPENAI_API_KEY, JIRA_API_TOKEN, WEAVIATE_URL

# Run Django gateway
python manage.py runserver

# Run FastAPI AI engine
uvicorn app.main:app --reload --port 8001
```

---

## Design Decisions

**Why Django + FastAPI together?**
Django's maturity handles auth, middleware, and API stability well. FastAPI's async nature is better suited for LangChain chains and concurrent LLM calls. Splitting them keeps each optimized for its job.

**Why split embedding and answering models?**
Using a lightweight model for embeddings and a capable model for answering cuts API costs significantly while maintaining response quality.

**Why hybrid search over pure vector search?**
Pure vector search missed exact Jira ticket IDs and keywords. Hybrid search combines semantic understanding with keyword precision — better for Jira-style queries.

---

## Author

**Manoj Kumar Anamdasu**
[LinkedIn](https://www.linkedin.com/in/manoj-kumar-anamdasu/) | [Portfolio](https://manoj-anamdasu.netlify.app/) | [GitHub](https://github.com/ManojKumar178/)
