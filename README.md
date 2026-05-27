System Design: High-Performance RAG Architecture
Overview
This architecture was designed for the Gen AI Academy APAC challenge. The objective was to build a production-grade Retrieval-Augmented Generation (RAG) system that minimizes the latency-accuracy tradeoff—ensuring responses are both highly accurate and near-instant.

Architecture Design
Technical Stack & Logic
The system is built on a Modular Agentic Pipeline to allow for independent scaling of retrieval, ranking, and generation services.

Request & Caching Layer:

API Gateway: Manages traffic, rate-limiting, and security at the entry point.

Semantic Cache (Redis): Implements dual-layer caching. By checking for semantic matches in Redis before hitting the retrieval pipeline, we bypass the LLM entirely for frequent queries, significantly reducing latency and operational costs.

Retrieval & Intelligence Layer:

Orchestrator (LangGraph): Manages the stateful flow of the query, allowing for multi-hop reasoning.

Query Rewriter: Optimizes raw user inputs into search-ready queries to ensure better retrieval results.

Hybrid Retriever: Combines Dense Vector Search (for conceptual semantic understanding) and Sparse BM25 Keyword Search (for exact technical terminology matching).

Cross-Encoder Re-ranker: Acts as a quality filter. It re-scores the retrieved context chunks to ensure only the most relevant information is passed to the LLM, directly mitigating context-based hallucinations.

Generation & Evaluation Layer:

Prompt Assembler: Synthesizes the user query with the re-ranked context and system instructions.

LLM Inference (Gemini Flash): Selected for its high-speed inference capabilities and excellent instruction-following.

Monitoring/Evaluation: Continuously tracks "Faithfulness" and "Relevance" metrics via integrated monitoring, feeding data back into the orchestrator to improve retrieval parameters over time.

Why this design?
Latency: The use of Semantic Caching ensures the system remains responsive under high concurrent load.

Accuracy: By moving away from naive RAG and introducing a Re-ranker, we ensure the model is grounded in verified, highly relevant context.

Scalability: The decoupled, modular design allows individual components (like the vector database or LLM) to be upgraded or scaled without re-architecting the entire pipeline.
