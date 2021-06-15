# Lecture 4 Text Classification

<h2 id="fund_classification">Fundamentals of Classification</h2>

### Classification

* Input:
    * A document d: Often represented as a vector of features
    * A fixed output set of classes C = {c<sub>1</sub>, c<sub>2</sub>, ..., c<sub>k</sub>}: Categorical, not continuous or ordinal
    
* Output: 
    * A predicted class c &isin; C
    
### Text Classification Tasks

* Some common examples:
    * Topic classification
    * Sentiment analysis
    * Native-language identification
    * Natural language interence
    * Automatic fact-checking
    * Paraphrase
    
* Input may not be a long document

#### Topic Classification

* Motivation: Library science, information retrieval
* Classes: Topic categories E.g. "jobs", "international news"
* Features:
    * Unigram bag-of-words, with stop-words removed
    * Longer n-grams for phrases
    
* Examples of corpora:
    * Reuters news corpus. E.g. RCV1, NLTK
    * Pubmed abstracts
    * Tweets with hashtags
    
#### Sentiment Analysis

* Motivation: Opinion mining, business analytics
* Classes: Positive/Negative/(Neutral)
* Features:
    * N-grams
    * Polarity lexicons(极性词词典)
    
* Examples of corpora:
    * Movie review dataset in NLTK
    * SEMEVAL Twitter polarity datasets
    
#### Native-Language Identification

* Motivation: Forensic linguistics, educational applications
* Classes: First language of author
* Features:
    * Word N-grams
    * Syntactic patterns (POS, parse trees)
    * Phonological features
    
* Example of corpora:
    * TOEFL/IELTS essay corpora
    
#### Natural Language Inference

* Also called textual entailment
* Motivation: Language understanding
* Classes: entailment, contradiction, neutral
* Features:
    * Word overlap
    * Length difference between the sentences
    * N-grams
    
* Examples of corpora:
    * SNLI, MNLI
    
### Building a Text Classifier
1. Identify a task of interest
2. Collect an appropriate corpus
3. Carry out annotation
4. Select features
5. Choose a machine learning algorithm
6. Train model and tune hyperparameters using hold-out development data
7. Repeat earlier steps as needed
8. Train fianl model
9. Evaluate model on hold-out test data

<h2 id="classification_algorithm">Algorithms for Classification

### Choosing a Classification Algorithm

* Bias vs. Variance
    * Bias: Assumptions made in the model
    * Variance: Sensitivity to training set
    
* Underlying assumptions E.g. Independence
* Complexity
* Speed

### Naive Bayes

* Find the class with the highest likelihood under Bayes Law:
    <img src="https://render.githubusercontent.com/render/math?math=P(C|F) \propto P(F|C)P(C)" alt="placeholder">
    
    * Probability of the class times probability of features given the class
    
* Naively assumes features are independent:
    <img src="https://render.githubusercontent.com/render/math?math=P(c_n|f_1, \ldots, f_m) = \prod_{i=1}^{m}P(f_i|c_n)P(c_n)" alt="placeholder">

* Pros:
    * Fast to train and classify
    * Robust, low-variance -> good for low data situations
    * Optimal classifier if independence assumption is correct
    * Extremely simple to implement
    
* Cons:
    * Independence assumption rarely holds
    * Low accuracy compared to similar methods in most situations
    * Smoothing required for unseen class/feature combinations
    
### Logistic Regression

* A classifier, even called regression
* A linear model, but users softmax function squashing to get valid probability
    <img src="https://render.githubusercontent.com/render/math?math=P(c_n|f_1, \ldots, f_m) = \frac{1}{Z} * exp(\sum_{i=0}^{m}w_if_i)" alt="placeholder">

* Training maximizes probability of training data subject to regularization which encourages low or sparse weights
* Pros:
    * Unlike Naive Bayes not confounded by diverse, correlated features gives better performance
    
* Cons:
    * Slow to train
    * Feature scaling needed
    * Requires a lot of data to work well in practice
    * Choosing regularization strategy is important since overfitting is a big problem
    
### Support Vector Machines

* Finds hyperplane which separates the training data with maximum margin
* Pros:
    * Fast and accurate linear classifier
    * Can do non-linearity with kernel trick
    * Works well with huge feature sets
    
* Cons:
    * Multiclass classification awkward
    * Feature scaling needed
    * Deals poorly with class imbalances
    * Interpretability 
    
### K-Nearest Neighbor

* Classify based on majority class of <i>k</i>-nearest training examples in feature space

* Definition of the nearest can vary:
    * Euclidean distance
    * Cosine distance
    
* Pros:
    * Simple but surprisingly effective
    * No training required
    * Inherently multiclass
    * Optimal classifier with infinite data
    
* Cons:
    * Have to select k
    * Issues with imbalanced classes
    * Often slow for finding the neighbors
    * Features must be selected carefully
    
### Decision Tree

* Construct a tree where nodes correspond to tests on individual features
* Leaves are final class decisions
* Based on greedy maximization of mutual information
* Pros:
    * Fast to build and test
    * Feature scaling irrelevant
    * Good for small feature sets
    * Handles non-linearly-separable problems
    
* Cons:
    * In practice, not very interpretable
    * Highly redundant sub-trees
    * Not competitive for large feature sets
    
### Random Forests

* An ensemble classifier
* Consists of decision trees trained on different subsets of the training and feature space
* Final class decision is majority voting of sub-classifiers
* Pros:
    * Usually more accurate and more robust than decision trees
    * Great classifier for medium feature sets
    * Training easily parallelized
    
* Cons:
    * Interpretability
    * Slow with large feature sets
    
### Neural Networks

* An interconnected set of nodes typically arranged in layers
* Input layer(features), output layer(class probabilities), and one or more hidden layers
* Each node performs a linear weighting of its inputs from previous layer, passes result through activation function to nodes in next layer
* Pros:
    * Extremely powerful, dominant method in NLP and computer vision
    * Little feature engineering
    
* Cons:
    * Not an off-the-shelf classifier
    * Many hyperparameters, difficult to optimize
    * Slow to train
    * Prone to overfitting
    
### Hyperparameter Tuning

* Dataset for tuning:
    * Development set
    * Not the training set or the test set
    * k-fold cross-validation
    
* Specific hyper-parameters are classifier specific. But many hyper-parameters relate to regularization
    * Regularization hyperparameters penalize model complexity 
    * Used to prevent overfitting
    
* For multiple hyperparameters, use grid search

<h2 id="evaluation">Evaluation</h2>

### Confusion Matrix

<table>
<tr>
<th></th>
<th colspan="2">Classified As</th>
</tr>
<tr>
<td>Class</td>
<td>A</td>
<td>B</td>
</tr>
<tr>
<td>A</td>
<td>True Positive</td>
<td>False Positive</td>
</tr>
<tr>
<td>B</td>
<td>False Negative</td>
<td>True Negative</td>
</tr>
</table>

### Evaluation Metrics

* Accuracy = True Positive / (True Positive + False Positive + True Negative + False Negative)
* Precision = True Positive / (True Positive + False Positive)
* Recall = True Positive / (True Positive + False Negative)
* F1-score = (2 * precision * recall) / (precision + recall)
  * Macroaverage: Average F-score across classes
  * Micreaverage: Calculate F-score using sum of counts