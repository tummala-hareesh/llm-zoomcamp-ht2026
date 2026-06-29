# Week-2 Notes - Vector Search 
- Extend the RAG pipeline with vector search 
- Vector search = matches documents by semantic meaning 
- Embeddings -> persistent vector index (sqlitesearch, PGVector), ONNX-based embedders for lightweight deployments. 

## What is Vector Search?
- Previously, keyword search with minsearch and sqlitesearch -> Matches exact words.
    - If searching for "Docker", Document should contain "Docker" for this to work. 
- Questions like:
    - Can I still join the course after the start date?
    - Is it possibel to enroll late?
    - Mean the same thing. Yet they share almost no words. 
    - Keyword engines struggle to match them. 
    - Need something that works on meaning, not on the eact words. 
- Vector Search -> matches ideas instead of words
- The vector search process:
    - 1. Offline (indexing): convert all documents into vectors and store them in an index. 
    - 2. Online (query): 
        - convert the user query into a vector with the same model 
        - find closest document vectors by similarity 
    - Embeddings model produces the vectors. -> model trained to capture meaning. 
    - So, similar meaning text = similar vectors 
- Measure how close the vectors are. Common one is cosine simlilary. 
- Cosine similarity measures the angles between two vectors:
    - 1. Vectors pointing in the same direction: similarity = 1
    - 2. Vectors at right angles: similarity close to 0
    - 3. Vectors pointing in opposite direction: similarity = -1
- Keyword search vs vector search 
    - Choosing the right search architecture depends on your data structure and user intent. Below is a comprehensive comparison of **Keyword Search** and **Vector Search**, followed by implementation advice.
- Comparison Matrix

| Feature / Aspect | Keyword Search | Vector Search |
| :--- | :--- | :--- |
| **Matching Logic** | Matches exact words | Matches meaning and intent |
| **Best Used For** | Specific terms, IDs, and names | Paraphrased questions and natural language |
| **Example Queries** | `"pandas dataframe"` | `"How do I work with tabular data?"` |
| **Underlying Technology** | Inverted index (e.g., BM25, TF-IDF) | Vector index (based on cosine similarity) |
| **Common Pitfalls** | Misses synonyms and paraphrases | Can miss exact term matches |
| **Operational Complexity** | Low | High |
| **Implementation Advice** | **Start here** as a default | Add later if worth the extra cost |

- Production Strategy 
    - 1. Start simple 
        - Don't start with vector search 
        - Deploy keyword search as your baseline 
        - Cheaper and faster to deploy, easier to maintain 
    - 2. Scale to vectors
        - Graduate to vector search only when text search fails to meet requirements. 
        - Ensure you can quantitatively justify the increased infra cost. 
    - 3. The Hybrid ideal 
        - 2 work together best. 
- Building vector search 
    - Build vector search with 3 tools 
        - 1. minsearch - in-memory vector search
        - 2. sqlitesearch - persistent vector search bu SQLite
        - 3. PGVector - vector search in PostgreSQL; runs on docker 

## Embeddings 
- Embeddings = Document -> Text -> Vectors -> Setup Vector Search 
- Embed text into a vector space 
- Word embeddings and sentence embeddings 
    - Idea from word2vec
    - Model learns from place words as points in multi-dimensional space. 
    - Words -> similar meaning -> closer to each other in multi-dim space. 
- 1200 documents -> each becomes a point in space 
- Model encodes the whole sentence, not words in isolation. 
- Embeddings model
    - `sentence-transformers`; 
    - open-source library for embeddings
    - local; no API cost.
    - Pytorch under the hood. 
- Choosing a model 
    - Sentence Transformers supports many models 
    - Choice depends on the task and language and resources 
    - Larger models are slower; for FAQs smaller model is enough.
    - Choice of models: `all-MiniLM-L6-v2`
        - 384-dimensional vectors 
        - Fast on CPU
        - Good quality for general English text
        - Uses cosine similarity
        - model = ~80MB; and tokenizer from HuggingFace. 
        - Tokenizer -> turns text into something the model can read. 
- Cosine similarity
    - cos(0) =1.0 = same direction (similar); Rare case
    - cos(90) = 0.0 = perpendicular (unrelated)
    - cost(180) = -1.0 = opposite direction (opposite meaning)

## Embedding out Dataset
- Download ingest.py from 1st week code
- Generate embeddings
- Go to... quickstart -vectorsearch.ipynb notebook 

## Vector Search 
- Scoring documents 
    - Matrix X with all documents embeddings 
- Best match 
- Top 5 results 

## Vector Search with minsearch
- vector search by hand 
    - embed the query 
    - compute dot products
    - found best matches
    - using argsport and matrix code gets old with time and can't filter by course
- use library that wraps all of this. 
- `minsearch`
    - small in-memory search library 
    - Has Text Search and Vector Search. 
    - Both classes
        - `fit` to index data
        - `search` to query 
        - `filter_dict` in `search` to filter by keyword
- Creating index 
```
from minsearch import VectorSearch 

vindex = VectorSearch(keyword_fields=["course"])
vindex.fit(X, documents)
```
- `X` is all embeddings and list of documents as payload
- `keyword_fields` paramter works the same as in the text `Index`; filter the course later
- Searching 
    - model encode 
    - vindex search 
- 

## RAG With vector search 

## Vector search with sqlitesearch 

## Vector search with PGVector

## ONNX Embedder



## Homework 
- [HW-2 Questions](https://courses.datatalks.club/llm-zoomcamp-2026/homework/hw2)
- [HW-2 Notebook Submission](https://github.com/tummala-hareesh/llm-zoomcamp-ht2026/blob/main/02-vector-search/ipynb/HomeWork-2.ipynb)
- [Quick Start for Vector Search](https://github.com/tummala-hareesh/llm-zoomcamp-ht2026/blob/main/02-vector-search/ipynb/quickstart-vectorsearch.ipynb)

## References 
- [Vector Databases: Embeddings, Semantic Search, and Hybrid Retrieval](https://www.youtube.com/watch?v=BC3NsRUNEIg)