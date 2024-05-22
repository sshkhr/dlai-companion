---
tags:
  - Course Notes
  - Data Preprocessing
  - RAG
---

# Preprocessing Unstructured Data for LLM Applications

## 01. Introduction

Course covers preprocessing unstructured data from various file formats, getting it into a structured format, and utilizing the data in LLM applications for both semantic search (text similarity based) and hybrid search (semantic + metadata based information retrieval).

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

- **LLM Relevance:** Integrate fresh internet content into LLM rcontext to maintain relevance.
- **HTML Understanding:** Use existing structure from HTML tags to extract document structure, use NLP to understand text content
    - e.g. Long content within <p> tags is probably content, short capitalized text is probably a section title
- **Data Extraction and Categorization:** Analyze HTML elements to extract and organize web content

```python
filename = "example_files/medium_blog.html"
elements = partition_html(filename=filename)

element_dict = [el.to_dict() for el in elements]
example_output = json.dumps(element_dict[11:15], indent=2)
print(example_output)
```

### Preprocessing Powerpoint

- **Professionals Use Cases:** Extracting content from Powerpoint slides useful for LLM applications in business use cases
- **Extraction Process:** Under the hood `.pptx` are combination of `.xml` files. Can exploit known structure to extract content
- **Tool Utilization:** Use python libraries like `pptx` library to extract content from Powerpoint slides

```python
filename = "example_files/msft_openai.pptx"
elements = partition_pptx(filename=filename)
element_dict = [el.to_dict() for el in elements]
JSON(json.dumps(element_dict[:], indent=2))
```

### Preprocessing PDFs

More complex than HTML or Powerpoints

- **PDF Characterstics:**
    - Consistent format across devices
    - Complex structure with diverse layout (images, tables, text in various orders)
- **Extraction Process:** 
    - Need to facilitate text and element extraction
    - Need to preserve context and layout
- **Advanced:** Use OCR and transformer-based approaches - use visual information to extract content

```python
filename = "example_files/CoT.pdf"
with open(filename, "rb") as f:
    files=shared.Files(
        content=f.read(), 
        file_name=filename,
    )

req = shared.PartitionParameters(
    files=files,
    strategy='hi_res',
    pdf_infer_table_structure=True,
    languages=["eng"],
)
try:
    # Model based workload
    resp = s.general.partition(req)
    print(json.dumps(resp.elements[:3], indent=2))
except SDKError as e:
    print(e)
```

## 04. Metadata Extraction and Chunking

Metadata supports hybrid search

### What is Metadata?

- **Document Details:** Additional information about content extracted from source document
- **Source Identification:** Data about document itself e.g. filename, source URL, filetype
- **Structural Information:** Metadata extracted from document structure e.g. element type, heirarchy information, section information, page number
- **Search enhancement:** Metadata can be used to filter search results for hybrid search (semantic + traditional search)

### Semantic Search

- **Goal:** Given input text, find semantically similar documents from a corpus (e.g. for use in prompt templates as context)
- **Vector Embeddings:** Convert text into vectors, then use similarity measures e.g. cosine similarity to find similar documents
- **Vector Database:** Databse optimized for storing vector embeddings and performing similarity search
    - **Load:** Insert vectors into database, along with source document (or pointer to it)
- **Prompt Templates:** Insert relevant context into the token context window for an LLM input
    - **Query Embed:** Emebed the input into the vector, perform similarity search
    - **Compare and Retrieve:** Retrieve k most similar documents, insert into prompt template

### Hybrid Search

When does semantic search fail?

- **Too many results:** Sometimes similarity search returns too many matches, e.g. when many documents on similar topic
- **Most recent information:** Users may care about most recent information, not most semantically similar
- **Loss of important information:** Semantic search may not consider all relevant information, such as section information

Why hybrid search?

- **Hybrid Search:** Combine semantic search with traditional information retrieval, such as filtering and keyword search
- **Filtering:** Use metadata to filter search results

### Metadata Extraction and Chunking

e.g. Extracting metadata from an ePUB file for a book

#### Filerting elements 

e.g. Filtering elements from a response to find those that are titles and contain the word "hockey"

```python
[x for x in resp.elements if x['type'] == 'Title' and 'hockey' in x['text'].lower()]
```

e.g. Filtering element IDs for elements corresponding to chapters. All child elements of this element will be contents of the chapter.

```python
chapter_ids = {}
for element in resp.elements:
    for chapter in chapters:
        if element["text"] == chapter and element["type"] == "Title":
            chapter_ids[element["element_id"]] = chapter
            break
```

#### Load documents into a vector database

ChromaDB is an in-memory vector database.

```python
client = chromadb.PersistentClient(path="chroma_tmp", settings=chromadb.Settings(allow_reset=True))
client.reset()

collection = client.create_collection(
    name="winter_sports",
    metadata={"hnsw:space": "cosine"} # metadata for vector emebdding
)

for element in resp.elements:
    parent_id = element["metadata"].get("parent_id")
    chapter = chapter_ids.get(parent_id, "")
    collection.add(
        documents=[element["text"]],
        ids=[element["element_id"]],
        metadatas=[{"chapter": chapter}]
    )
```

#### Perform Hybrid Search with metadata

```python
result = collection.query(
    query_texts=["How many players are on a team?"],
    n_results=2,
    where={"chapter": "ICE-HOCKEY"},
)
print(json.dumps(result, indent=2))
```

#### Chunking

- **Chunking Necessity:** Vector databases need documents to be split into chunks for retrieval and prompt generation
- **Query Result Variability:** Document chunking quality determines quality of query results
- **Chunking Process:** 
    - **Most straightforward:** Split document into fixed-size chunks
    - **By atomic elements:** Rather than splitting raw text, split by document elements.
        - Results in more coherent chunks
        - e.g. combining content under same section into the same chunk

#### Chunking By Elements

1. First, break down documents into atomic elements.
2. Combine elements into a chunk, until you reach a character or token threshold.
3. Apply conditions for starting a new chunk, e.g. when a new section starts, or when a section metadata changes.
4. Optionally, combine small chunks into larger chunks so that they are sufficiently large for effective semantic search.

```python
elements = dict_to_elements(resp.elements)

chunks = chunk_by_title(
    elements,
    combine_text_under_n_chars=100,
    max_characters=3000,
)
```

## 05. Preprocessing PDFs and Images

These types of documents require model based processing (i.e. using an ML model). This lecture covers document layout detection, and using vision transformers for document processing.

- **Rules based parsing:** Extracting text from a document based on known structure (e.g. HTML, Powerpoint)
- **Visual information based parsing:** Extracting text from a document based on visual information (e.g. PDF, Images)

### Document Image Analysis

Extract formatting information and text from raw image of the document.
- Document Layout Detection
- Vision Transformers

#### Document Layout Detection

Use object detection to draw and label bounding boxes around elements in the document image.

1. **Detection:** Identify and classify a bounding box using a Vision model like YOLOX or Detectron2.
2. **OCR:** Extract text from the bounding box using an OCR model like Tesseract or EasyOCR.
3. **Direct Extraction:** For some documents like PDFs, text can be exracted directly from the dcoument without OCR.

#### Vision Transformers

Document image as input (+ a text prompt - optionally) and produce a text representation of a structured output (e.g. JSON).

1. **Architecture:** Use a Vision Transformer model like DONUT (Document Understanding Transformer).
2. **Direct Extraction:** Extract text from the document image using the model, no OCR required.
3. **Structured Output:** Model can be trained to output structured JSON representation.

### Comparison of Document Analysis Approaches

|              | **Document Layout Models**                    | **Vision Transformers**                        |
|--------------|------------------------------------------|---------------------------------------------|
| **Advantages**   | - Fixed set of element types              | - More flexible for non-standard docs (e.g. forms) |
|              | - Get bounding box info                    | - More adaptable for new ontologies         |
| **Disadvantages**| - Two model calls (detection + ocr)        | - Generative model (prone to hallucination, repetition) |
|              | - Less flexible                            | - Computationally Expensive                 |

```python
with open(filename, "rb") as f:
    files=shared.Files(
        content=f.read(),
        file_name=filename,
    )

req = shared.PartitionParameters(
    files=files,
    strategy="hi_res",
    hi_res_model_name="yolox",
)

try:
    resp = s.general.partition(req)
    dld_elements = dict_to_elements(resp.elements)
except SDKError as e:
    print(e)
```

## 06. Extracting Tables

Most LLM/RAG use cases focus on text content withing documents.

- **Structured Data:** Some industries (e.g. finance, insurance, etc.) deal heavily with structured data embedded withing structured documents.
- **Table Extraction:** For QA over tables, we first need to extract tables from documents.
    - Some documents contain inherent table structure information (e.g. HTML, Word, Excel).
    - For other documents, we need to extract tables (e.g. PDFs, Images).

### Table Extraction Approaches

- Table Transformers
- Vision Transformers
- OCR Post-Processing

After processing, keep table data in a structured format (e.g. HTML) for downstream processing.

#### Table Transformers

**Table Transformers:** Model trained to extract tables from document images called [Tableformer](https://arxiv.org/abs/2203.01017). Steps:
    1. **Detection:** Detect table regions in the document image using document layout model.
    2. **Extraction:** Extract table using tableformer, which returns HTML representation of the table.

**Pros:** Can trace cells back to original bounding box.
**Cons:** Requires two models (layout detection + table extraction). Expensive.

#### Vision Transformers

Use vision transformers, but trained with HTML response.

**Pros:** One model call; more flexible; can be prompted with text.
**Cons:** Generative - prone to hallucination; no bounding boxes so limited grounding.

#### OCR Post-Processing

OCR the table, then use output patterns in the OCR output to build the table.

**Pros:** Fast; works well for tables which are not too complicated.
**Cons:** Requires rule-based parsing; Less flexible; No bounding box.

```python
filename = "example_files/embedded-images-tables.pdf"

with open(filename, "rb") as f:
    files=shared.Files(
        content=f.read(),
        file_name=filename,
    )

req = shared.PartitionParameters(
    files=files,
    strategy="hi_res",
    hi_res_model_name="yolox",
    skip_infer_table_types=[],
    pdf_infer_table_structure=True,
)

try:
    resp = s.general.partition(req)
    elements = dict_to_elements(resp.elements)
except SDKError as e:
    print(e)

tables = [el for el in elements if el.category == "Table"]

table_html = tables[0].metadata.text_as_html

from io import StringIO 
from lxml import etree

parser = etree.XMLParser(remove_blank_text=True)
file_obj = StringIO(table_html)
tree = etree.parse(file_obj, parser)
print(etree.tostring(tree, pretty_print=True).decode())
```

## 06. Build your own RAG Bot

``` mermaid
%%{init: {'flowchart' : {'curve' : 'stepAfter'}}}%%

flowchart-elk TD
    subgraph Unstructured Data
        A1[HTML]
        A2[PowerPoint]
        A3[PDF]
    end
    subgraph RAG System
        B[Vector Database]
        C[Prompt: Context + Query]
        D[LLM]
        E[User]
    end
    A1 -->|Extract| B
    A2 -->|Extract| B
    A3 -->|Extract| B
    E -->|Query| B & C
    B -->|Context| C
    C -->|Prompt| D
    D -->|LLM Response| E

```

`Code example not included because it breaks with Chroma. Chroma expects metadata to be a simpler type, and some of the metadata is a list with bounding boxes and text content in the example.`

## 07. Conclusion

**Learnings:** Extracting data,  metadata extraction, chunking, vector databases, semantic and hybrid search, building a RAG bot.