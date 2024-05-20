# ChatGPT Prompt Engineering for Developers

- GPT 3.5 and chat completion only
- Principle 1: Clear & Specific instructions
    - Use delimiters in prompt
    - Ask for structured output JSON/HTML
    - Ask model to check output in prompt
    - Few shot prompting
- Principle 2: Give model time to think
    - Specify steps required to complete a task
    - Ask for output in a specified format
    - Ask model to work out its own solution before rushing to a conclusion
- Limitations: Hallucinations
- Iterative Prompt Development
    - Idea -> Implement prompt/code -> Get experimental result -> Analyze Errors -> Iterate
    - e.g.
        - Define prompt
        - Limit # of tokens/words/characters
        - Ask to focus on aspects relevant to intended audience
- Summarizing:
    - e.g. Focus on word/character/sentence limits
    - e.g. Focus on shipping and delivery
    - e.g. Focus on price and value
    - Exraction vs Summarization
- Inferring:
    - e.g. Sentiment (positive/negative)
    - e.g. Emotion types
    - e.g. Information Extraction e.g. entity via structured output generation
    = e.g. Information Extraction e.g. topic extraction
- Transforming:
    - e.g. Translation
    - e.g. Tone translation
    - e.g. JSON to HTML
    - e.g. Spell/Grammar check (Python redlines package)
- Expanding:
    - e.g. Automated reply based on sentiment
    - Temperature parameter to increase/decrease creativity (i.e. variance)
- Chatbot 
    - System/User message
    - System context specified via message

e.g.

```python
    import panel as pn  # GUI
    pn.extension()

    panels = [] # collect display 

    context = [ {'role':'system', 'content':"""
    You are OrderBot, an automated service to collect orders for a pizza restaurant. \
    You first greet the customer, then collects the order, \
    and then asks if it's a pickup or delivery. \
    You wait to collect the entire order, then summarize it and check for a final \
    time if the customer wants to add anything else. \
    If it's a delivery, you ask for an address. \
    Finally you collect the payment.\
    Make sure to clarify all options, extras and sizes to uniquely \
    identify the item from the menu.\
    You respond in a short, very conversational friendly style. \
    The menu includes \
    pepperoni pizza  12.95, 10.00, 7.00 \
    cheese pizza   10.95, 9.25, 6.50 \
    eggplant pizza   11.95, 9.75, 6.75 \
    fries 4.50, 3.50 \
    greek salad 7.25 \
    Toppings: \
    extra cheese 2.00, \
    mushrooms 1.50 \
    sausage 3.00 \
    canadian bacon 3.50 \
    AI sauce 1.50 \
    peppers 1.00 \
    Drinks: \
    coke 3.00, 2.00, 1.00 \
    sprite 3.00, 2.00, 1.00 \
    bottled water 5.00 \
    """} ]  # accumulate messages


    inp = pn.widgets.TextInput(value="Hi", placeholder='Enter text here…')
    button_conversation = pn.widgets.Button(name="Chat!")

    interactive_conversation = pn.bind(collect_messages, button_conversation)

    dashboard = pn.Column(
        inp,
        pn.Row(button_conversation),
        pn.panel(interactive_conversation, loading_indicator=True, height=300),
    )

    dashboard
```