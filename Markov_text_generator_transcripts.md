**User**
write a program that takes the following input:
  filename: str
  start_word: str
   output_length: str
returns amount of text starting from start_word of length output_length depending on the frequencies of words present in the text
kind of like Markov chain
**ChatGPT**
Sure! Here's a Python program that takes the specified input and generates text of the specified length using a Markov chain-like approach based on word frequencies:

```python
Copy code
import random
from collections import defaultdict
import re

def generate_text(filename, start_word, output_length):
    # Read text from file
    with open(filename, 'r', encoding='utf-8') as file:
        text = file.read()

    # Preprocess text: remove punctuation and split into words
    words = re.findall(r'\b\w+\b', text.lower())

    # Create a dictionary to store word frequencies
    word_freq = defaultdict(int)
    for i in range(len(words) - 1):
        word_freq[(words[i], words[i+1])] += 1

    # Generate text
    output = [start_word]
    current_word = start_word
    for _ in range(int(output_length) - 1):
        next_word_candidates = [word for word in words if (current_word, word) in word_freq]
        if not next_word_candidates:
            break
        next_word = random.choices(next_word_candidates, weights=[word_freq[(current_word, w)] for w in next_word_candidates])[0]
        output.append(next_word)
        current_word = next_word

    return ' '.join(output)

# Example usage:
filename = 'your_text_file.txt'
start_word = 'the'
output_length = 100
generated_text = generate_text(filename, start_word, output_length)
print(generated_text)
```
Make sure to replace 'your_text_file.txt' with the path to your text file. This program reads the text from the file, preprocesses it, computes word frequencies, and generates text based on the Markov chain-like approach, starting from the specified start word and producing text of the specified length.

**User**
can you implement it like a model

2 / 2

**ChatGPT**
Sure! I can provide a simple implementation of a Python class that encapsulates this functionality. Here's how you could structure it:

```python
Copy code
import random
from collections import defaultdict
import re

class MarkovTextGenerator:
    def __init__(self, filename):
        self.filename = filename
        self.word_freq = defaultdict(int)
        self.words = []

    def _preprocess_text(self):
        with open(self.filename, 'r', encoding='utf-8') as file:
            text = file.read()
        self.words = re.findall(r'\b\w+\b', text.lower())

    def _compute_word_frequencies(self):
        for i in range(len(self.words) - 1):
            self.word_freq[(self.words[i], self.words[i+1])] += 1

    def generate_text(self, start_word, output_length):
        if not self.words:
            self._preprocess_text()
        if not self.word_freq:
            self._compute_word_frequencies()

        output = [start_word]
        current_word = start_word
        for _ in range(int(output_length) - 1):
            next_word_candidates = [word for word in self.words if (current_word, word) in self.word_freq]
            if not next_word_candidates:
                break
            next_word = random.choices(next_word_candidates, weights=[self.word_freq[(current_word, w)] for w in next_word_candidates])[0]
            output.append(next_word)
            current_word = next_word

        return ' '.join(output)

# Example usage:
filename = 'your_text_file.txt'
markov_generator = MarkovTextGenerator(filename)
start_word = 'the'
output_length = 100
generated_text = markov_generator.generate_text(start_word, output_length)
print(generated_text)
```
This implementation defines a class MarkovTextGenerator with methods to preprocess the text, compute word frequencies, and generate text based on the Markov chain-like approach. You can create an instance of this class with the filename of your text file and then generate text using the generate_text method.

**User**
add another parameter chain_length
which predicts next word based on the chain of words (previous)
**ChatGPT**
Certainly! Here's the modified implementation of the MarkovTextGenerator class with an additional parameter chain_length:

```python
Copy code
import random
from collections import defaultdict
import re

class MarkovTextGenerator:
    def __init__(self, filename):
        self.filename = filename
        self.word_freq = defaultdict(int)
        self.words = []

    def _preprocess_text(self):
        with open(self.filename, 'r', encoding='utf-8') as file:
            text = file.read()
        self.words = re.findall(r'\b\w+\b', text.lower())

    def _compute_word_frequencies(self, chain_length):
        for i in range(len(self.words) - chain_length):
            chain = tuple(self.words[i:i+chain_length])
            next_word = self.words[i+chain_length]
            self.word_freq[(chain, next_word)] += 1

    def generate_text(self, start_words, output_length, chain_length):
        if not self.words:
            self._preprocess_text()
        if not self.word_freq:
            self._compute_word_frequencies(chain_length)

        output = list(start_words)
        current_chain = tuple(start_words)
        for _ in range(int(output_length) - len(start_words)):
            next_word_candidates = [word for word in self.words if (current_chain, word) in self.word_freq]
            if not next_word_candidates:
                break
            next_word = random.choices(next_word_candidates, weights=[self.word_freq[(current_chain, w)] for w in next_word_candidates])[0]
            output.append(next_word)
            current_chain = tuple(output[-chain_length:])

        return ' '.join(output)

# Example usage:
filename = 'your_text_file.txt'
markov_generator = MarkovTextGenerator(filename)
start_words = ['the', 'quick']
output_length = 100
chain_length = 2
generated_text = markov_generator.generate_text(start_words, output_length, chain_length)
```
print(generated_text)
This implementation extends the MarkovTextGenerator class with the chain_length parameter. It now computes word frequencies based on chains of words of the specified length and generates text accordingly. You can specify the chain_length when calling the generate_text method.