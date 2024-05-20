# Pair Programming with a Large Language Model

- Getting started with PaLM
    - PaLM API, Makersuite (quick prototyping), Vertex AI (scalable applications)
    - PaLM has a chat/text/embedding models
    - Simple prompt engineering for coding
        - e.g. "Show me how to..." will include instructions with code
        - e.g. "write code to..." will generate code only

- Using a string template
    - Priming
        ```python
        prompt_template = """
                            {priming}

                            {question}

                            {decorator}

                            Your solution:
                            """
        priming_text = "You are an expert at writing clear, concise, Python code."
        # option 1
        decorator = "Work through it step by step, and show your work. One step per line."

        # option 2
        decorator = "Insert comments for each line of code."
        ```

- Pair Programming Scenarios
    - Improve existing code e.g.
        ```python
        prompt_template = """
        I don't think this code is the best way to do it in Python, can you help me?

        {question}

        Please explore multiple ways of solving the problem, 
        and tell me which is the most Pythonic
        """
        ```
    - Simplify existing code
        ```python
        prompt_template = """
        Can you please simplify this code for a linked list in Python?

        {question}

        Explain in detail what you did to modify it, and why.
        """
        ```
    - Write test code
        ```python
        prompt_template = """
        Can you please create test cases in code for this Python code?

        {question}

        Explain in detail what these test cases are designed to achieve.
        """
        ```
    - Make code more efficient
        ```python
        prompt_template = """
        Can you please make this code more efficient?

        {question}

        Explain in detail what you changed and why.
        """
        ```
    - Debug your code
        ```python
        prompt_template = """
        Can you please help me debug this code?

        {question}

        Explain in detail what you found and why it was a bug.
        """
        ```
    - Double check LLM code cause it is often slightly wrong

- Technical Debt
    - Issue where complicated code is written, and often handed down to other developers
    - e.g. For explaining previously written code
        ```python
        prompt_template = """
                        Can you please explain how this code works?

                        {question}

                        Use a lot of detail and make it as clear as possible.
                        """
        ```
    - e.g. For writing documentation
        ```python
        prompt_template = """
                            Please write technical documentation for this code and \n
                            make it easy for a non swift developer to understand:

                            {question}

                            Output the results in markdown
                            """
        ```