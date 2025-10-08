# RAG Pipeline

Original code: [https://github.com/Infotrend-Inc/CoreAI-DemoProjects/tree/main/RAG_Pipeline](https://github.com/Infotrend-Inc/CoreAI-DemoProjects/tree/main/RAG_Pipeline)

## About

The `RAG_Pipeline` notebook is a self-contained demo that builds a **Retrieval-Augmented Generation (RAG)** workflow:

- it ingests a small document set, 
- chunks and embeds the text, 
- indexes it in a vector store, 
- and then answers user questions by retrieving the most relevant chunks and passing them, along with a carefully structured prompt, to an LLM to generate grounded answers with citations.

This notebook version was modified to use Ollama instead of [LiteLLM](https://www.litellm.ai/) to run with local models.

Used components:

- [Ragbits](https://ragbits.deepsense.ai/)
- [Docling](https://docling-project.github.io/docling/)
- [Chromadb](https://www.trychroma.com/)
- [Ollama](https://www.ollama.com/)

High-level steps:

1. Load documents (PDFs/Markdown/text) from a local data folder.
2. Split (“chunk”) the text into manageable pieces (by tokens, sentences, or paragraphs) to improve retrieval granularity.
3. Generate embeddings for each chunk using a text-embedding model.
4. Index the vectors in a vector database (here Chromadb).
5. Configure a search function to perform a semantic search method: takes a natural-language question, embeds it into a vector, searches the vector database, and returns the most relevant text chunks with similarity scores (the “retrieval” half of a RAG pipeline)
6. Build the prompt: assemble the user query + retrieved chunks into a system/instruction template that asks the model to cite sources and stay within context.
7. Generate an answer with an LLM, grounded by the retrieved context; display answer and source snippets/links.
