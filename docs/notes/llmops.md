---
tags:
  - MLOps
---

# LLMOps

- Fundamentals
    - MLOps: ML Development (Dev) + ML Operations (Ops)
        - integration
        - testing
        - releasing
        - deployment
        - infrastructure
    - MLOps framework: 
        - Data ingestion -> Data validation -> Data transformation -> Model training -> Model analysis -> Serving -> Logging
        - Job Management, Monitoring => Job Orchestration
    - LLMOps
        - Focus on LLMs development and managing deployment
            - experiment with foundation models
            - prompt design and management
            - supervised tuning
            - monitoring
            - evaluate generative output
        - LLM system design
            - Chain LLM calls
            - Grounding
            - Check history
        - e.g. LLM system 
                
                                            prompt     response
                User Input -> Pre-processing ----> LLM -----> Post-processing + Responsible AI -> User Output
                                 |                                  |   
                                  ----------- Grounding ------------ 
        - e.g. LLMOps pipeline
            - Data preparation and versioning -> Pipeline design (supervised tuning)
        
- Data Preparation for Tuning a Foundation Model
    - [Vertex AI](https://cloud.google.com/vertex-ai?hl=en)
    - Data Warehouse
        - Large data which does not fit into memory
        - e.g. Stack Overflow data
            - Tabels: Questions, Answers, Metadata
        - Use SQL (e.g. BigQuery) to query data
        - Best practice: 
            - Save large data in a data warehouse
            - Filter data needed to train/test model using SQL
            - Then transfer to SSD or Bucket for training
    - Query Optimization
        - Important to optimize queries for large datasets
    - When dealing with lots of data, consider filtering by time (only train on newer data)
    - Add instruction templates for data training to improve pefromance
    - Versioning data is also important - could just use a timestamp
    - JSONL is easily readable JSON format, for larger data use TFRecord or Parquet since they are more efficient to read

- Automation and Orchestration with Pipelines
    - Kubeflow Pipelines
                         -----------------  
                        |                 |
    - Training Data -> Training      Evaluation -> Deployment
                        |                 |
                         -----------------
    - Pipeline is a self-contained piece of code that represents a step in your workflow (e.g. data preprocessing, model training, model evaluation)
    - Uses DSL (Domain Specific Language) to define the pipeline
        - DSL component runs a containerized environment
        - When passing arguments to a component, must be keywork arguments
    - When dealing with large data, pass data by location (e.g. Cloud bucket location) instead of files
    - Compile pipelines into a yaml file which contains instructions on how to provision, run, and finish the pipeline
    - Can run pipelines using Vertex AI Pipelines (serverless layer on top of Kubernates)
    - Pipelines can be shared and reused, which is a big advantage
    ![Pipeline visualizer](image.png)

- Predictions, Prompts and Safety
    - Deployment: 
        - Real-time could be REST API, Offline bulk use cases could be batch processing
        - Model Monitoring: 
            - Operational metrics: e.g. endpoint hit rate, latency, throughput, error rate
        - Inference scalability
            - Load test
            - Controlled rollout
        - Latency
            - Smaller models
            - Faster processors
            - Regional deployment
    - Prompting:
        - Query prompts should have same format as training data
            ```python
                INSTRUCTION = """\
                    Please answer the following Stackoverflow question on Python.\
                    Answer it like\
                    you are a developer answering Stackoverflow questions.\
                    Question:
                    """
                
                PROMPT = f"""
                        {INSTRUCTION} {QUESTION}
                        """
            ```
    - Safety Attributes
        - Provided by model providers
        - As developer, you can add your own safety checks using the safetyAttributes values
        - probabilityScore (how likely harmful) vs severityScore (how harmful)
    - Citation 
        - VertexAI models have `['citationMetadata']['citations']` object to check for original source

