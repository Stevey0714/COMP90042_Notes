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
  
### The Markov Assumption

* <img src="https://render.githubusercontent.com/render/math?math=P(w_1)P(w_2|w_1)P(w_3|w_1, w_2) \ldots P(w_m|w_1, \ldots, w_{m-1})" alt=""> is still intractable, so make a simplifying assumption:
    <img src="https://render.githubusercontent.com/render/math?math=P(w_i|w_1, \ldots, w_{i-1}) \approx P(w_i|w_{i-n+1}, \ldots, w_{i-1})" alt="">
* For some small <i>n</i>: 
  
    * When n = 1, it is a unigram model: <img src="https://render.githubusercontent.com/render/math?math=P(w_1, w_2, \ldots, w_m) = \prod_{i=1}^{m} P(w_i)">
      > <img src="001.png" alt="unigram_example" width=290 height=140>
      
    * When n = 2, it is a bigram model: <img src="https://render.githubusercontent.com/render/math?math=P(w_1, w_2, \ldots, w_m) = \prod_{i=1}^{m} P(w_i|w_{i-1})">
      > <img src="002.png" alt="bigram_example"  width=290 height=140>
      
    * When n = 3, it is a trigram model: <img src="https://render.githubusercontent.com/render/math?math=P(w_1, w_2, \ldots, w_m) = \prod_{i=1}^{m} P(w_i|w_{i-2}, w_{i-1})">
      > <img src="003.png" alt="trigram_example" width=290 height=140>

### Maximum Likelihood Estimation

* Estimate the probabilities based on counts in the corpus:
    * For unigram models:
      <img src="https://render.githubusercontent.com/render/math?math=P(w_i) = \frac{C(w_i)}{M}" alt="">
      
    * For bigram models:
      <img src="https://render.githubusercontent.com/render/math?math=P(w_i|w_{i-1}) = \frac{C(w_{i-1}, w_i)}{C(w_{i-1}})" alt="">

    * For n-gram models generally:
      <img src="https://render.githubusercontent.com/render/math?math=P(w_i|w_{i-n+1}, \ldots, w_{i-1}) = \frac{C(w_{i-n+1}, \ldots, w_i)}{C(w_{i-n+1}, \ldots, w_{i-1})}" alt="">

### Book-ending Sequences

* Special tags used to denote start and end of sequence:
    * `<s>` = sentence start
    * `</s>` = sentence end
    
### Problems with N-gram models

* Language has long distance effects, therefore large n required. 
  > The <span color="red">lecture/s</span> that took place last week <span color="red">was/were</span> on preprocessing