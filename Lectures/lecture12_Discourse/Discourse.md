# Lecture 12 Discourse

### Discourse

* Most tasks/models we learned operate at word or sentence level:
    * POS tagging
    * Language models
    * Lexical/distributional semantics
    
* But NLP often deals with documents

* Discourse: Understanding how sentences relate to each other in a document

<h2 id="discourse_seg">Discourse Segmentation</h2>

### Discourse Segmentation

* A document can be viewed as a sequence of segments

* A segment: a span of cohesive text

* Cohesion: organized around a topic or function

### Unsupervised Approaches

* TextTiling algorithm: Looking for points of low lexical cohesion between sentence

* For each sentence gap:
    * Create two BOW vectors consisting of words from `k` sentences on either side of gap
    * Use cosine to get a similarity score `sim` for two vectors
    * For gap `i`, calculate a depth score, insert boundaries when depth is greater than some threshold `t`
    * <img src="https://render.githubusercontent.com/render/math?math=depth(gap_i) = (sim_{i-1} - sim_i) + (sim_{i %2B 1} - sim_i)" alt="">
    
* E.g.: 
    > <img src="001.png" alt="" width=600 height=310><br>
    > `k = 1, t = 0.9`
  
### Supervised Approaches

* Get labelled data from easy sources:
    * Scientific publications
    * Wikipedia articles
    
* Apply a binary classifier to identify boundaries. Or use sequential classifiers. 

* Potentially include classification of section types

* Integrate a wider range of features:
    * Distributional semantics
    * Discourse markers
    
<h2 id='discourse_par'>Discourse Parsing</h2>

### Discourse Analysis

* Identify discourse units, and the relations that hold between them

* Rhetorical Structure Theory is a framework to do hierarchical analysis of discourse structure in documents

### Discourse Units

* Typically clauses of a sentence 

* Discourse Units do not cross sentence boundary

* E.g. \[It does have beautiful scenery,\] | \[some of the best since Lord of the Rings\]

* 2 merged DUs = another composite DU

### Discourse Relations

* Relations between discourse units:
    * Conjunction, justify, concession, elaboration
    
* E.g.
    > <img src="002.png" alt="" width=600 height=140>
  
### Nucleus vs. Satellite

* Within a discourse relation, one argument is the nucleus (the primary argument).

* The supporting argument is the satellite
    > <img src="003.png" alt="" width="600" height="120">
  
* Some relations are equal (conjunction), and so both arguments are nuclei
    > <img src="004.png" alt="" width="600" height="150">
  
### RST Tree

* An RST relation combines two or more DUs into composite DUs
* Process of combining DUs is repeated creating an RST Tree
* E.g.
  > <img src="005.png" alt="" width="600" height="750">
  
### RST Parsing

* Task: given a document, recover the RST tree
* Three approaches:
  * Rule-based parsing
  * Bottom-up approach
  * Top-down approach
  
### Rule-based Parsing: Parsing Using Discourse Markers
* Some discourse markers explicitly indicate relations:
  * although, but, for example, in other words, so, because, in conclusion
  
* Can be used to build a simple rule-based parser

* Problems:
  * Many relations are not marked by discourse marker
  * Many discourse marker ambiguous
  
### Parsing Using Machine Learning

* RST Discourse Treebank: 300+ documents annotated with RST trees

* Basic idea:
  * Segment document into DUs
  * Combine adjacent DUs into composite DUs iteratively to create the full RST 
  
### Bottom-up Parsing

* [Transition-based parsing](#Lecture16): Greedy, uses shift-reduce algorithm

* [CYK/chart parsing algorithm](#Lecture14): Global, but some constraints prevent CYK from finding globally optimal tree for discourse parsing

### Top-down Parsing

1. Segment documents into DUs

2. Decide a boundary to split into 2 segments

3. For each segment, repeat step 2

### Discourse Parsing Features

* Bag of words
* Discourse markers
* Starting and ending n-grams
* Location in the text
* Syntax features
* Lexical and distributional similarities

### Applications of Discourse Parsing

* Summarization
* Sentiment analysis
* Argumentation
* Authorship attribution
* Essay scoring

<h2 id="anaphora_resolution">Anaphora Resolution</h2>

### Anaphors

* Anaphor(照应语): Linguistic expressions that refer back to earlier elements in the text

* Anaphor have a antecedent in the discourse, often but not always a noun phrase
  * E.g. 
    > <img src="006.png" alt="" width="600" height="70">
    
* Pronouns are the most common anaphor
* Motivation: Essential for deep semantic analysis
  * Very useful for question answering and reading comprehension
  
### Antecedent Restrictions
* Pronouns must agree in number with their antecedents
  > <img src="007.png" alt="" width="600" height="70">
  
* Pronouns must agree in gender with their antecedents
  > <img src="008.png" alt="" width="600" height="70">
  
* Pronouns whose antecedents are the subject of the same syntactic clause must be reflexive
  > <img src="009.png" alt="" width="600" height="120">
  
### Antecedent Preferences

* The antecedents of pronouns should be recent
  > <img src="010.png" alt="" width="600" height="130">
  
* The antecedent should be salient, as determined by grammatical position:
  * Subject > object > argument of preposition
  > <img src="011.png" alt="" width="600" height="65">
  
### Entities and Reference
<img src="012.png" alt="" width="800" height="240">

* Discourse 16.1 is more coherent
* Pronouns all refer to John consistently, the protagonist

### Centering Theory

* A unified account of relationship between discourse structure and entity reference
* Every utterance in the discourse is characterized by a set of entities, known as centers
* Explain preference of certain entities for ambiguous pronouns

<h3> For an Utterance U<sub>n</sub></h3>

<img src="013.png" alt="" width="400" height="150">

* Forward-looking centers: 
  * All entities in U<sub>n</sub>: <img src="https://render.githubusercontent.com/render/math?math=C_f(U_n) = [e_1, e_2, \ldots]" alt="">
  * <img src="https://render.githubusercontent.com/render/math?math=C_f(16.1a) = [John, music store, piano]" alt="">
  * Ordered by syntactic prominence: subjects > objects
  
* Backward-looking center:
  * Highest ranked forward-looking center in previous utterance
  * Candidate entities in 16.1 b = `[John, music store]`
  * <img src="https://render.githubusercontent.com/render/math?math=C_b(16.1b) = [John]" alt="">
  * Not `music store` because `John` has a higher rank in previous utterance's forward-looking centers
  
### Centering Algorithm

* When resolving entity for anaphora resolution, choose the entity such that top forward-looking center matches with the backward-looking center

* Because the text reads more fluent when this condition is satisfied

### Supervised Anaphor Resolution

* Build a binary classifier for anaphor/antecedent pairs

* Convert restrictions and preferences into features:
  * Binary features for number/gender compatibility
  * Position of antecedent in text
  * Include features about type of antecedent
  
* With enough data, can approximate the centering algorithm

* But also easy to include features that are potentially helpful.
  * Words around anaphor/antecedent