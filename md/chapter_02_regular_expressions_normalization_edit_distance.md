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

- `[]` : square brackets called a **disjunction**, matches any one of the single characters in contains
- `[a-z]`, `[A-Z]`, `[0-9]`: all specify a range of possible single character matches
- `[^]` : using a caret as the first character in the square bracket specifies a not
- `?` : outside of square brackets, refers to 0 or 1 of the preceding letter
- `*` : zero or more of the preceding character
- `+` : one or more of the preceding character
- `.` : the wildcard, matching any single character
- `^`+`$` : anchors denoting start and end of the string, remember power before money
- `\b` : matches a word boundary, i.e. where a sequence of digits, underscores, or letters ends
- `|` : pipe/disjunction is the or operator
- `()` : groups expression
- `{n,m}` : curly brackets can be used to define a specific number or range of repetitions
- `()` + `\1` : can rereference a captured group by referring to it's index
- `(?:)` : makes group uncaptured
- `(?= pattern)` : is true is the pattern occurs but the match pointer doesn't advance
- `(?! pattern)` : returns true if a pattern does not match and again the doesn't advance the cursor


```python
from IPython.core.display import display, Markdown, HTML
```


```python
import pandas as pd
```


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
    (r'([a-z]{3}) the \1', 'dog the dog and hog the frog but no hog the dog')
]
```


```python
results = []
for regex, example in examples:
    results.append({
        'pattern':regex, 
        'example': example, 
        'matches': re.findall(regex, example)}
    )
display(HTML(pd.DataFrame(results).to_html()))
```


<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>pattern</th>
      <th>example</th>
      <th>matches</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>[a]</td>
      <td>aaa</td>
      <td>[a, a, a]</td>
    </tr>
    <tr>
      <th>1</th>
      <td>[a-z]</td>
      <td>ABc</td>
      <td>[c]</td>
    </tr>
    <tr>
      <th>2</th>
      <td>[^a-z]</td>
      <td>ABc</td>
      <td>[A, B]</td>
    </tr>
    <tr>
      <th>3</th>
      <td>chainsaws?</td>
      <td>chainsaw chainsaws, chainsawss</td>
      <td>[chainsaw, chainsaws, chainsaws]</td>
    </tr>
    <tr>
      <th>4</th>
      <td>the\b</td>
      <td>the other</td>
      <td>[the]</td>
    </tr>
    <tr>
      <th>5</th>
      <td>([a-z]{3}) the \1</td>
      <td>dog the dog and hog the frog but no hog the dog</td>
      <td>[dog]</td>
    </tr>
  </tbody>
</table>


## Words

Deciding what counts as a word can depend heavily on the downstream task. For topic modeling or text classification punctuation may not play a big role, however things like sentiment analysis, speech to text, or parsing linguistic features may rely on such information. Furthermore, in speech to text, one might encounter words like *uh* or *um* which may or may not be useful.

Further complications can be things like the casing of a word, *they* versus *They* or the inflection of words like *cats* verus *cat*.  A **lemma** refers to sets of inflected word forms which share the same stem, part of speech and word sense. In contrast **word form** is the inflected form, the natural variations of words that come up in language.

The english language is rather forgiving in this sense, because a given **lemma** tends to have a limited number of **word forms**. In contrast languages like **Arabic** or **Turkish** rely on a large variety of **word forms** for any given **lemma**. This can be more challenging because if these tokens are treated indepently, a model has to recover the relationship between a wide array possible **word forms**.

One phenomena that comes up in english corpora is called **Herdan's Law** or **Heap's Law** which implies that the  size of the vocabulary for a text goes up significantly fast than the square root of its length in words. Namely for `V` the vocabulary (i.e. unique words) of a corpora, where the corpora consists of `N` tokens (the individual, non-unique words of a text) the following holds:

```
|V| = kN^b
```
 where `k` and `b` are positive constants and `0 < b < 1` (usually ~ 0.65-0.75). This pattern will come up when we experiment with tokenization in the next section.
 
 

## Tokenization

Strictly speaking, things like tokenization can be done via hand written regexp and bash scripts, however there now exist libraries like spaCy which make the process of tokenization and extraction of linguistic features much easier. In addition, to overcome limitations of more complex languages and their morphological features, sub-word tokenization approaches have also been developed.
