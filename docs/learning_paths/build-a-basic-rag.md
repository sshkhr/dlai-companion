---
tags:
  - RAG
---

# Learn: How To Build a Basic RAG

``` mermaid
flowchart TB
  subgraph A[Learn: Data Pre-Processing]
    A1(Preprocessing Unstructured Data for LLM Applications)
  end
  subgraph B[Learn: Vector Embeddings]
    B1(Understanding and Applying Text Embeddings)
  end
  subgraph C[Learn: Vector Databases]
    B2(Vector Databases: From Embeddings to Applications)
    B3(Building Applications with Vector Databases)
  end
  subgraph D[Learn: Intro to LLMs]
    C1(Getting Started With Mistral)
    C2(Prompt Engineering with Llama 2 & 3)
    C3(Building Systems with the ChatGPT API)
  end
  subgraph E[Learn: Basic RAG]
    D2(LangChain: Chat with Your Data)
    D3(Build LLM Apps with LangChain.js)
    D4(LangChain for LLM Application Development)
    D5(JavaScript RAG Web Apps with LlamaIndex)
  end

  A --> B
  B --> C
  C --> E
  D --> E
  
  click A1 "https://www.deeplearning.ai/courses/preprocessing-unstructured-data-for-llm-applications" "Go to Data Pre-Processing Course"
  click B1 "https://www.deeplearning.ai/courses/understanding-and-applying-text-embeddings" "Go to Understanding and Applying Text Embeddings Course"
  click B2 "https://www.deeplearning.ai/courses/vector-databases-from-embeddings-to-applications" "Go to Vector Databases: From Embeddings to Applications Course"
  click B3 "https://www.deeplearning.ai/courses/building-applications-with-vector-databases" "Go to Building Applications with Vector Databases Course"
  click C1 "https://www.deeplearning.ai/courses/getting-started-with-mistral" "Go to Getting Started With Mistral Course"
  click C2 "https://www.deeplearning.ai/courses/prompt-engineering-with-llama-2-3" "Go to Prompt Engineering with Llama 2 & 3 Course"
  click C3 "https://www.deeplearning.ai/courses/building-systems-with-the-chatgpt-api" "Go to Building Systems with the ChatGPT API Course"
  click D1 "https://www.deeplearning.ai/courses/building-and-evaluating-advanced-rag-applications" "Go to Building and Evaluating Advanced RAG Applications Course"
  click D2 "https://www.deeplearning.ai/courses/langchain-chat-with-your-data" "Go to LangChain: Chat with Your Data Course"
  click D3 "https://www.deeplearning.ai/courses/build-llm-apps-with-langchain-js" "Go to Build LLM Apps with LangChain.js Course"
  click D4 "https://www.deeplearning.ai/courses/langchain-for-llm-application-development" "Go to LangChain for LLM Application Development Course"
  click D5 "https://www.deeplearning.ai/courses/javascript-rag-web-apps-with-llamaindex" "Go to JavaScript RAG Web Apps with LlamaIndex Course"
  click D6 "https://www.deeplearning.ai/courses/advanced-retrieval-for-ai-with-chroma" "Go to Advanced Retrieval for AI with Chroma Course"
  click D7 "https://www.deeplearning.ai/courses/building-agentic-rag-with-llamaindex" "Go to Building Agentic RAG with LlamaIndex Course"
  click D8 "https://www.deeplearning.ai/courses/knowledge-graphs-for-rag" "Go to Knowledge Graphs for RAG Course"
```