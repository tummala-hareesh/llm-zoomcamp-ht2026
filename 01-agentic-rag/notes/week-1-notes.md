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


### What is RAG

### Dataset - Course FAQ
### Seach 
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
