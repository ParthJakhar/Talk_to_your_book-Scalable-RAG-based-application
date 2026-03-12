# Scalable Retrieval-Augmented Generation (RAG) Application

This repository contains a scalable RAG application built around a queue-based
architecture. The code lives primarily in the `rag_queue` package and demonstrates
how to decouple document ingestion, vector indexing, and query processing using
RQ (Redis Queue) workers.

## Features

- **Scalable design**: ingestion and chat workers operate independently and can
  be scaled horizontally.
- **Queue-driven processing** using RQ and Redis.
- **Vector store** backed by Qdrant (configured via `docker-compose.yml`).
- **Client-server separation**: the HTTP API is provided by `rag_queue.server`,
  while workers live in `rag_queue.worker` and `rag_queue.worker_runner`.
- **Simple Python-only stack** with minimal external dependencies.

## Repository structure

```
rag_queue/              # application package
    __init__.py
    main.py             # entrypoint for starting components
    server.py           # Flask/FastAPI (or similar) HTTP interface
    worker_runner.py    # helper to start RQ workers
    client/             # helpers for enqueuing jobs
        rq_client.py
    queues/             # job definitions
        __init__.py
        worker.py        # actual work performed by RQ workers
```

Other top‑level files include `requirements.txt`, `docker-compose.yml`, and this
README.

## Getting started

1. **Install dependencies**:

   ```sh
   python -m venv venv
   venv\\Scripts\\activate      # on Windows
   pip install -r requirements.txt
   ```

2. **Run Redis & Qdrant**:

   ```sh
   docker-compose up -d
   ```

3. **Configure environment**:

   Create a `.env` file with your API keys and other settings. For example:

   ```env
   GEMINI_API_KEY=your_key_here
   ```

4. **Start the server**:

   ```sh
   python -m rag_queue.server
   ```

   The HTTP API listens on `http://localhost:5000` by default. It exposes
   endpoints for submitting documents, triggering re‑indexing, and querying the
   RAG system.

5. **Run workers**:

   ```sh
   python -m rag_queue.worker_runner
   ```

   This command starts the RQ workers that process ingestion and chat jobs. You
   can run multiple instances of the runner on different machines to scale the
   workload.

## Usage

- **Ingest documents**: POST them to the server or use the Python client in
  `rag_queue.client.rq_client` to enqueue ingestion jobs.
- **Ask questions**: send a request to the `/chat` endpoint; the server will
  enqueue a query job and return the response once a worker processes it.

## Development tips

- Modify `rag_queue/queues/worker.py` to change how documents are embedded or
  how responses are generated.
- Adjust the RQ queue names in `worker_runner.py` when adding new job types.

## Deployment

- Use the provided `docker-compose.yml` for local development; in production you
  can swap Redis/Qdrant services with managed equivalents.
- Horizontal scaling is achieved by running additional `worker_runner` processes
  and/or copying the `server` container behind a load balancer.

---

For more details on the original prototype and architecture rationale, see the
previous README history in Git.
