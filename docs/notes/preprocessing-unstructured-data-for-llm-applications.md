---
tags:
  - Data Preprocessing
---

# Preprocessing Unstructured Data for LLM Applications

## 01. Introduction

Course covers preprocessing unstructured data from various file formats, 

## 02. Overview of LLM Data Preprocessing

- **Retrieval Augmented Generation (RAG):** Grounding LLM responses on validated external information
- **Contextual Integration:** RAG apps load context into a datbase, retrieve relevant content, and insert it into a prompt

### Preprocessing Unstructured Data

- **Document Content:** Extracting text from documents. Used for keyword or similarity search
- **Document Elements:** Building blocks of documents. Useful for filtering and chunking in RAG
    - e.g. Title, Narrative Text, List Item, Table, Image
- **Document Metadata:** Extracting metadata from documents. Used for filtering in hybrid search, or identifying source of a response
    - e.g. Filename, File Type, Page Number, Section

### Why is Data Preprocessing Hard?

- **Content Cues:** Different element types in an HTML versus a Markdown document
- **Standardization Need:** Need to standardize data across different document types for pipelines
- **Extracttion Variability:** Different requirements for extracting data from different document types e.g. forms vs journal articles
- **Metadata Insight:** Extracting metadata requires understanding of document structure

## 03. Normalizing Document Content

### Why Normalize?

We want data in common format so that your LLM pipeline is source agnostic. Benefits:

- **Normalization Benefit:** 
    - Filter out the unwanted elements (e.g. headers, footers)
    - Chunk elements by sections
- **Reduced Cost:** 
    - Downstream tasks like chunking easier on normalized data
    - Can chunk later without reprocessing documents

### Why Serialize?

Once data is normalized, we can serialize it to use again later. JSON is a suiable serialization format:

- Common structure, well understood
- Standard HTTP response format
- Easy to use in different programming languages
- Can be converted into JSONL for streaming use cases

### Preprocessing HTML

```python
filename = "example_files/medium_blog.html"
elements = partition_html(filename=filename)

element_dict = [el.to_dict() for el in elements]
example_output = json.dumps(element_dict[11:15], indent=2)
print(example_output)
```

### Preprocessing Powerpoint


### Preprocessing PDFs


## 03. Metadata Extraction and Chunking
