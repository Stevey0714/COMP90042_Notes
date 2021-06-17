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