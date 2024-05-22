---
tags:
  - RAG
---

# Learn How To: Build a Basic RAG

``` mermaid
graph TD
    A[Data Collection] --> B[Data Preprocessing]
    B --> C[Index Creation]
    C --> D[Query Understanding]
    D --> E[Retrieval]
    E --> F[Response Generation]
    F --> G[Post-Processing]

    A -->|Collect relevant datasets| B
    B -->|Clean, normalize, and tokenize| C
    C -->|Create and store index| D
    D -->|Parse and preprocess query| E
    E -->|Retrieve and rank documents| F
    F -->|Generate response with language model| G
    G -->|Refine and format response| H[Final Response]
```