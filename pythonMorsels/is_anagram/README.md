# is_anagram
#### Assigned from Mixed Level on 10/07/2019
Hey friend!

When solving this week's exercise, don't look back at your previous answers and avoid searching on the Internet until you get to the bonus. I'd like to make sure you struggle on this one. See if you can think up at least a couple different ways to solve it.

I want you to write a function that accepts two strings and returns True if the two strings are anagrams of each other.

Your function should work like this:
```
>>> is_anagram("tea", "eat")
True
>>> is_anagram("tea", "treat")
False
>>> is_anagram("sinks", "skin")
False
>>> is_anagram("Listen", "silent")
True
```
Make sure your function works with mixed case.

Before you try to solve any bonuses, I recommend you try to find at least two ways to solve this problem.

Okay now to the bonuses...

### Bonus 1

For the first bonus, make sure your function ignores spaces ✔️:
```
>>> is_anagram("coins kept", "in pockets")
True
```
### Bonus 2

For a second bonus, make sure your function also ignores punctuation ✔️:
```
>>> is_anagram("a diet", "I'd eat")
True
```
### Bonus 3

If you solved this one really quickly, here's a more challenging third bonus for you: try allowing accented latin1 characters but ignoring the accent marks. ✔️
```
>>> is_anagram("cardiografía", "radiográfica")
True
```
## Hints

Hints for when you get stuck (hover over links to see what they're about):

1. [Counting the number of times each letter occurs](https://stackoverflow.com/a/46486682/2633215)
2. [Setting the default value of a key in a dictionary](https://docs.quantifiedcode.com/python-anti-patterns/correctness/not_using_setdefault_to_initialize_a_dictionary.html)
3. [A variety of different ways to count the number of times each word is seen](https://treyhunner.com/2015/11/counting-things-in-python/)
4. [A different way to solve the base problem](https://stackoverflow.com/a/15046263/2633215)
5. [Comparing strings case-insensitively](https://stackoverflow.com/a/319437/2633215)
6. [Ignoring spaces during comparisons](https://stackoverflow.com/a/8270146/2633215)
7. [Removing punctuation characters from strings](https://stackoverflow.com/a/34214187/2633215)
8. [Removing accents from characters](https://stackoverflow.com/a/14682498/2633215)

## Tests

Automated tests for this week's exercise [can be found here](https://www.pythonmorsels.com/exercises/33b4af3082734d00bc62effc8da16375/tests/). You'll need to write your function in a module named anagram.py next to the test file. To run the tests you'll run "python test_anagram.py" and check the output for "OK". You'll see that there are some "expected failures" (or "unexpected successes" maybe). If you'd like to do the bonus, you'll want to comment out the noted lines of code in the tests file to test them properly. You'll see those noted in the test file.

You'll receive some answers and links to resources explaining ways to solve this exercise within a few days. Don't peek at the answers before attempting to solve this on your own.
