# Knowledge Workspace RAG

> A polished document intelligence workspace that turns your PDFs, DOCX files, and text documents into grounded, conversational answers.

![Python](https://img.shields.io/badge/Python-3.11-3776AB?logo=python&logoColor=white)
![FastAPI](https://img.shields.io/badge/API-FastAPI-009688?logo=fastapi&logoColor=white)
![Streamlit](https://img.shields.io/badge/UI-Streamlit-FF4B4B?logo=streamlit&logoColor=white)
![ChromaDB](https://img.shields.io/badge/Vector%20Store-ChromaDB-6C47FF)
![Gemini](https://img.shields.io/badge/Model-Google%20Gemini-4285F4?logo=google&logoColor=white)

## What it does

Knowledge Workspace RAG is a local RAG application with two parts:

1. **Streamlit frontend** — upload documents, track indexed files, and ask questions in a conversational workspace.
2. **FastAPI backend** — chunk documents, create Gemini embeddings, retrieve relevant context from ChromaDB, and generate grounded Gemini responses.

The assistant is instructed to answer from retrieved document context and say when the answer is not available in the uploaded knowledge base.

## Features

- Upload `.pdf`, `.docx`, and `.txt` files
- Automatic text extraction and chunking
- Gemini-powered document embeddings
- Persistent ChromaDB vector storage
- Semantic retrieval of relevant passages
- Context-grounded Gemini answers
- FastAPI health, ingestion, collection, and chat endpoints
- Dark, responsive Streamlit workspace UI

## Architecture

```text
┌──────────────────────┐
│  Streamlit frontend  │
│  Uploads + chat UI   │
└──────────┬───────────┘
           │ HTTP
           ▼
┌──────────────────────┐
│   FastAPI backend    │
│  Ingestion + chat    │
└───────┬────────┬─────┘
        │        │
        ▼        ▼
┌────────────┐  ┌─────────────────┐
│ ChromaDB   │  │ Google Gemini   │
│ Retrieval  │  │ Embeddings + AI │
└────────────┘  └─────────────────┘
```

## Project structure

```text
knowledge-workspace-rag/
├── Backend/
│   ├── requirements.txt
│   └── myvenv/
│       ├── main.py
│       ├── database.py
│       ├── services.py
│       └── .env.example
├── Frontend/
│   ├── Frontend.py
│   └── chunking.py
├── .gitignore
└── README.md
```

The source files currently live in `Backend/myvenv` for compatibility with the existing local setup. The virtual environment internals, local Chroma data, and secrets are intentionally excluded from Git.

## Requirements

- Windows, macOS, or Linux
- Python 3.11+
- A Google Gemini API key

## Setup

From the project root:

```powershell
python -m venv Backend\myvenv
Backend\myvenv\Scripts\activate
python -m pip install -r Backend\requirements.txt
```

Create `Backend/myvenv/.env` from the included template:

```powershell
Copy-Item Backend\myvenv\.env.example Backend\myvenv\.env
```

Then set your key in `Backend/myvenv/.env`:

```env
GEMINI_API_KEY=your_gemini_api_key_here
```

Never commit `.env` or share your API key.

## Run the application

### 1. Start the FastAPI backend

Open a terminal at `Backend\myvenv`:

```powershell
cd Backend\myvenv
.\Scripts\uvicorn.exe main:app --reload
```

The API will be available at:

- API: http://127.0.0.1:8000
- Swagger docs: http://127.0.0.1:8000/docs

### 2. Start the Streamlit frontend

Open another terminal at the project root:

```powershell
Backend\myvenv\Scripts\streamlit.exe run Frontend\Frontend.py
```

The workspace will open at the local Streamlit URL shown in the terminal.

## API endpoints

| Method | Endpoint | Purpose |
|---|---|---|
| `GET` | `/` | Backend health check |
| `POST` | `/collection` | Add document chunks to ChromaDB |
| `GET` | `/collection` | Read stored collection data |
| `POST` | `/chat` | Retrieve context and generate an answer |

Example chat request:

```json
{
  "prompt": "Summarize the uploaded document"
}
```

## How the RAG flow works

1. The frontend extracts text from the uploaded file.
2. Text is split into overlapping chunks.
3. Chunks are sent to the FastAPI `/collection` endpoint.
4. Gemini embeddings are stored in ChromaDB.
5. A user question is sent to `/chat`.
6. ChromaDB retrieves the most relevant chunks.
7. Retrieved context is placed into a grounded prompt.
8. Gemini generates the final response.

## Troubleshooting

### API connection refused

Make sure the FastAPI backend is running on port `8000` before using the Streamlit chat.

### API key error

Confirm that `Backend/myvenv/.env` exists and contains:

```env
GEMINI_API_KEY=your_gemini_api_key_here
```

### Import errors

Run the application with the Python and executable files inside `Backend/myvenv`.

## Security notes

- API keys are loaded from `.env` and excluded by `.gitignore`.
- Local vector database files are excluded from Git.
- Do not upload sensitive documents to a shared or public deployment without adding authentication and access controls.

## License

Add a license before distributing this project publicly.
