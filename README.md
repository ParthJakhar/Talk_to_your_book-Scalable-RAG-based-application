# LearningRAG

This workspace demonstrates a simple Retrieval-Augmented Generation (RAG) pipeline using Qdrant, LangChain, and Google's Gemini model, with a minimal React frontend.

## Backend

### Setup

1. Create a virtual environment and install dependencies:

```bash
python -m venv venv
venv\Scripts\activate  # Windows
pip install -r requirements.txt
```

The requirements file includes `sentence-transformers` (for embeddings), `langchain-huggingface` (updated embedding class) and the new `google-genai` SDK instead of the deprecated `google-generativeai`.

2. Ensure Qdrant is running locally (`docker-compose.yml` can be used).
3. Put your Gemini API key in `.env`:

```
gemini_API_Key=YOUR_KEY_HERE
```

### Running

```bash
python app.py
```

The server listens on `http://localhost:5000` and exposes:

- `POST /chat` with JSON `{ "query": "..." }` returns `{ "response": "..." }`.

The server prints a warning at startup if it cannot reach the Qdrant instance; you can still start the app but the `/chat` route will return a 503 until the vector database is available.

> ⚠️ The backend now uses the `google-genai` client (`genai.Client`) instead of the deprecated `google.generativeai`. It also uses the new `HuggingFaceEmbeddings` class from `langchain_huggingface`, so the `sentence-transformers` package is required.
## Frontend

A Vite + React app lives in the `client/` directory.

### Setup & run

```bash
cd client
npm install    # dependencies already installed by scaffolder
npm run dev
```

Open `http://localhost:5173` in your browser. Enter a question and press **Send**.

## Structure

- `app.py` – Flask server with retrieval and Gemini call.
- `chat.py` – original console-based helper (can still be used standalone).
- `client/` – React/Vite UI with minimal chat form.

---

