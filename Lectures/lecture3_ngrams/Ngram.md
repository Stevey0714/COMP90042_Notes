# Lecture 3 N-gram Language Models

<h2 id="language_model">Language Models</h2>

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

<h2 id="ngram">N-gram Language Model</h2>

### Probabilities: Joint to Conditional

* Goal of language model is to get a probability for an arbitrary sequence of <i>m</i> words:   
  <img src="https://render.githubusercontent.com/render/math?math=P(w_1, w_2, \ldots, w_m)">

* First step is to apply the chain rule to convert joint probabilities to conditional ones:
  <img src="https://render.githubusercontent.com/render/math?math=P(w_1, w_2, \ldots, w_m) = P(w_1)P(w_2|w_1)P(w_3|w_1, w_2) \ldots P(w_m|w_1, \ldots, w_{m-1})">
  
### The Markov Assumption

* <img src="https://render.githubusercontent.com/render/math?math=P(w_1)P(w_2|w_1)P(w_3|w_1, w_2) \ldots P(w_m|w_1, \ldots, w_{m-1})" alt=""> is still intractable, so make a simplifying assumption:
    <img src="https://render.githubusercontent.com/render/math?math=P(w_i|w_1, \ldots, w_{i-1}) \approx P(w_i|w_{i-n%2B1}, \ldots, w_{i-1})" alt="">
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
      <img src="https://render.githubusercontent.com/render/math?math=P(w_i|w_{i-n%2B1}, \ldots, w_{i-1}) = \frac{C(w_{i-n%2B1}, \ldots, w_i)}{C(w_{i-n%2B1}, \ldots, w_{i-1})}" alt="">

### Book-ending Sequences

* Special tags used to denote start and end of sequence:
    * `<s>` = sentence start
    * `</s>` = sentence end
    
### Problems with N-gram models

* Language has long distance effects, therefore large n required. 
  > The <span color="red">lecture/s</span> that took place last week <span color="red">was/were</span> on preprocessing
  * The "was/were" here is mentioning "lecture/s" which is 6 words ahead. Therefore need a 6-grams
    
* Resulting probabilities are often very small
    * Possible solution: Use log probability to avoid numerical underflow
    
* Unseen words:
    * Special symbol to represent. E.g. `<UNK>`
    
* Unseen n-grams: Because the opertaion is multiplication, if one term in the multiplication is 0 then whole probability is 0
    * Need to smooth the n-gram language model
    
<h2 id="smoothing">Smoothing</h2>

### Smoothing

* Basic idea: give events you have never seen before some probability
* Must be the case that <img src="https://render.githubusercontent.com/render/math?math=P(everything) = 1" alt="">
* Many different kinds of smoothing
    * [Laplacian(add-one) smoothing](#add-one)
    * [Add-k smoothing](#add-k)
    * [Absolute discounting](#absolute_discount)
    * [Katz Backoff](#katz)
    * Kneser-Ney

<h3 id="add-one">Laplacian(add-one) smoothing</h3>

* Simple idea: pretend we have seen each n-gram once more than we did.
* For unigram models:
    <img src="https://render.githubusercontent.com/render/math?math=P_{add1}(w_i) = \frac{C(w_i)%2B1}{M%2B|V|}" alt="">
  
* For bigram models:
  <img src="https://render.githubusercontent.com/render/math?math=P_{add1}(w_i|w{i-1}) = \frac{C(w_{i-1}, w_i)%2B1}{C(w_{i-1})%2B|V|}" alt="">

<h3 id="add-k">Add-k smoothing</h3>

* Adding one is often too much. Instead, add a fraction k.
* Also called Lidstone Smoothing
  <img src="https://render.githubusercontent.com/render/math?math=P_{addk}(w_i|w_{i-1}, w_{i-2}) = \frac{C(w_{i-2}, w_{i-1}, w_i)%2Bk}{C(w_{i-2}, w_{i-1})%2Bk|V|}" alt="">

* Have to choose k

<h3 id="absolute_discount">Absolute Discounting</h3>

* Borrows a fixed probability mass from observed n-gram counts
* Redistributes it to unseen n-grams

<h3 id="katz">Katz Backoff</h3>

* Absolute discounting redistributes the probability mass equally for all unseen n-grams
* Katz Backoff: redistributes the mass based on a lower order model (e.g. Unigram)
  > <img src="004.png" alt="unigram_example" width=750 height=280>

