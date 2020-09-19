# Chapter 2 
## Regular Expressions, Text Normalization and Edit Distance

Regular expressions are useful in analysing data. Their use is not limited to only NLP, often we want to extract specific values or patterns from a document. Furthermore this tool is often supplied in many common commandline tools as a means to search the file system and its contents.

Turning to NLP their value centers around tasks we will call **text normalization** and **tokenization**. **Text normalization** as its name implies refers to tasks or tricks used by nlp practitioners to normalize written text into some standard form. This can be not only things such as fixing spelling errors, correcting numbers to all use the same formatting, and making all nouns singular but also **sentence segmentation** (splitting text to sentences).

**Tokenization** the task of splitting words is intuitive to humans, however describing the exceptions, or errors which appear in real world data in terms of a regular expression are difficult. Examples of such exceptions are the following:

    - New York
    - rock'n'roll
    - emoticons: ':)'
    - hashtags: #blm
    
As shown in the text, regular expressions can also be used to parse text in a predictable format and extract subjects, however these types of applications are very rigid and labor intensive.

Another task in **text normalization** is **lemmatization** and it's simplified counterpart **stemming**. **Lemmatization** is the process of mapping words to their roots, for example *sings*, *sang*, and *sung* all share the root *sing*. While english is one of the most morphologically simplest languages, many other languages allow words to adapt to not only tense, but gender, person, etc. making the task of mapping a word to its **lemma** much more labor intensive. To this end, **stemming** is a pragmatic approach which removes suffixes from words, in an attempt to strip them to some common subword.

## Regular Expressions

Regex has a specialized syntax to allow one to specify complex string patterns.

The fundamental parts are:

- [] : square brackets called a **disjunction**, matches any one of the single characters in contains
- [a-z], [A-Z], [0-9]: all specify a range of possible single character matches
- [^] : using a caret as the first character in the square bracket specifies a not
- ? : outside of square brackets, refers to 0 or 1 of the preceding letter
- * : zero or more of the preceding character
- + : one or more of the preceding character
- . : the wildcard, matching any single character
- ^/$ : anchors denoting start and end of the string, remember power before money
- \b : matches a word boundary, i.e. where a sequence of digits, underscores, or letters ends
- | : pipe/disjunction is the or operator
- () : groups expression
- {n,m} : curly brackets can be used to define a specific number or range of repetitions
- () + \1 : can rereference a captured group by referring to it's index
- (?:) : makes group uncaptured
- (?= pattern) : is true is the pattern occurs but the match pointer doesn't advance
- (?! pattern) : returns true if a pattern does not match and again the doesn't advance the cursor


```python
import re
```


```python
examples = [
    ('[a]', 'aaa'),
    ('[a-z]', 'ABc'),
    ('[^a-z]', 'ABc'),
    ('chainsaws?', 'chainsaw chainsaws, chainsawss'),
    (r'the\b', 'the other'),#must preprend r otherwise interpreted as backspace
    (r'([a-z]{3}) the \1', 'dog the dog and hog the hog but dont hog the dog')
]
```


```python
for regex, example in examples:
    print(regex, example, re.findall(regex, example))
```

    [a] aaa ['a', 'a', 'a']
    [a-z] ABc ['c']
    [^a-z] ABc ['A', 'B']
    chainsaws? chainsaw chainsaws, chainsawss ['chainsaw', 'chainsaws', 'chainsaws']
    the\b the other ['the']
    ([a-z]{3}) the \1 dog the dog and hog the hog ['dog', 'hog']



```python
re.findall('a', 'aaaaa')
```




    ['a', 'a', 'a', 'a', 'a']




```python

```
