# Understanding and Applying Text Embeddings

- Vector Embeddings
    - A neural network based compression of text into a fixed length vector
    - Similarity
        - e.g. Cosine similarity
        - Useful for comparing how similar different pairs of vectors (and thus text inputs) are
    - Word to Sentence Embeddings
        - One way: Average embeddings for all words in a sentence
            - Basically treats sentence as a bag of words
        - Better to have sentence level embeddings

-  What is an embedding?
    - Represent data as points in space which are semantically meaningful
    - Use a transformer neural network to learn word embeddings, then take an average of context aware embeddings to get sentence embeddings
    - Compute embeddings for each token (e.g. sub word). Works better for novel words and misspellings
    - Learned using contrastive learning on large unsupervised corpus of texts
    - Multi-modal embeddings

- Visualize embeddings
    - Better for building intuition, not as useful for monitoring
    - One way to visualize is to get a low dimensional representation of embeddings using PCA and visualize in 2D
    - Another way to visualize is to use heatmaps to compare similarity between different embeddings

- Application of embeddings
    - Clustering
    - Anamoly/Outlier detection
        - Use an outlier detection algorithm e.g. Isolation Forest to find embeddings that are far from the rest
    - For classification (i.e. labelling)
        - Use embeddings to classify text
            - Do a normal train/test split and verify classification accuracy before using in production

- TextGeneration with VertexAI
    - Use a pre-trained LLM model to generate text + Use a prompt to guide the model
        - Generative by default
        - Use a prompt to get classification, or extract information
    - Use temperature to creativity/randomness
    - Top p: Sample from the minimum set of tokens whose probabilities add up to probability p or greater
    - Top k: Sample from the top k tokens
    - Sampling sequence: Top k -> Top p -> Temperature

- Question Answering: LLM text generation + embedding
    - Embeddings are useful for grounding
    - Semantic search with embeddings
        - Use embeddings to find similar questions
            - Use distance metrics like Euclidean distance or cosine similarity or dot product
        - Take new query, embed it, and find closest embeddings in the database of questions, use the answer to the closest question as context in the prompt for the LLM query
            - ```python
                context = "Question: " + so_database.input_text[index_doc_cosine] +\
                            "\n Answer: " + so_database.output_text[index_doc_cosine]

                prompt = f"""Here is the context: {context}
                            Using the relevant information from the context,
                            provide an answer to the query: {query}."
                            If the context doesn't provide \
                            any relevant information, \
                            answer with \
                            [I couldn't find a good match in the \
                            document database for your query]
                            """
            ```
    - For questions that have no relevant context, use a default response (or some other answer flow)
    - Scale with approximate nearest neighbors (ANN) search for large scale embedding databases
        - e.g. scann algorithm `import scann`
