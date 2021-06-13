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