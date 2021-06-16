# Lecture 7 Deep Learning for NLP: Feedforward Networks

<h2 id="NN_basics"> Feedforward Neural Networks Basics</h2>

### Deep Learning

* A branch of machine learning

* Re-branded name for neural networks

* Deep: Many layers are chained together in modern deep learning models

* Neural Networks: Historically inspired by the way computation works in the brain
    * Consists of computation units called neurons
    
### Feedforward Neural Network

* Also called multilayer perceptron
* E.g. of architecture:
    > <img src="001.png" alt="" width=688 height=776>
  
* Each arrow carries a weight, reflecting its importance
* Certain layers have non-linear activation functions

### Neuron

* Each neuron is a function:
    * Given input `x`, compute real-value `h`: 
    
    > <img src="https://render.githubusercontent.com/render/math?math=h = tanh(\sum_{j}w_jx_j %2B b)" alt=""><br>
    > <img src="002.png" alt="" width=486 height=494>
  
    * Scales input (with weights, `w`) and adds offset (bias, `b`)
    * Applies non-linear function, such as logistic sigmoid, hyperbolic sigmoid(tanh), or rectified linear unit
    * `w` and `b` are parameters of the model
    
* Typically hava several hidden units. E.g. <img src="https://render.githubusercontent.com/render/math?math=h_i = tanh(\sum_{j}w_{ij}x_j %2B b_i)" alt="">
  
    * Each with its own weights w<sub>i</sub>, and bias terms b<sub>i</sub>
      
    * Can be expressed using matrix and vector operators: <img src="https://render.githubusercontent.com/render/math?math=\vec{h} = tanh(W\vec{x} %2B \vec{b}" alt="">
    
    * Where <img src="https://render.githubusercontent.com/render/math?math=W" alt=""> is a matrix comprising the weight vectors and <img src="https://render.githubusercontent.com/render/math?math=\vec{b}" alt=""> is a vector of all bias terms
    
    * Non-linear function applied element-wise
    
### Output Layer

* For binary classification problem: Sigmoid Activation Function

* Multi-class classification problem: Softmax Activation Function ensures probabilities are greater than 0 and sum to 1: <img src="https://render.githubusercontent.com/render/math?math=[\frac{exp(v_1)}{\sum_iexp(v_i)}, \frac{exp(v_2)}{\sum_iexp(v_i)}, \ldots, \frac{exp(v_m)}{\sum_iexp(v_i)}]" alt="">

### Optimization

* Consider how well the model fits the training data, in terms of the probability it assigns to the correct output
  
    * <img src="https://render.githubusercontent.com/render/math?math=L = \prod_{i=0}^mP(y_i|x_i)" alt="">
    
    * Want to maximize total probability `L`
    
    * Equivalently minimize `-log L` with respect to parameters
    
* Trained using gradient descent
    * Tools like <i>tensorflow</i>, <i>pytorch</i>, <i>dynet</i> use autodiff to compute gradients automatically
    
### Regularization

* Have many parameters, so the model overfits easily

* Low bias, high variance

* Regularization is very import in neural networks

* L1-norm: sum of absolute values of all parameters

* L2-norm: sum of squares of all parameters

* Dropout: randomly zero-out some neurons of a layer
    * If a dropout rate = 0.1, a random 10% of neurons now have 0 values
    > <img src="003.png" alt="" width=676 height=738>
    * Can apply dropout to any layer, but in practice, mostly to the hidden layers
  
<h2 id="fnn_application">Applications in NLP</h2>

### Topic Classification

* Given a document, classify it into a predefined set of topics. E.g. economy, politics, sports

* Input: Bag-of-words 
  > Example input:<br>
  > <img src="004.png" alt="" width=640 height=220>
  
* Training: 
  > Architecture:<br>
  > <img src="005.png" alt="" width=620 height=702><br>
  > Hidden Layers:<br>
  > <img src="https://render.githubusercontent.com/render/math?math=\vec{h_1} = tanh(W_1\vec{x} %2B \vec{b_1})" alt=""><br>
  > <img src="https://render.githubusercontent.com/render/math?math=\vec{h_2} = tanh(W_2\vec{h_1} %2B \vec{b_2})" alt=""><br>
  > <img src="https://render.githubusercontent.com/render/math?math=\vec{y} = softmax(W_3\vec{h_2})" alt=""><br>
  
  * Randomly initialize `W` and `b`
  * E.g:
    > Input: <img src="https://render.githubusercontent.com/render/math?math=\vec{x} = [0, 2, 3, 0]" alt=""><br>
    > Output: <img src="https://render.githubusercontent.com/render/math?math=\vec{y} = [0.1, 0.6, 0.3]" alt="">: Probability distribution over <img src="https://render.githubusercontent.com/render/math?math=C_1, C_2, C_3" alt=""><br>
    > Loss: <img src="https://render.githubusercontent.com/render/math?math=L = -log(0.1)" alt=""> if true label is <img src="https://render.githubusercontent.com/render/math?math=C_1" alt=""><br>
  
* Prediction:
  * E.g. 
    > Input: <img src="https://render.githubusercontent.com/render/math?math=\vec{x} = [1, 3, 5, 0]" alt=""><br>
    > Output: <img src="https://render.githubusercontent.com/render/math?math=\vec{y} = [0.2, 0.1, 0.7]" alt=""><br>
    > Predicted class is <img src="https://render.githubusercontent.com/render/math?math=C_3" alt=""><br>

* Potential Improvements:
  * Use Bag of bigrams as input
  * Preprocess text to lemmatize words and remove stopwords
  * Instead of raw counts, can weight words using TF-IDF or indicators

### Language Model as Classifiers

* Language Models can be considered simple classifiers. 
  * E.g. For a trigram model: <img src="https://render.githubusercontent.com/render/math?math=P(w_i|w_{i-2} = salt, w_{i-1} = and" alt=""> classifies the likely next word in a sequence, given `salt` and `and`
  
* Feedforward Neural Network Language Model
  * Use neural network as a classifier to model
  * Input features: The previous words
  * Output classes: the next word
  * Use Word Embeddings to represent each word
    * E.g. of Word Embeddings: 
      > <img src="006.png" alt="" width=600 height=30>
      
### Word Embeddings

* Maps discrete word symbols to continuous vectors in a relatively low dimensional space

* Word embeddings allow the model to capture similarity between words

* In feed-forward neural network language model, the first layer is the sum of input word embeddings

### Training a Feed-Forward Neural Network Language Model

* E.g. <img src="https://render.githubusercontent.com/render/math?math=P(w_i = grass|w_{i-3} = a, w_{i-2} = cow, w_{i-1} = eats)" alt="">

* Lookup word embeddings (<img src="https://render.githubusercontent.com/render/math?math=W_1" alt="">) for `a`, `cow`, and `eats`

* Concatenate them and feed it to the network: 
  * <img src="https://render.githubusercontent.com/render/math?math=\vec{x} = \vec{v_a} \oplus \vec{v_cow} \oplus \vec{v_eats}" alt="">
  
  * <img src="https://render.githubusercontent.com/render/math?math=\vec{h} = tanh(W_2\vec{x} %2B \vec{b_1})" alt="">
  
  * <img src="https://render.githubusercontent.com/render/math?math=\vec{y} = softmax(W_3\vec{h})" alt="">
  
* <img src="https://render.githubusercontent.com/render/math?math=\vec{y}" alt=""> gives the probability distribution over all words in the vocabulary
  * E.g. 
  > <img src="007.png" alt="" width=736 height=108><br>
  > <img src="https://render.githubusercontent.com/render/math?math=P(w_i = grass | w_{i-3}=a, w_{i-2}=cow, w_{i-1}=eats) = 0.8" alt="">

* Loss: <img src="https://render.githubusercontent.com/render/math?math=L = -log(0.8)" alt="">

* Most parameters are in the word embeddings W<sub>1</sub> (size = d * |V|) and the output embeddings W<sub>3</sub> (size = |V| * d)

* Example Architecture: 
  > <img src="008.png" alt="" width=700 height=450><br>
  
* Problem of Count-based N-gram models:
  * Cheap to train
  * Problems with sparsity and scaling to larger contexts
  * Don't adequately capture properties of words
  
* Advantages of Feed-Forward Neural Network Language Model:
  * Automatically capture word properties, leading to more robust estimates
  
### Feed-Forward Neural Network for POS Tagging

* POS tagging can also be framed as classification:
  * <img src="https://render.githubusercontent.com/render/math?math=P(t_i|w_{i-1}=cow, w_i=eats)" alt=""> -> classifies the likely POS tag for `eats`
  
* Feed-Forward Neural Network Language Model architecture can be adapted to the task directly

* Inputs:
  * recent words: <img src="https://render.githubusercontent.com/render/math?math=w_{i-2}, w_{i-1}, w_i" alt="">
  * recent tags: <img src="https://render.githubusercontent.com/render/math?math=t_{i-2}, t_{i-1}" alt="">
  
* Outputs:
  * current tags: <img src="https://render.githubusercontent.com/render/math?math=t_i" alt="">
  
* Frame as neural network with:
  * 5 inputs: 3 word embeddings and 2 tag embeddings
  * 1 output: vector of size |T|, using softmax
  
* Training to minimize: <img src="https://render.githubusercontent.com/render/math?math=-\sum__ilogP(t_i|w_{i-2}, w_{i-1}, w_i, t_{i-2}, t_{i-1})" alt="">

* Example Architecture:
  > <img src="009.png" alt="" width=800 height=425><br>