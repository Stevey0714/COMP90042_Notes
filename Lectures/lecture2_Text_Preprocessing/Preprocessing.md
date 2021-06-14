# Lecture 2 Text Preprocessing

### Some Definitions

* Words: Sequence of characters with a meaning and/or function
* Sentence: Sequence of words
* Document: One or more sentences
* Corpus: A collection of documents
* Word <span color="red">token</span>: Each instance of a word
* Word <span color="red">type</span>: Distinct words
* Lexicon ("dictionary"): A group of word types
* E.g.:
    * Sentence: `"The student is enrolled at the University of Melbourne."`
    * Word: 9 words in the sentence above: `["The", "student", "is", "enrolled", "at", "the", "University", "of", "Melbourne"]`
    * Word Token: 9 word tokens in the sentence above: `["the", "student", "is", "enrolled", "at", "the", "university", "of", "melbourne"]`
    * Word Type: 8 word types in the sentence above: `["the", "student", "is", "enrolled", "at", "university", "of", "melbourne"]`
  
### Reasons for Preprocessing

* Most NLP applications have documents as inputs
* Language is compositional(组合的). As humans, we can break these documents into individual components. To understand language, a computer should do the same
* Preprocessing is the first to break documents into individual components.

### Preprocessing Steps

1. Remove unwanted formatting. E.g. HTML tags
2. [Sentence Segmentation](#sentence_seg): Break documents into sentences
3. [Word Tokenization](#word_token): Break sentences into words
4. [Word Normalization](#word_norm): Transform words into canonical(标准的) forms  
5. [Stopword removal](#stopword_remove): delete unwanted words
* E.g. Sample Document: "<p>Hi there. I'm TARS.</p>"
  * Step 1: "Hi there. I'm TARS."
  * Step 2: ["Hi there.", "I'm TARS"]
  * Step 3: [["Hi", "there", "."], ["i", "'m", "TARS", "."]]
  * Step 4: [["hi", "there", "."], ["i", "am", "tars", "."]]
  * Step 5: [[], ["tars"]]
  
<h2 id="sentence_seg">Sentence Segmentation</h2>

### Sentence Segmentation

* Naïve approach: break on sentence punctuations. E.g. `[.?!]`
  * Problem: Some punctuations are used in abbreviations. E.g. "U.S. dollar", "Yahoo!"
* Second approach: User regex to require capital letter after the punctuations. E.g. `[.?!][A-z]`
  * Problem: Abbreviations for name also matching this case. E.g. "Mr.Brown"
* Better approach: Have lexicons(词典)
  * Problem: Difficult to enumerate all names and abbreviations
* State-of-the-Art approach: User machine learning, rather than rules

### Binary Classifier

* Looks at every "." and decides whether it is the end of a sentence.
  * Applicable models: decision trees, logistic regression
* Features:
  * Look at the words before and after "."
  * Word shapes:
    * Uppercase
    * Lowercase
    * ALL_CAPS
    * number
    * Character length
  * Part-of-speech tags: Determiners tend to start a sentence
  
<h2 id="word_token">Word Tokenization</h2>

### Word Tokenization: English

* Naïve approach: separate out alphabetic strings. E.g. `\w+`
  * Problem: There are words that has special punctuations or does not contain alphabetics:
    * Abbreviations: E.g. U.S.A
    * Hyphens: E.g. merry-go-round, well-respected, yes-but
    * Numbers: E.g. 10000.01
    * Dates: E.g. 3/1/2016
    * Clitics(附着词): E.g. can't 
    * Internet language: E.g. http://www.google.com, #metoo, :-)
    * Multiword units: E.g. New Zealand
  
### Word Tokenization: Chinese

* Some Asian languages are written without spaces between words.
* In Chinese, words often correspond to more than one character. 
* E.g 
```
墨大      的   学生           与众不同
Unimelb  's   students(are)  special
```
* Standard approach assumes an existing vocabulary
* MaxMatch algorithm: Greedily match the longest word in the vocabulary
  * E.g.  墨大的学生与众不同
  ```
  V = {墨，大，的，学，生，与，众，不，同，墨大，学生，不同，与众不同}
  MaxMatch process:
  1. match 墨大
  2. move to 的
  3. match 的
  4. move to 学
  5. match 学生
  6. move to 与
  7. match 与众不同
  8. done
  Result: 墨大｜的｜学生｜与众不同
  ```
  * Problem: We might not get all vocabulary accurately.
    * E.g. 
    ```
      Sentence: 去买新西兰花
      1. 去｜买｜新西兰｜花 -> go|buy|New Zealand|flowers
      2. 去｜买｜新｜西兰花 -> go|buy|new|broccoli
    ```

### Word Tokenization: German

* Lebensversicherungsgesellschaftsangestellter -> life insurance company employee
* Requires compound splitter

### Subword Tokenization

* Colorless green ideas sleep furiously -> `[color][less][green][idea][s][sleep][furious][ly]`
* One popular algorithm: byte-pair encoding(BPE)
  * Core idea: Iteratively merge frequent pairs of characters
  * Advantages:
    * Data-informed tokenization
    * Works for different languages
    * Deals better with unknown words
  
#### Byte-Pair Encoding

* E.g. 
  ```
  1. count all word frequencies in the corpus. [] means how many times the word appears in the corpus
  - [5] l o w _
  - [2] l o w e s t _
  - [6] n e w e r _
  - [3] w i d e r _
  - [2] n e w _
  
  2. First add all individual characters into the vocabulary:
  V = {_, d, e, i, l, n, o, r , s, t, w}
  
  3. Find the next most frequent byte-pair in the corpus:
    Next frequent is : r_ which appears in newer and wider in total of 6 + 3 = 9 times
    V = {_, d, e, i, l, n, o, r , s, t, w, r_}
  
  4. Continue to add the next most frequent byte-pair until all subwords are in the vocabulary:
    V = {_, d, e, i, l, n, o, r , s, t, w, r_, er_}
    V = {_, d, e, i, l, n, o, r , s, t, w, r_, er_, ew}
    V = {_, d, e, i, l, n, o, r , s, t, w, r_, er_, ew, new}
    V = {_, d, e, i, l, n, o, r , s, t, w, r_, er_, ew, new, lo}
    V = {_, d, e, i, l, n, o, r , s, t, w, r_, er_, ew, new, lo, low}
    V = {_, d, e, i, l, n, o, r , s, t, w, r_, er_, ew, new, lo, low, newer_}
    V = {_, d, e, i, l, n, o, r , s, t, w, r_, er_, ew, new, lo, low, newer_, low_}
    ... 
  ```
* In practice BPE will run with thousands of merges creating a large vocabulary
* Most frequent words will be represented as full words
* Rarer words will be broken into subwords
* In the word case, unknown words in the test data will be broken into individual letter

#### Disadvantage of Word Tokenization 

* Creates non-sensical subwords
* Generally large vocabulary, but size of vocabulary is controllable(less merges).

<h2 id="word_norm">Word Normalization</h2>

### Word Normalization

* Lower casing: Australia -> australia
* Removing morphology(词法): cooking -> cook
* Correcting spelling: definately -> definitely
* Expanding abbreviations: U.S.A -> USA
* Goal of word normalization:
  * Reduce vocabulary
  * Map words into the same type
  
### Inflectional Morphology(屈折词法)

* Inflectional morphology creates grammatical variants
* english inflects nouns, verbs, and adjectives:
  * Nouns: number of nouns (-s)
  * Verbs: number of the subjects (-s), the aspect (-ing) of the action, and the tense (-ed) of the action
  * Adjectives: comparatives (-er) and superlatives(-est)
* Many languages have much richer inflectional morphology than English
  * E.g. French inflects nouns for gender (un chat vs. une chatte)
  
### Lemmatization(词性还原)

* Lemmatization means removing any inflection to reach the uninflected form called the lemma
  * E.g. speaking -> speak
* In English, these are irregularities that prevent a trivial solution:
  * poked -> poke (not pok)
  * stopping -> stop (not stopp)
  * watches -> watch (not watche)
  * was -> be (not wa)
* A lexicon of lemmas needed for accurate lemmatization

### Derivational Morphology(派生词法)

* Derivational morphology creates distinct words
* English derivational <i>suffixes</i> often change the lexical category.
  * E.g.:
    * -ly: personal -> personally (adjectives -> adverbs)
    * -ize: final -> finalize (adjective -> transitive verbs)
    * -er: write -> writer (verb -> noun)
* English derivational <i>prefixes</i> often change the meaning without changing the lexical category.
  * E.g.:
    * write -> rewrite
    * healthy -> unhealthy
  
### Stemming(词干提取)

* Stemming strips off all suffixes, leaving a <i>stem</i>
  * E.g. automate, automatic, automation -> automat
  * Often not an actual lexical item
* Even less lexical sparsity than lemmatization
* Popular in information retrieval
* Stem not always interpretable

#### The Porter Stemmer

* The most popular stemmer for English
* Applies rewrite rules in stages:
  * First strip inflectional suffixes: E.g. -ies -> -i
  * Then derivatioal suffixes: E.g. -ization -> -ize -> i
* Notations:
  * c = consonant E.g. 'b', 'c', 'd'
  * v = vowel E.g. 'a', 'e', 'i', 'o', 'u'
  * C = a sequence of consonants E.g. 's', 'ss', 'tr', 'bl'
  * V = a sequence of vowels E.g. 'o', 'oo', 'ee', 'io'
* A word has one of the four forms:
  * CVCV ... C
  * CVCV ... V
  * VCVC ... C
  * VCVC ... V
* Therefore, a word can be represented as:
  \[C\]VCVC...\[V\] -> \[C\](VC)<sup>m</sup>\[V\] where m is the measure
  * E.g. 
    * Tree = C(tr)V(ee) = C(VC)<sup>0</sup>V
    * Trees = C(tr)V(ee)C(s) = C(VC)<sup>1</sup>
    * Troubles = C(tr)V(ou)C(bl)V(e)C(s) = C(VC)<sup>2</sup>
* Rules format: (condition) S1 -> S2
  * E.g. (m > 1) ement -> null: replacement -> replac
    * replac -> CVCVC = C(VC)<sup>2</sup> -> m = 2
* Always use the longest matching S1
  * E.g. 
    * Rules: sses -> ss, ies -> i, ss -> ss, s -> null
    * caresses -> caress
    * caress -> caress
    * cares -> care
* Algorithm:
  * Step 1: plurals and inflectional morphology
  * Step 2, 3, 4: derivational inflections
  * Step 5: Tidying up
* Examples: 
  * computational -> comput
    * Step 2: ational -> ate: computate
    * Step 4: ate -> null: comput
  * computer -> comput:
    * Step 4: er -> null: comput
  
### Fixing Spelling Errors

* Reasons:
  * Spelling errors create new, rare types
  * Disrupt various kinds of linguistic analysis
  * Very common in internet corpora
  * In web search, particularly important in queries
* Methods:
  * String distance (Levenshtein)
  * Modelling of error types (phonetic, typing, etc)
  * Use an n-gram language model
  
### Other Word Normalization

* Normalizing spelling variations
  * normalise -> normalize
  * U r so coool! -> you are so cool
* Expanding abbreviations
  * US, U.S. -> United States
  * imho -> in my humble opinion
  
<h2 id="stopword_remove">Stopword Removal</h2>

### Stopwords

* Definition: a list of words to be removed from the document
  * Typical in bag-of-word(BOW) representations
  * Not appropriate when sequence is important
* Choosing stopwords:
  * All <i>closed-class</i> or <i>function words</i>: E.g. the, a, of, for, he, ...
  * Any high frequency words
  * NLTK, spaCy NLP toolkits