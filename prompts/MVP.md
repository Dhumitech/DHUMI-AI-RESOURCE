# Example MVP Plan: Local RAG Second Brain

This is an example implementation plan, not a universal template. Use `TEMPLATE_PROMPT.md`, `PRD.md`, or `ARD.md` when creating a new prompt from scratch.

## Goal

Build an LLM-based RAG app that lets users upload documents and ask natural-language questions over their own knowledge base.

## MVP Scope

A local "second brain" application using Retrieval-Augmented Generation. Users can upload files, index their contents, and query the knowledge base through a simple UI.

## Suggested Stack

| Layer | Default | Reason |
|---|---|---|
| Frontend | Streamlit | Fast MVP UI for upload and query workflows |
| Backend | FastAPI | Clear API boundaries for upload, indexing, and search |
| Local LLM | LM Studio-compatible endpoint | Keeps the MVP local and beginner-friendly |
| Metadata store | SQLite | Simpler than a full database server for MVP |
| Vector index | Chroma, FAISS, or local file-backed store | Keeps retrieval local and inspectable |

## Core Features

1. File upload
   - Accept PDF and text files.
   - Extract text and metadata.
   - Reject unsupported formats with clear errors.

2. Indexing
   - Split extracted text into chunks.
   - Generate embeddings.
   - Store chunks, embeddings, and document metadata.

3. Search and answer
   - Accept a natural-language query.
   - Retrieve the most relevant chunks.
   - Generate an answer grounded in retrieved context.
   - Show citations or source chunk previews.

4. Document list
   - Show uploaded documents.
   - Display indexing status and basic metadata.

## Data Model

```python
class Document:
    id: str
    title: str
    source_type: str
    upload_time: str
    metadata: dict

class Chunk:
    id: str
    document_id: str
    text: str
    chunk_index: int
    embedding_id: str
```

## API Endpoints

| Method | Path | Purpose |
|---|---|---|
| `POST` | `/api/files/upload` | Upload and index documents |
| `GET` | `/api/files` | List uploaded documents |
| `GET` | `/api/files/{id}` | Fetch document metadata and status |
| `POST` | `/api/search` | Query the knowledge base |

## Quality Requirements

- Answers must cite retrieved chunks.
- Unsupported file types must return clear errors.
- Empty or failed extraction must be visible to the user.
- The app should not send documents to external APIs unless explicitly configured.
- Logs should not store full private document text by default.

## Evaluation Plan

- Upload 5 representative documents.
- Ask 10 questions with known answers.
- Measure retrieval hit rate, answer groundedness, and citation accuracy.
- Include 3 negative tests where the answer is not in the documents.

## Future Enhancements

- Authentication and workspace sharing
- OCR for scanned PDFs
- DOCX and webpage ingestion
- Document version history
- Reranking
- Query filters
- Conversation history
- Deployment guide
