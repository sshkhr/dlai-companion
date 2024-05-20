# Prompt Engineering with Llama 2 & 3

- Llama model family:
    - Llama 2: 7B, 13B, 70B 
    - Llama 2 Chat: instruction tuned versions of Llama 2
    - Llama-cpp: Runs on regular CPUs
    - Code Llama: Llama 2 fine-tuning
        - Code Llama: Long-context fine-tuning
        - Code Llama Instruct: Long-context fine-tuning + instruction fine-tuning
        - Code Llama Python: Python training + Long-context fine-tuning
    - Purple Llama: Tools + Benchmarks + Models
        - CyberSecEval: Tools and benchmarks for cybersecurity risk of LLMs
        - Llama Guard: Safety classifier model

- Llama 2:
    - Hosted API service
        - Amazon Bedrock
        - Anyscale
        - Google Cloud
        - Microsoft Azure
        - Replicate
        - Together.ai
    - Host yourself on cloud
    - Download + Host yourself
    - [INST]...[/INST] tags before and after prompt to let model know that it's an instruction
    - Foundation model not trained for instruction following, but next token prediction
        - Optimizes for generation and not following instructions
    - Temperature parameter to increase/decrease creativity
        - Set to 0 to get deterministic output
    - Max tokens parameter to limit output length
        - Llama 2: 4096 tokens or less (sum of input + output)
    - By default, chats across multiple messages are treated as separate conversations, and context is not maintained

- To run llama 2 locally: [Ollama](https://ollama.com/)

- Llama 3:
    - 8B Chat, 70B Chat available on Together.ai

- Llama is stateless
    - Need to constuct multi-turn prompts manually
    - use start and end tages for in-context prompting, but leave final end tag empty
    - ```python
        chat_prompt = f"""
        <s>[INST] {prompt_1} [/INST]
        {response_1}
        </s>
        <s>[INST] {prompt_2} [/INST]
        """

- Prompt Engineering
    - In-context learning: providing examples of what tasks you want the model to do
        - Zero-shot prompting: e.g. infer task from prompt
            - prompt = """
                        Message: Hi Amit, thanks for the thoughtful birthday card!
                        Sentiment: ?
                        """
        - Few-shot prompting: e.g. infer task from 2-3 examples given in prompt
            - prompt = """
                        Message: Hi Dad, you're 20 minutes late to my piano recital!
                        Sentiment: Negative

                        Message: Can't wait to order pizza for dinner tonight
                        Sentiment: Positive

                        Message: Hi Amit, thanks for the thoughtful birthday card!
                        Sentiment: ?
                        """
    - Specifying output format: 
        - e.g. "Give a one word response"
        - e.g. "Respond with either positive, negative, or neutral"
    - Role/Persona based prompting: 
        - e.g. "Your role is a life coach \
                who gives advice to people about living a good life.\
                You attempt to provide unbiased advice."
    - Summarization
    - Providing information in the prompt
        - e.g. prompt = f"""
                        Given the following context, who won the 2023 Women's World cup?
                        context: {context}
                        """
    - Chain-of-thought prompting
        - Ask the model to "think step by step", or "explain your reasoning"
        - e.g. prompt = """
                        Think step by step.
                        Explain each intermediate step.
                        Only when you are done with all your steps,
                        provide the answer based on your intermediate steps.
                        """
        - Order of instructions matters
            - Providing answer first and then asking for steps will often not work
            - Since LLMs predict their answer one token at a time, the best practice is to ask them to think step by step, and then only provide the answer after they have explained their reasoning.

- Comparing Llama Models
    - Llama 2: 7B, 13B, 70B (base/chat), 13.5 GB, 26 GB, 138 GB 
    - Benchmarks: Commonsense Reasoning, World Knowledge, Reading Comprehension
    - Safety/Alignment: TruthfulQA (chat > base), Toxigen (chat models = 0)
        - But for fine-tuning, better to start with base
    - Comparing summarization
    - Model-Graded Evaluations
    - Reasoning
        - Smaller models tend to perform worse on reasoning tasks than larger models

- Llama Code
    - Code Llama: Llama 2 7B/13B/34B + code training 
        - Code Llama 7B/13B/34B: Long-context fine-tuning
        - Code Llama Instruct 7B/13B/34B: Long-context fine-tuning + instruction fine-tuning
            - Requires [INST]{prompt}[/INST] tags
        - Code Llama Python 7B/13B/34B: Python training + Long-context fine-tuning
        - Code Filling:
            - Use <FILL> tokens
            - ```python
                    prompt = """
                        def star_rating(n):
                        '''
                        This function returns a rating given the number n,
                        where n is an integers from 1 to 5.
                        '''

                            if n == 1:
                                rating="poor"
                            <FILL>
                            elif n == 5:
                                rating="excellent"

                            return rating
                        """
                    ```
        - Can use CodeLLama to also ask to make code more efficient
        - Longer context windows for code completion: 20K tokens vs 4K tokens for chat
            - While it can be used for summarization, it's not optimized for it

- Llama Guard
    - Llama-2-7B fine-tuned for safety classification = Llama Guard
    - Safeguarding guidelines:
        - 01: Violence and Hate
        - 02: Sexual Content
        - 03: Criminal Planning
        - 04: Guns and Illegal Weapons
        - 05: Regulated or Controlled Substances
        - 06: Self-harm
    - Formatting: 
        - ```python
            conversation = f"""
            <BEGIN CONVERSATION>
            User: {query}
            <END CONVERSATION>
            """
            ```
    - Can be applied to both User and Agent messages
