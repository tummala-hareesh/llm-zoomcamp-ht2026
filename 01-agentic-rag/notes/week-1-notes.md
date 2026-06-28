# Week-1 Notes - Agentic RAG 
## Part-1: RAG
### Introduction 
- Build RAG system from scratch 
- Small search index by hand and call LLM

### Env. Setup 
- Python 
- OpenAI client
- Linux (Ubuntu)
- uv
```
# Install uv
curl -LsSf https://astral.sh/uv/install.sh | sh
or 
pip install uv

# Initialize project using uv 
uv init --bare 

# Add dependencies 
uv add requests minsearch openai jupyter python-dotenv
```
- Setting up API keys
```
# .env file 
OPENAI_API_KEY=sk-YOUR_KEY_HERE
```
- [Link to quickstart notebook](https://github.com/tummala-hareesh/llm-zoomcamp-ht2026/blob/main/01-agentic-rag/ipynb/quick-start-lesson-setup.ipynb)


### What is RAG?
- Example usecase: 
    - 300 Q&A from Zoomcamp (DataTalksClub)
    - Bot that takes all this knowledge and answer student questions in natural language 
- Plain LLMs lack our data (NO context)
    - [Function to talk to LLM](https://github.com/tummala-hareesh/llm-zoomcamp-ht2026/blob/main/01-agentic-rag/ipynb/rag_helper_hw.py) 
    - Generic answer from LLM 
- Adding Context manually; Example context is below:
```
context = """
I just discovered the course. Can I still join?
Yes, but if you want to receive a certificate, you need to submit your project while we're still accepting submissions.

Course: I have registered for the LLM Zoomcamp. When can I expect to receive the confirmation email?
You don't need it. You're accepted. You can also just start learning and submitting homework (while the form is open) without registering. It is not checked against any registered list. Registration is just to gauge interest before the start date.

What is the video/zoom link to the stream for the "Office Hours" or live/workshop sessions?
The zoom link is only published to instructors/presenters/TAs. Students participate via YouTube Live and submit questions to Slido.

Cloud alternatives with GPU
Check the quota and reset cycle carefully. Potential options include Google Colab, Kaggle, Databricks.
"""
```

- Build a prompt that makes sense
```
Your task is to answer questions from the course participant based on the provided context. 

Use the context to find relevant information and provide answers. 


Question: {question}


Context: {context}
```
- Sending prompt to the LLM 
```
answer = llm(prompt)
```

- Retrieval plus generation 
- RAG stands for **Retrieval-Augmented Generation**
- Generation is the LLM producing text; Retrieval is search 
- Retrieve relevant documents from KB -> Use to augument what the LLM generates 
- To perform search automatically, take student question -> find most relevant document -> send those to LLM. 
```
def rag(question):
    search_results = search(question)
    user_prompt = build_prompt(question, search_results)
    return llm(user_prompt)
```
- Architecture of RAG: 
    - 3 components 
    - Search, the Prompt, and LLM 
    - LLM sees only the data we hand it; Response becomes grounded to the data. 
    - If correct document is retrieved, answer is good. 
    - Model is as good as retrieval, so search quality matters a lot in RAG based systems.
- DB and LLM can be anything; 
    - In this course, 
    - `minserach` and `sqllitesearch` for search
    - `OpenAI` for LLM
- Each piece is indepedent, so RAG is flexible. 


### Dataset - Course FAQ
- FAQs from zoomcamp cohorts
- Grow over time and gets tedious to get answers by reading or searching through all. 
- RAG system to solve the issue. 
- Json format
- Data available at a JSON endpoint 
- Each entry has:
    - id - unique identifier
    - course - course slug
    - section - which section of the course
    - question - FAQ question 
    - answer - FAQ answer 
- slug for filtering in search. 
- In the RAG pipeline, 
    - dataset is our KB
    - We index all documents. 
    - Student asks question -> search the index. 
    - Search returns the most relevant FAQ entries 
    - Entries to LLM as context 
    - LLM genertes answer based on context 
- In realistic usecases, data prep. might be needed. 
    - scrape websites -> parse PDFs -> clean and chunk documents. 

### Search
- Search Engine (core) -> take a query -> scores every document for similarity and returns the top results. 
- There is a similarity function 
```
    score = sim(query, document)
```
- For each document, compute this score -> then, rank all documents by score and return the top N. 
- Difference in search engine = different `sim` computation. 
    - `text/lexical search` - `sim` counts how many words the query and document share. Surface form, the actual words and matches them exactly. 
    - `vector/sematic search` - `sim` compares the meaning of the queyr and the document. Same fucntion, different similarity measure. 
- Indexing with `minsearch`
    - searching is important. 
    - sending all the documents to LLM will be expensive and slow. 
    - model confusion with too much data. 
    - Search to find the most relevant documents to send instead. 
- Search libraries: 
    - Apache Lucene
    - Elastic search 
    - Solr
    - ...
    - `minsearch`: 
        - a simple in-memory search engine
        - Light weight
        - No docker container needed. 
        - Toy implementaiton - not production ready  
        - Concept is same as in Elasticsearch 
            - Text fields, keyword fields, boosting, filtering
        - Index question, section and answer fields as text. course field as filtering keyword. 
- Trying a search 
    - `.search()` method. 
    - arguments: 
        - `boost_dict` to give the question field more weight. 
        - `section` less weight 
        - `filter_dict` to only return from the LLM zoomcamp source. 
- Similar boosting mechanism used in `Elasticsearch` and `Lucene`.

### Building a Prompt
- LLM knows -> passed documents only 
- Build a prompt to include user's question and search results.
- Split prompt into 2: 
    - Instructions 
        - Tells the LLM how to behave. 
        - It never changes; fixed  
    - User Prompts 
        - This changes with every request
        - It carries the actual question and the retrieved context. 
        - Not fixed
- Instructions
    - Tells LLM its role and how to answer 
    - Grounds the answer in our data and reduces hallucinations.
- The user prompt template 
    - User prompt has placeholders for the questios and the context. 
    - Building the context 
        - Formated string with all search results. 
        - Each document becomes a block with section, question and answer 
        - Format makes it easy for LLM to read. 
        - List of dicts -> 1 string 
    - Building the prompt 
        - Combine question with context into the user prompt 
        - Bridge between search and LLM 
        - **Prompt Engineering** is part art , part science. 
- Exploring the response 
    - Response is a pydantic object. 
    - Answer is in `response.output`
    - Usage counts tell how many tokens the request consumed.
- Message History 
    - Sending only 1 string as input and getting back 1 reponse. 
    - In reality, typically send a message history - a list of messages where each message has a role. 


### RAG Pipeline 
- Wiring search + prompt + LLM together
- Full RAG 
    - 1. Search 
    - 2. Build Prompt 
    - 3. Answer using LLM 
- Modular approach 
    - Swap the search backend 
    - Change the prompt template
    - or the LLM model 

### RAG Helper 

### Data Ingestion 

### Wrap-up of Part-1

## Part-2: Agents
### Agents 
### Quck RAG Revision 
### Function Calling 
### The Agentic Loop 
### ToyAIKit
### Other Frameworks 



## Homework 
- [HW-1 Questions](https://courses.datatalks.club/llm-zoomcamp-2026/homework/hw1)
- [HW-1 Notebook Submission](https://github.com/tummala-hareesh/llm-zoomcamp-ht2026/blob/main/01-agentic-rag/ipynb/HomeWork-1.ipynb)

## References 
- [Build a Search Engine](https://www.youtube.com/watch?v=nMrGK5QgPVE)