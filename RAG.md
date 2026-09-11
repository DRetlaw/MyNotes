Offline: indexing pipeline
Documents Raw sources -> Cleaning Remove noise -> Chunking Split into pieces -> Embedding Text to vectors -> Vector database Stores chunks + vectors 
 
 
Online: query pipeline -> User query Plus chat history -> Retrieval Top-k similarity -> Re-ranking Best few kept -> Generation LLM answers


Chunking strategies and overlap
Semantic chunking, or chunking by document structure like headers and sections

Evaluation and monitoring Watches both phases

Questions
RAG - If your retriever is missing obvious matches, what's one thing you'd check first?

What is cross-encoder?
reranking is exactly the right lever, using a cross-encoder to re-score the top candidates from your initial retrieval is a genuine best practice.