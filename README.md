# MultiDocChat

MultiDocChat is a simple **multi-document RAG application** built with **FastAPI, LangChain, Google Gemini, and FAISS**.

It allows a user to upload documents and then ask questions about their content through a web chat interface.

## How It Works

The application follows a basic RAG pipeline:

1. **Upload documents**
   - Supported formats: PDF, DOCX, TXT
   - Files are stored in a session-specific folder.

2. **Process documents**
   - Text is extracted from the uploaded files.
   - Documents are split into smaller chunks.

3. **Create embeddings**
   - Each chunk is converted into a vector using `gemini-embedding-001`.

4. **Store vectors**
   - Embeddings are stored in a local **FAISS** vector index.

5. **Ask questions**
   - The user's question is processed using the conversation history.
   - Relevant chunks are retrieved using **MMR (Maximal Marginal Relevance)**.

6. **Generate the answer**
   - The retrieved context is sent to the configured LLM.
   - The model answers using the retrieved document context.
   - If the information is not available, the prompt instructs the model to return **I don't know.**

## Architecture

```text
User
 |
 | Upload documents
 v
FastAPI
 |
 v
Document Loader
 |
 v
Text Chunking
 |
 v
Gemini Embeddings
 |
 v
FAISS Vector Store
 |
 | User Question
 v
Question Reformulation
 |
 v
MMR Retrieval
 |
 v
LLM (Gemini / Groq)
 |
 v
Answer
```

## Project Structure

```text
AI-Powered-Multi-Document-RAG-Platform-LLMOps/
│
├── main.py
├── multi_doc_chat/
│   ├── config/
│   ├── exception/
│   ├── logger/
│   ├── model/
│   ├── prompts/
│   ├── src/
│   │   ├── document_ingestion/
│   │   └── document_chat/
│   └── utils/
│
├── templates/
│   └── index.html
│
├── static/
│   └── styles.css
│
├── tests/
├── notebook/
├── run_evaluations.py
├── Dockerfile
└── Jenkinsfile.test
```

## Technologies

- Python 3.12+
- FastAPI
- LangChain
- Google Gemini
- FAISS
- Groq
- Jinja2
- pytest
- LangSmith
- Docker
- Jenkins

## Requirements

Create a `.env` file in the project root and configure the API keys used by the project.

Example:

```env
GOOGLE_API_KEY=your_google_api_key
GROQ_API_KEY=your_groq_api_key
LLM_PROVIDER=google
```

For LangSmith evaluation:

```env
LANGSMITH_API_KEY=your_langsmith_api_key
```

## Installation

Move into the project directory:

```bash
cd AI-Powered-Multi-Document-RAG-Platform-LLMOps
```

Install dependencies:

```bash
pip install -r requirements.txt
```

## Run the Application

Start the FastAPI server:

```bash
uvicorn main:app --reload
```

Then open:

```text
http://localhost:8000
```

## API Endpoints

| Method | Endpoint | Description |
|---|---|---|
| GET | `/` | Web application |
| GET | `/health` | Health check |
| POST | `/upload` | Upload and index documents |
| POST | `/chat` | Ask questions about uploaded documents |

## Evaluation

The project includes a LangSmith evaluation script:

```bash
python run_evaluations.py
```

Run all evaluators:

```bash
python run_evaluations.py --evaluator all
```

Example with custom retrieval parameters:

```bash
python run_evaluations.py --evaluator correctness --chunk-size 500 --k 10
```

## Testing

Run the test suite with:

```bash
pytest tests/ -v
```

## Docker

Build the image:

```bash
docker build -t multidocchat .
```

Run the container:

```bash
docker run -p 8080:8080 multidocchat
```

Then open:

```text
http://localhost:8080
```

## Notes

- The current application supports PDF, DOCX, and TXT ingestion.
- Chat history is stored in memory and is cleared when the server restarts.
- FAISS indexes are stored locally per session.
- The project includes evaluation and CI/CD files for LLMOps workflows.
