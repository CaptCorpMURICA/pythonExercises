# count_words
#### Assigned from Mixed Level on 09/16/2019
Hey!

When solving this week's exercise, make sure to hold off on searching directly for the answer on Google/StackOverflow.

This is a fairly general exercise and there are a number of answers to it. I'd like you to struggle to come to an answer or two (or five?) on your own.

I want you to write a function that accepts a string and returns a mapping (a dictionary or dictionary-like structure) that has words as the keys and the number of times each word was seen as the values.

Your function should work like this:
```
>>> count_words("oh what a day what a lovely day")
{'oh': 1, 'what': 2, 'a': 2, 'day': 2, 'lovely': 1}
>>> count_words("don't stop believing")
{"don't": 1, 'stop': 1, 'believing': 1}
```
### Bonus 1

As a bonus, make sure your function works well with mixed case words:
```
>>> count_words("Oh what a day what a lovely day")
{'oh': 1, 'what': 2, 'a': 2, 'day': 2, 'lovely': 1}
```
### Bonus 2

For even more of a bonus try to get your function to ignore punctuation outside of words:
```
>>> count_words("Oh what a day, what a lovely day!")
{'oh': 1, 'what': 2, 'a': 2, 'day': 2, 'lovely': 1}
```
## Hints

Hints for when you get stuck (hover over links to see what they're about):

1. [Getting a list of words from a sentence](https://stackoverflow.com/a/13734966/2633215)
2. [Checking existence of a given word (key) in dictionary](https://docs.python.org/3/tutorial/datastructures.html#dictionaries)
3. [Initializing a key to a unique default value in a dictionary](https://stackoverflow.com/a/9359011/2633215/)
4. [Various techniques for counting words in a sentence](https://treyhunner.com/2015/11/counting-things-in-python/)
5. [Ignoring punctuation marks while extracting words](https://stackoverflow.com/a/37543765/2633215)

## Tests

Automated tests for this week's exercise [can be found here](https://www.pythonmorsels.com/exercises/9af6665915964ee7ba19fe3d762d9ca8/tests/). You'll need to write your function in a module named count.py next to the test file. To run the tests you'll run "python test_count.py" and check the output for "OK". You'll see that there are some "expected failures" (or "unexpected successes" maybe). If you'd like to do the bonus, you'll want to comment out the noted lines of code in the tests file to test them properly. You'll see those noted in the test file.

You'll receive some answers and links to resources explaining ways to solve this exercise within a few days. Don't peek at the answers before attempting to solve this on your own.
