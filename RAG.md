Offline: indexing pipeline
Documents Raw sources -> Cleaning Remove noise -> Chunking Split into pieces -> Embedding Text to vectors -> Vector database Stores chunks + vectors 
 
 
Online: query pipeline -> User query Plus chat history -> Retrieval Top-k similarity -> Re-ranking Best few kept -> Generation LLM answers


Evaluation and monitoring Watches both phases

Questions
RAG - If your retriever is missing obvious matches, what's one thing you'd check first?