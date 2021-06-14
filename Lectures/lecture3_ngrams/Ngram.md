# Lecture 3 N-gram Language Models

### Language Models

* One application NLP is about <i>explaining language</i>: Why some sentences are more fluent than others
* E.g. in speech recognition: recognize speech > wreck a nice beach
* Measures "goodness" using probabilities estimated by language models
* Language model can also be used for generation
* Language model is useful for:
    * Query completion
    * Optical character recognition
    * And other generation tasks:
        * Machine translation
        * Summarization
        * Dialogue systems
* Nowadays pretrained language models are the backbone of modern NLP systems

### Probabilities: Joint to Conditional

* Goal of language model is to get a probability for an arbitrary sequence of <i>m</i> words:   
  <img src="https://render.githubusercontent.com/render/math?math=P(w_1, w_2, \ldots, w_m)">

* First step is to apply the chain rule to convert joint probabilities to conditional ones:
  <img src="https://render.githubusercontent.com/render/math?math=P(w_1, w_2, \ldots, w_m) = P(w_1)P(w_2|w_1)P(w_3|w_1, w_2) \ldots P(w_m|w_1, \ldots, w_{m-1})">
