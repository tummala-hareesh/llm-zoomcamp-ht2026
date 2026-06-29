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


## Embedding out Dataset


## Vector Search 

## Vector Search with minsearch


## RAG With vector search 

## Vector search with sqlitesearch 

## Vector search with PGVector

## ONNX Embedder



## Homework 
- [HW-2 Questions](https://courses.datatalks.club/llm-zoomcamp-2026/homework/hw2)
- [HW-2 Notebook Submission](https://github.com/tummala-hareesh/llm-zoomcamp-ht2026/blob/main/02-vector-search/ipynb/HomeWork-2.ipynb)

## References 
- [Vector Databases: Embeddings, Semantic Search, and Hybrid Retrieval](https://www.youtube.com/watch?v=BC3NsRUNEIg)