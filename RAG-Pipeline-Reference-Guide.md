# The RAG Pipeline: A Complete Reference Guide

A stage-by-stage walkthrough of Retrieval-Augmented Generation (RAG), covering data prep, chunking, embedding, indexing, storage, retrieval, re-ranking, and generation — with model/tool comparisons and notes on where each stage runs (local vs. remote).

---

## Pipeline overview

```
1. Data prep  →  2. Chunking  →  3. Embedding
                                        ↓
6. Re-rank  ←  5. Retrieve  ←  4. Index & store (vector database)
      ↓
7. Context assembly  →  8. LLM generation
```

---

## Stage 1: Data (sources and collection)

This is your knowledge base before anything happens to it. Typical sources: PDFs, Word docs, Confluence/Notion pages, HTML/web pages, databases, CSVs, Slack exports, code repos, transcripts.

**Where this runs:** Local — you're pulling files from your own systems.

## Stage 2: Data filtering

Cleaning up content before you embed it. Garbage in the index means garbage retrieved forever.

- **Deduplication** — near-duplicate pages/paragraphs waste index space and crowd out better chunks.
- **Boilerplate removal** — nav bars, footers, legal disclaimers, repeated headers.
- **Quality/relevance filtering** — drop stubs, broken exports, outdated document versions.
- **PII/sensitive data scrubbing** — redact or gate content that shouldn't be broadly retrievable.
- **Access control tagging** — tag chunks with source permissions now, so retrieval can filter by who's allowed to see what later.

**Where this runs:** Local (rule-based filtering, regex, dedup). Optional LLM-based quality judging can be local or remote.

## Stage 3: Data formatting / parsing

Converting heterogeneous formats into clean, structured text: PDFs (text/table extraction, OCR for scans), HTML (strip tags, keep headings/lists), tables (serialize to markdown/JSON). Preserve metadata — source URL, title, section headers, page numbers, timestamps — for citations and filtering later.

**Where this runs:** Local, usually via libraries (unstructured.io, PyMuPDF, BeautifulSoup) or a local layout model. OCR can be local (Tesseract) or a remote API.

## Stage 4: Chunking

Splitting documents into passages small enough to embed meaningfully, large enough to preserve context. See the deep-dive section below for full details.

### Tokens

A token is the unit models actually process — roughly ¾ of a word in English. Token counts matter for chunk size limits, embedding model input limits, and LLM context window budget. Tokenizers differ across model providers, so "500 tokens" isn't identical everywhere.

### Chunking strategies at a glance

| Strategy | How it works | Pros | Cons | Best for |
|---|---|---|---|---|
| Fixed-size | Split every N tokens, often with overlap | Simple, fast, predictable | Cuts sentences/ideas mid-thought | Quick prototypes |
| Recursive/sentence-aware | Split on paragraph → sentence boundaries | Respects language boundaries | Slightly more complex | General-purpose default |
| Semantic chunking | Split where meaning shifts (embedding similarity) | Topically coherent chunks | Slower, more compute | Long-form technical/legal docs |
| Document-structure-aware | Split on headings/markdown structure | Preserves logical hierarchy | Needs structured source docs | Wikis, manuals |
| Sliding window w/ overlap | Fixed-size chunks overlapping 10–20% | Reduces boundary information loss | More chunks, more storage | Context continuity |
| Agentic/LLM-based | LLM decides chunk boundaries | Highest quality boundaries | Expensive, slow | High-value, small corpora |
| Parent-child (small-to-big) | Embed small, retrieve large parent | Precise search + full context | More indexing complexity | Production systems |

## Stage 5: Embedding models

Converts text into vector embeddings — lists of numbers representing meaning, so similarity is computable via cosine similarity, dot product, or Euclidean distance.

| Model | Type | Access | Approx. cost | Context | Notes |
|---|---|---|---|---|---|
| OpenAI text-embedding-3-large | Closed | API | ~$0.13/1M tokens | 8,192 tokens | Strong general default |
| OpenAI text-embedding-3-small | Closed | API | ~$0.02/1M tokens | 8,192 tokens | Cheapest good option |
| Google gemini-embedding-001 | Closed | API | Cloud pricing | 2,048 tokens | Tops multilingual MTEB (early 2026) |
| Cohere Embed v4 | Closed | API | Cohere pricing | Long, multimodal | Pairs with Cohere Rerank |
| BGE-M3 (BAAI) | Open (Apache-2.0) | Self-host | GPU cost only | Long | Default open-source pick |
| Qwen3-Embedding (0.6B/4B/8B) | Open (Apache-2.0) | Self-host | GPU cost only | Long | Top open-weight MTEB scores |
| nomic-embed-text | Open | Self-host | Free | Standard | Lightweight, modest hardware |
| mxbai-embed-large | Open | Self-host | Free | Standard | Good English performance |

Run your own retrieval eval on a sample of your corpus before committing — MTEB scores don't always translate to your data. The same embedding model must be used at indexing time and query time; you can't mix models.

**Where this runs:** Remote (OpenAI, Google, Cohere APIs) or local/self-hosted (BGE, Qwen3-Embedding, nomic — needs a GPU).

## Stage 6: Indexing

Building a data structure over your vectors so similarity search is fast at scale. Dominant algorithm: **HNSW** (graph-based approximate nearest neighbor). Alternatives: **IVF** (cluster-then-search), and **quantization** (scalar/product/binary — compresses vectors, trades a little accuracy for memory savings at scale).

**Where this runs:** Wherever your vector database runs (local or remote).

## Stage 7: Storing — vector databases

| Database | Type | Hosting | Open source | Standout | Watch out for |
|---|---|---|---|---|---|
| Pinecone | Managed only | Cloud | No | Zero-ops, serverless | Costs climb above 1M vectors; lock-in |
| Qdrant | Open + managed | Self-host or cloud | Yes (Apache 2.0) | Best latency, strong filtering | Smaller ecosystem |
| Weaviate | Open + managed | Self-host or cloud | Yes | Built-in hybrid search | More to operate self-hosted |
| Milvus / Zilliz Cloud | Open + managed | Self-host or cloud | Yes (Apache 2.0) | Billion-scale | Heavier at small scale |
| pgvector | Postgres extension | Self-host/managed | Yes | No new infra | Millions, not billions, without tuning |
| Chroma | Embedded + managed | Local or cloud | Yes | Simplest to start | Less proven at large scale |
| LanceDB | Embedded + managed | Local or cloud | Yes | Runs in-process | Cloud offering less mature |

Quick picks: already on Postgres → **pgvector**. Lowest latency + heavy filtering, self-hosted → **Qdrant**. Zero ops, managed only → **Pinecone**. Billions of vectors → **Milvus/Zilliz**. Prototyping/local-first → **Chroma** or **LanceDB**.

## Stage 8: Retrieval

| Strategy | How it works | Strength | Weakness |
|---|---|---|---|
| Dense (vector) | Embedding similarity search | Semantic meaning, paraphrasing | Misses exact keyword matches |
| Sparse (BM25) | Term-frequency matching | Exact terms, jargon, IDs | Misses semantic similarity |
| Hybrid | Combines dense + sparse | Best of both; production standard | Needs a fusion method (e.g. RRF) |
| MMR | Relevance + redundancy penalty | Less duplication in results | Needs diversity-parameter tuning |
| Multi-query | LLM rewrites query several ways | Better recall on ambiguous questions | Extra LLM + retrieval calls |
| HyDE | LLM writes hypothetical answer, embeds that | Improves match on complex questions | Extra generation step |
| Parent-document | Search small, return large parent | Precise + full context | More index bookkeeping |
| Metadata filtering | Vector search + structured filters | Access control, freshness, scoping | Needs accurate metadata at ingestion |

**Where this runs:** Query embedding (local/remote, matching your indexed model). Search itself runs wherever the vector DB lives. Query rewriting/HyDE need an LLM call (local or remote).

## Stage 9: Re-ranking

A fast retrieval pass pulls back a wide net (top 20–100); a reranker (cross-encoder) scores query+document jointly and re-sorts for precision — more accurate than embeddings alone, but too slow to run over the whole corpus.

| Model | Access | Best for | Latency (approx.) |
|---|---|---|---|
| Cohere Rerank (v3.5/v4) | API, closed | Broadest language coverage | ~80–150ms |
| Voyage rerank-2.5 | API, closed | Domain-tuned (code/finance/legal) | ~90–180ms |
| BGE-reranker-v2-m3 | Open, self-host | Reliable open baseline | Depends on GPU |
| Qwen3-Reranker (0.6B–4B) | Open, self-host | Best open-weight quality | Depends on GPU |
| Jina Reranker v2/v3 | API + open weights | Long context, code | Fast on commodity GPU |
| mxbai-rerank-large-v2 | Open, self-host | Air-gapped/on-prem | Depends on GPU |

Rerankers typically lift answer accuracy 10–40% depending on first-pass retrieval quality — high ROI, but adds latency.

**Where this runs:** Remote (Cohere, Voyage) or local (BGE, Qwen3-Reranker, mxbai — on your own GPU).

## Stage 10: Context window and prompt assembly

Assembling system instructions, re-ranked chunks (with source metadata for citations), and the user's question into a single prompt. The context window caps how many tokens the LLM can accept, so typically only the top 3–10 reranked chunks are passed. Watch for the "lost in the middle" effect — LLMs attend more to the start and end of long contexts.

**Where this runs:** Local — pure prompt-construction in your application code, no model call.

## Stage 11: LLM response generation

The LLM reads the prompt and generates an answer grounded in the retrieved chunks, ideally with citations back to sources.

**Where this runs:** Local (self-hosted Llama, Qwen, Mistral) or remote (Claude, GPT, Gemini via API).

## Stages you might be missing

- **Query understanding/rewriting** — intent classification, typo correction, decomposing multi-part questions, routing (deciding if retrieval is needed at all).
- **Evaluation/observability** — retrieval metrics (recall@k, MRR, NDCG) and answer-quality metrics (faithfulness, relevance) — how you know a change actually helped.
- **Caching** — embeddings and full responses, to cut cost and latency on repeated queries.
- **Guardrails/grounding checks** — verifying the answer is actually supported by retrieved chunks, catching hallucination before it reaches the user.
- **Feedback loop** — logging retrieved/used chunks and user feedback to iteratively improve chunking, retrieval, and re-ranking.
- **Incremental updates/re-indexing** — handling new or changed documents without re-embedding the whole corpus (versioning, upsert, TTL invalidation).

## Local vs. remote, at a glance

| Stage | Local? | Remote? |
|---|---|---|
| Data collection/filtering/formatting | Yes (default) | Rare (cloud OCR) |
| Chunking (rule-based) | Yes | — |
| Chunking (semantic/agentic) | Yes | Yes |
| Embedding | Yes (BGE, Qwen3, nomic) | Yes (OpenAI, Google, Cohere) |
| Indexing/storing | Yes (Qdrant, Milvus, pgvector, Chroma, LanceDB) | Yes (Pinecone, managed clouds) |
| Retrieval (search) | Wherever the DB lives | Wherever the DB lives |
| Query rewriting/HyDE | Yes | Yes |
| Re-ranking | Yes (BGE, Qwen3-Reranker, mxbai) | Yes (Cohere, Voyage, Jina) |
| LLM generation | Yes (Llama, Qwen, Mistral) | Yes (Claude, GPT, Gemini) |

The pattern across every model-based stage: **local/self-hosted** gives full data control, no per-call cost, but requires GPU infra and ops work. **Remote/API** gives zero infrastructure and often top-tier quality, but sends data to a third party and costs scale with usage.

---

*Compiled September 2026. Model names, pricing, and benchmark leaders shift quickly — verify current details (MTEB leaderboard, provider pricing pages) before making final selections.*
