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
    * Sentence: "The student is enrolled at the University of Melbourne."
    * Word: 9 words in the sentence above: ["The", "student", "is", "enrolled", "at", "the", "University", "of", "Melbourne"]
    * Word Token: 9 word tokens in the sentence above: ["the", "student", "is", "enrolled", "at", "the", "university", "of", "melbourne"]
    * Word Type: 8 word types in the sentence above: ["the", "student", "is", "enrolled", "at", "university", "of", "melbourne"]
  
### Reasons for Preprocessing

* Most NLP applications have documents as inputs
* Language is compositional(组合的). As humans, we can break these documents into individual components. To understand language, a computer should do the same
* Preprocessing is the first to break documents into individual components.

### Preprocessing Steps

1. Remove unwanted formatting. E.g. HTML tags
2. [Sentence Segmentation](#sentence_seg): Break documents into sentences
3. Word Tokenization: Break sentences into words
4. Word NormalizationL Transform words into canonical(标准的) forms  
5. Stopword removal: delete unwanted words
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
  
