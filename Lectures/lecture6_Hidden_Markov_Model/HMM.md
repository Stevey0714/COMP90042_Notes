# Lecture 6 Sequence Tagging: Hidden Markov Models

### Problems with POS Tagging

* Exponentially many combinations: |Tags|<sup>M</sup>, for length M

* Tag sequences of different lengths

* Tagging is a sentence-level task but as humans we decompose it into small word-level tasks

* Solution:
    * Define a model that decomposes process into individual word-level tasks steps. But this takes into account the whole sequence when learning and predicting.
    * This is called sequence labelling, or structured prediction
    
### Probabilistic Model of HMM

* Goal: Obtain best tag sequence <b>t</b> from sentence <b>w</b>
    > The formulation: <img src="https://render.githubusercontent.com/render/math?math=\hat{t} = argmax_tP(t|w)" alt=""><br>
    > Applying Bayes Rule: <img src="https://render.githubusercontent.com/render/math?math=\hat{t} = argmax_t\frac{P(w|t)P(t)}{P(w)} = argmax_tP(w|t)P(t)" alt=""><br>
    > Decomposing the Elements:<br>
    >> Probability of a word depends only on the tag: <img src="https://render.githubusercontent.com/render/math?math=P(w|t) = \prod_{i=1}^{n}P(w_i|t_i)" alt=""><br>
    >> Probability of a tag depends only on the previous tag: <img src="https://render.githubusercontent.com/render/math?math=P(t) = \prod_{i=1}^{n}P(t_i|t_{i-1})" alt="">

### Two Assumptions of HMM

* Output independence: An observed event(word) depends only on the hidden state(tag) -> <img src="https://render.githubusercontent.com/render/math?math=\prod_{i=1}^{n}P(w_i|t_i)" alt="">

* Markov assumption: The current state(tag) depends only on the previous state -> <img src="https://render.githubusercontent.com/render/math?math=\prod_{i=1}^{n}P(t_i|t_{i-1})" alt="">

### Training HMM

* Parameters are individual probabilities:
    * Emission Probabilities (O): <img src="https://render.githubusercontent.com/render/math?math=P(w_i|t_i)" alt="">
    * Transition Probabilities (A): <img src="https://render.githubusercontent.com/render/math?math=P(t_i|t_{i-1})" alt="">
    
* Training uses Maximum Likelihood Estimation: Done by simply counting word frequencies according to their tags.

* E.g.
    * <img src="https://render.githubusercontent.com/render/math?math=P(like|VB) = \frac{count(VB, like)}{count(VB)}" alt="">
    
    * <img src="https://render.githubusercontent.com/render/math?math=P(NN|DT) = \frac{count(DT, NN)}{count(DT)}" alt="">
  
* The tag for the first word:
    * Assume there is a `<s>` symbol at the start of the sentence
    * E.g. <img src="https://render.githubusercontent.com/render/math?math=P(NN|<s>) = \frac{count(<s>, NN)}{count(<s>)}" alt="">
    
* Unseen `(word, tag)` and `(tag, previous_tag) ` combinations: Applying smoothing techniques

* Output:
    * Transition Matrix: 
    > <img src="001.png" alt="generation" width=700 height=290>
    
    * Emission(Observation) Matrix:
    > <img src="002.png" alt="generation" width=700 height=290>
  
### Making Predictions using HMM (Decoding)

<img src="https://render.githubusercontent.com/render/math?math=\hat{t} = argmax_tP(w|t)P(t) = argmax_t\prod_{i=1}^{n}P(w_i|t_i)P(t_i|t_{i-1})" alt="">

* Simple idea: For each word, take the tag that maximizes <img src="https://render.githubusercontent.com/render/math?math=P(w_i|t_i)P(t_i|t_{i-1})" alt="">. Do it left-to-right greedily
  
* However this is wrong. The goal is to find <img src="https://render.githubusercontent.com/render/math?math=argmax_t" alt="">, not individual <img src="https://render.githubusercontent.com/render/math?math=argmax_{t_i}" alt=""> terms.
  
* Correct way: Consider all possible tag combinations, evaluate them, take the max.

### Viterbi Algorithm

* Use Dynamic Programming.
    * We can still proceed sequentially but need to be careful. 
    
* POS tag: `can play`
    
* Best tag for `can` is: <img src="https://render.githubusercontent.com/render/math?math=argmax_tP(can|t)P(t|<s>)" alt="">
    
* Suppose best tag for `can` is `NN`. To get the tag for `play`, we can take <img src="https://render.githubusercontent.com/render/math?math=argmax_tP(play|t)P(t|NN)" alt="">, but this is wrong
    
* Instead, we keep track of <b>scores for each tag</b> for `can` and check them with the different tags for `play`

* E.g.
  > <img src="003.gif" alt="generation" width=750 height=462>
  
* Complexity: O(T<sup>2</sup>N), where `T` is the size of the tagset, and `N` is the length of the sequence.
  * `T * N` matrix, each cell performs T operations
  
* Viterbi Algorithm works because of the independence assumptions that decompose the problem

* PsuedoCode:
```python
alpha = np.zeros(M, T)
for t in range(T):
    alpha[1, t] = pi[t] * O[w[1], t]

for i in range(2, M):
    for t_i in range(T):
        for t_last in range(T):
            s = alpha[i-1, t_last] * A[t_last, t_i]
            if s > alpha[i, t_i]:
                alpha[i, t_i] = s
                back[i, t_i] = t_last
best = np.max(alpha[M-1, :])
return backtrace(best, back)
```

* Good practices:
  * Work with log probabilities to prevent underflow
  * Vectorization (User matrix-vector operations)
  
### HMMs in Practice

* Examples previously are based on bigrams called first order HMM

* State-of-the-art model use tag trigams called second order HMM
  * <img src="https://render.githubusercontent.com/render/math?math=P(t) = \prod_{i=1}^{n}P(t_i|t_{i-1}, t_{i-2})" alt="">
  * Viterbi is now O(T<sup>3</sup>N)
  
* Need to deal with sparsity: Some tag trigram sequences might not be present in training data
  * Use interpolation: <img src="https://render.githubusercontent.com/render/math?math=P(t_i|t_{i-1}, t_{i-2}) = \lambda_3\hat{P}(t_i|t_{i-1}, t_{i-2}) %2B \lambda_2\hat{P}(t_i|t_{i-1}) %2B \lambda_1\hat{P}(t_i)" alt="">
  
  * where <img src="https://render.githubusercontent.com/render/math?math=\lambda_1 %2B \lambda_2 %2B \lambda_3 = 1" alt="">
  
* With additional features, HMM model can reach 96.5% accuracy on Penn Treebank

### Generative vs. Discriminative Taggers

* HMM is generative: <img src="https://render.githubusercontent.com/render/math?math=\hat{T} = argmax_TP(T|W) = argmax_TP(W|T)P(T) = argmax_T\prod_{i}P(w_i|t_i)P(t_i|t_{i-1})" alt="">
  
  * Training HMM can generate data (sentences)
  * Allows for unsupervised HMMs: Learn model without any tagged data
  
* Discriminative models describe <img src="https://render.githubusercontent.com/render/math?math=P(T|W)" alt=""> directly
  * <img src="https://render.githubusercontent.com/render/math?math=\hat{T} = argmax_TP(T|W) = argmax_T\prod_iP(t_i|w_i, t_{i-1})" alt="">
  
  * Supports richer feature set, generally better accuracy when trained over large supervised datasets: <img src="https://render.githubusercontent.com/render/math?math=\hat{T} = argmax_TP(T|W) = argmax_T\prod_iP(t_i|w_i, t_{i-1}, x_i, y_i)" alt="">
  
  * E.g. Maximum Entropy Markov Model (MEMM), Conditional Random Field (CRF)
  
  * Most deep learning models of sequences are discriminative