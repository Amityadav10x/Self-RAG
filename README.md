# Self-RAG using LangGraph, Ollama & FAISS

A lightweight Self-RAG (Self-Reflective Retrieval-Augmented Generation) pipeline built using:

* LangGraph
* LangChain
* Ollama
* FAISS
* Local Embedding Models
* Adaptive Retrieval Logic

This project demonstrates how an LLM can decide whether external document retrieval is required before generating a response.

---

# Project Overview

Traditional RAG systems always retrieve documents before generating responses.

This project implements a **Self-RAG workflow** where the model first evaluates:

> “Do I need external documents to answer this question reliably?”

Based on that decision:

* Questions answerable from general knowledge are handled directly.
* Questions requiring company-specific or document-based information trigger retrieval.

The workflow is implemented using **LangGraph conditional routing**.

---

# Architecture

```text
                     ┌────────────────────┐
                    │  User Question     │
                    └─────────┬──────────┘
                              │
                              ▼
                  ┌────────────────────────┐
                  │ decide_retrieval()     │
                  │ LLM decides if RAG     │
                  │ is required            │
                  └─────────┬──────────────┘
                            │
           ┌────────────────┴────────────────┐
           │                                 │
           ▼                                 ▼
┌─────────────────────┐         ┌─────────────────────┐
│ generate_direct()  │         │ retrieve()          │
│ General knowledge  │         │ FAISS similarity    │
│ response           │         │ search              │
└─────────┬──────────┘         └─────────┬──────────┘
          │                              │
          │                              ▼
          │                 ┌────────────────────────┐
          │                 │ generate_rag()         │
          │                 │ Answer using retrieved │
          │                 │ context                │
          │                 └─────────┬──────────────┘
          │                           │
          └───────────────┬───────────┘
                          ▼
                   ┌─────────────┐
                   │ Final Answer│
                   └─────────────┘
```                

---

# Features

## Adaptive Retrieval

The LLM dynamically decides whether retrieval is necessary.

## Local LLM Support

Runs fully locally using Ollama.

## FAISS Vector Search

Efficient similarity search over document embeddings.

## LangGraph Workflow

Graph-based orchestration with conditional routing.

## PDF Document Support

Supports ingesting company PDFs and HR documents.

## Offline AI Stack

No external API dependency required.

---

# Tech Stack

| Technology                     | Purpose                |
| ------------------------------ | ---------------------- |
| LangChain                      | LLM framework          |
| LangGraph                      | Workflow orchestration |
| Ollama                         | Local LLM runtime      |
| FAISS                          | Vector database        |
| PyPDFLoader                    | PDF loading            |
| RecursiveCharacterTextSplitter | Chunking               |
| nomic-embed-text               | Embedding model        |
| Phi3                           | Local reasoning model  |

---

# Folder Structure

```text
Self-RAG/
│
├── documents/
│   ├── Hypothetical Company Hr Docs Rag Dataset.pdf
│   └── Nexora Hr Policy Handbook Separate.pdf
│
├── self_rag.ipynb
├── requirements.txt
├── README.md
└── .gitignore
```

---

# Installation

## 1. Clone Repository

```bash
git clone https://github.com/Amityadav10x/Self-RAG.git
cd Self-RAG
```

---

## 2. Create Virtual Environment

### Windows

```bash
python -m venv .venv
.venv\Scripts\activate
```

### Linux / Mac

```bash
python3 -m venv .venv
source .venv/bin/activate
```

---

## 3. Install Dependencies

```bash
pip install -r requirements.txt
```

---

# Ollama Setup

## Install Ollama

Visit:

[https://ollama.com](https://ollama.com)

---

## Pull Required Models

### Embedding Model

```bash
ollama pull nomic-embed-text
```

### LLM Model

```bash
ollama pull phi3
```

---

## Start Ollama

```bash
ollama serve
```

---

# How It Works

## Step 1 — Load PDFs

```python
from langchain_community.document_loaders import PyPDFLoader
```

PDFs are loaded into LangChain documents.

---

## Step 2 — Chunk Documents

Documents are split into smaller chunks for retrieval.

```python
RecursiveCharacterTextSplitter
```

---

## Step 3 — Create Embeddings

```python
OllamaEmbeddings(model="nomic-embed-text")
```

Embeddings are generated locally.

---

## Step 4 — Store in FAISS

```python
FAISS.from_documents(chunks, embeddings)
```

---

## Step 5 — Self Retrieval Decision

The LLM determines:

* Does this question require retrieval?
* Or can I answer directly?

---

## Step 6 — Conditional Routing

LangGraph routes execution:

* Direct generation path
* Retrieval + RAG generation path

---

# Example Queries

## General Knowledge Query

```text
What is Artificial Intelligence?
```

### Output Flow

```text
generate_direct()
```

---

## Company-Specific Query

```text
What is the leave policy at Nexora Technologies?
```

### Output Flow

```text
retrieve() → generate_rag()
```

---

# Example Output

```text
Nexora Technologies Pvt. Ltd. provides 12 casual leaves,
10 sick leaves, and 18 earned leaves annually.
Employees may carry forward up to 15 earned leave days.
```

---

# LangGraph Nodes

## decide_retrieval()

Determines whether document retrieval is required.

---

## generate_direct()

Generates responses using only model knowledge.

---

## retrieve()

Fetches relevant document chunks from FAISS.

---

## generate_rag()

Generates grounded answers using retrieved context.

---

# State Schema

```python
class State(TypedDict):
    question: str
    need_retrieval: bool
    docs: List[Document]
    answer: str
```

---

# Future Improvements

* Streaming responses
* Hybrid search (BM25 + Vector)
* Reranking models
* Multi-query retrieval
* Conversation memory
* Evaluation pipeline
* Hallucination detection
* Agentic workflows
* Web search integration

---

# Use Cases

* Internal company chatbots
* HR assistants
* Enterprise knowledge systems
* Customer support bots
* Policy Q&A systems
* AI copilots
* Local private AI systems

---

# Requirements

Example dependencies:

```text
langchain
langgraph
langchain-community
faiss-cpu
pypdf
ollama
ipython
```

---

# Running the Project

Launch Jupyter Notebook:

```bash
jupyter notebook
```

Open:

```text
self_rag.ipynb
```

Run all cells sequentially.

---

# Author

Amit Yadav

GitHub:

[https://github.com/Amityadav10x](https://github.com/Amityadav10x)

---

# License

This project is intended for learning and experimentation purposes.

---

# Acknowledgements

* LangChain
* LangGraph
* Ollama
* Meta AI research concepts around RAG
* Open-source AI community
