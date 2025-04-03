# LangGraph & LlamaIndex RAG with Local Models - Gemma3 via Ollama

This project implements a Retrieval-Augmented Generation (RAG) system using LangGraph for workflow orchestration and LlamaIndex for indexing and retrieval, powered by locally running language models via Ollama. It ingests data from PDF documents and answers questions based on their content.

## Workflow Architecture

The workflow processes queries through several steps, including parallel processing for efficiency:

1.  **Orchestrator**: Initiates the workflow and distributes the query.
2.  **Question Processing (Parallel)**:
    * **Question Conversion**: Reformulates the original question.
    * **Question Summary**: Creates a concise summary.
    * **Retrieval**: Finds relevant document chunks from a vector database built from PDFs.
3.  **RAG Generation**: Creates an answer based *only* on the retrieved document context.
4.  **Quality Evaluation**: Uses the LLM to rate the generated answer's quality and relevance. Loops back to generation if the quality is too low (up to a limit).
5.  **Synthesis**: Combines the results into a final, well-structured response.

## Key Components

* **Vector Store**: LlamaIndex builds and persists a vector index from PDF documents located in the `./data` directory.
* **LLM Integration**: Uses local LLMs (e.g., Gemma, Llama, Mistral) served via Ollama for all generation, conversion, summarization, and evaluation tasks.
* **Embedding Model Integration**: Uses a local embedding model served via Ollama (e.g., `nomic-embed-text`) for document indexing and retrieval.
* **LangGraph Orchestration**: Manages the flow, parallel execution, conditional logic (evaluation loop), and state.
* **PDF Ingestion**: Automatically parses PDF files using `llama-index` and `pypdf`.

## Prerequisites

1.  **Python:** Python 3.8 or higher.
2.  **Ollama:** Install Ollama from [https://ollama.com/](https://ollama.com/). Ensure the Ollama application or server is running.
3.  **Ollama Models:** Pull the required LLM and embedding models. Open your terminal and run:
    ```bash
    # Replace 'gemma3:12b' if you configure a different model
    ollama pull gemma3:12b
    ollama pull nomic-embed-text
    ```
4.  **(Optional) Graphviz:** For visualizing the workflow graph as a PNG, you need to install Graphviz.
    * **macOS:** `brew install graphviz`
    * **Debian/Ubuntu:** `sudo apt-get update && sudo apt-get install -y graphviz`
    * Then, install the Python library: `pip install pygraphviz`

## Setup

1.  **Clone the repository (or download the Notebook):**
    ```bash
    # git clone https://github.com/kelkalot/local-agentic-rag-gemma3
    ```
2.  **Create a Python virtual environment (Recommended):**
    ```bash
    python -m venv venv
    source venv/bin/activate  # On Windows use `venv\Scripts\activate`
    ```
3.  **Install dependencies:**
    ```bash
    pip install -r requirements.txt
    ```
4.  **Create Data Directory:** Create a folder named `data` in the root of the project directory.
    ```bash
    mkdir data
    ```
5.  **Add PDF(s):** Place the PDF file(s) you want to query inside the created `data` directory.

## Configuration

Modify the configuration variables at the top of the Notebook as needed:

* `LOCAL_LLM_MODEL`: The Ollama model tag for the main language model (e.g., `"gemma3:12b"`, `"llama3:8b"`, `"mistral:latest"`). Make sure you have pulled this model in Ollama.
* `LOCAL_EMBEDDING_MODEL`: The Ollama model tag for the embedding model (e.g., `"nomic-embed-text"`). Make sure you have pulled this model.
* `OLLAMA_BASE_URL`: The URL of your running Ollama instance (default is usually correct).
* `DATA_DIRECTORY`: The path to the directory containing your PDF files (defaults to `"data"`).
* `VECTOR_INDEX_PATH`: The path where the generated vector index will be stored (defaults to `"pdf_index_local"`).
