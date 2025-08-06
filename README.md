# Private AI Assistant - Secure Internal Data Querying (Server Only)

This is the **server component** of a Private AI Assistant built for companies to **securely query internal documents** using state-of-the-art RAG (Retrieval-Augmented Generation) architecture. Inspired by the ASTRA project, this version is optimized for **enterprise data security**, semantic search, and LLM-based responses from private knowledge bases.

---

## Tech Stack

- **Python 3.10+**
- **Gemini API** (or pluggable with OpenAI, Mistral, etc.)
- **FastAPI** (or Flask) – Web API Server
- **PostgreSQL** – Embedding + Q&A cache DB (optional)
- **Pickle / FAISS** – For embedding index persistence
- **SentenceTransformers** – For semantic search (if not using Gemini embedding)
- **dotenv** – Secure API key management
- **Uvicorn** – ASGI server
- **Langchain** (optional) – For advanced RAG pipelines

---

## Directory Structure
``` plaintext
private_ai_assistant/
├── client/ # Optional (not included here)
├── data/ # Internal company documents (.txt, .pdf, etc.)
│ ├── handbook.txt
│ └── compliance.pdf
├── logs/
│ └── query_log.jsonl # Logs of user queries and responses
├── server/
│ ├── init.py
│ ├── server.py # FastAPI server routes
│ ├── gemini_api.py # Handles Gemini API communication
│ ├── semantic_search.py # Embedding + cosine similarity logic
│ ├── gemini_embedding.py # Embedding generation via Gemini
│ ├── notion.py # (Optional) Notion page fetching
│ ├── pdf_reader.py # Utility to extract text from PDFs
│ ├── logger.py # JSONL logger
│ ├── z_generating_pickle.py # One-time embedding generation from /data
│ └── z_test.py # Testing file for server functions
├── embedding_index.pkl # Serialized embedding index
├── requirements.txt
├── .env # Contains GEMINI_API_KEY and other secrets
└── README.md
```



## Workflow

1. **Document Ingestion**  
   - Text/PDF files are stored in `/data`.
   - `z_generating_pickle.py` reads these, generates embeddings using Gemini API, and stores them in `embedding_index.pkl`.

2. **User Query Handling**
   - A POST request to `/ask` is received with a user query.
   - Embeddings of the query are generated.
   - Cosine similarity is computed against `embedding_index.pkl`.
   - Top-K relevant chunks are retrieved.
   - Gemini API is called with the query + context to generate a secure, accurate response.
   - Query and response are logged in `/logs/query_log.jsonl`.

3. **PDF Upload**
   - Endpoint `/upload` allows uploading new PDFs.
   - The file is parsed, embedded, and dynamically added to the existing index.

---

## Features

- **Private & Secure**: No public model training or exposure of sensitive data.
- **Multi-format Support**: PDF, TXT, and more supported.
- **Semantic Search**: Accurate context retrieval using embeddings.
- **Pluggable LLM/Embedder**: Easily switch between Gemini, OpenAI, or local models.
- **Query Logging**: All interactions stored securely for auditing.
- **LLM-Powered Q&A**: Relevant, context-aware answers using retrieved data.

---

## Setup Instructions

### 1. Clone the Repository

```bash
git clone https://github.com/your-org/private-ai-assistant.git
cd private-ai-assistant/server
```
2. Create Environment File
```bash
touch ../.env
echo "GEMINI_API_KEY=your_gemini_key_here" >> ../.env
```
3. Install Dependencies
```bash
pip install -r ../requirements.txt
```
4. Generate Embedding Index
```bash
python z_generating_pickle.py
```
5. Start the Server
```bash
uvicorn server:app --reload
```
#API Endpoints
1. Visit Swagger Ui
```bash
http://<url>:<port>/docs
```