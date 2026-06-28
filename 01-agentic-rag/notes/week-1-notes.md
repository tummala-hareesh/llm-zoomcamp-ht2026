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
- 


### Dataset - Course FAQ

### Search

### Building a Prompt

### RAG Pipeline 

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
