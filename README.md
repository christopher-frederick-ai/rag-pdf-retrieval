# RAG Pipeline: Question Answering Grounded in Your Documents

A retrieval-augmented generation (RAG) pipeline built from scratch. It loads PDFs, splits them into chunks, converts the chunks to vector embeddings, stores them in a searchable index, and answers a plain-English question using only the most relevant passages.

Built during the Virginia Tech Agentic AI System Design program as a hands-on exercise. It runs on sample public documents and is a reference build, not client work.

## Why it matters

A language model on its own answers from memory and can invent details. This pipeline instead retrieves the relevant passages from your own documents first, then answers from that text and says so when the documents do not contain the answer. That makes answers easier to check against a source.

## How it works

| Stage | Tool |
|---|---|
| PDF loading | PyPDFLoader (LangChain) |
| Chunking | RecursiveCharacterTextSplitter, 1000-character chunks with 150-character overlap |
| Embeddings | Voyage AI (`voyage-4`, 1024 dimensions) |
| Vector store | FAISS |
| Answer generation | Claude, told to answer only from the retrieved context |

The course assignment specified Azure OpenAI for embeddings and generation. This build swaps in Voyage AI and Claude and keeps the same two-model design: one model to embed, one to generate.

## Sample

**Question:** "What are the key risks associated with deploying AI systems?"

The pipeline retrieves the three most relevant chunks, all from the NIST AI Risk Management Framework, and answers from them. The answer covers risk measurement in real-world settings, inscrutability, difficulty setting human baselines, overlapping software risks, data-related risks, scale and complexity, and pre-trained model risks.

## Run it

1. Add the source PDFs to `data/` (see `data/README.md`).
2. Copy `.env.example` to `.env` and add your Anthropic and Voyage AI keys. The `.env` file is ignored by Git and should never be committed.
3. Install the dependencies and run the notebook from top to bottom:

```
pip install -r requirements.txt
jupyter notebook rag_pipeline.ipynb
```

Running it builds the FAISS index locally in `faiss_index/`.

## Notes and limits

- The code uses the `langchain-community` FAISS wrapper, which the LangChain project has said is being phased out. It works today, but a longer-lived version should move to the dedicated FAISS integration package.
- Retrieval is plain similarity search over the top 3 chunks. There is no reranking, no evaluation of answer quality, and no handling of tables or scanned pages.
- The three sample PDFs cover unrelated topics (AI risk, web security, and a hardware product), so a question about AI risk should retrieve from the NIST document and not the others.
