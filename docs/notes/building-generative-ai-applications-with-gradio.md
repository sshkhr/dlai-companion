# Building Generative AI Applications with Gradio

- NLP tasks with a simple interface
    - Summarization/Distillation
    - gr.Textbox()
    - demo.launch(share=True) for sharing the app
    - Named Entity Recognition: bert-ner
    - allow_flagging=True for flagging bad responses
    - gr.HighlightedText()
    - gr.Interface(examples) to show examples of input 

- Image interface
    - Image captioning
    - gr.Image()
    - gr.Image(type="pil/numpy") for image type. Gradio processes the image into format specified before passing to function

- Image generation
    - input: gr.Textbox(), output: gr.Image()
    - gr.Slider() for controlling numerical inputs
    - gr.Blocks()
        - gr.Markdown() for title/text etc.
        - gr.Row() for horizontal layout, gr.Column() for vertical layout
        - gr.Button() for buttons, also need to define button.click() to associate with function to call
        - gr.Accordion() for collapsible sections, set open=False to keep closed by default

- Chat
    - gr.Chatbot()
        - gr.ClearButton() and specify components() to clear
    - Can set up the text function to yield a response if LLM supports streaming responses

- demo.queue().launch() instead of demo.launch() to speed up demos
