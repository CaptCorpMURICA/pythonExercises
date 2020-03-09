# window
##### Assigned from Mixed Level on 01/27/2020
Hi there,

This week I'd like you to write a function returns "windows" of items from a given list. Your function should takes a list and a number `n` and return a new list of tuples, each containing "windows" of `n` consecutive items. That is, each tuple should contain the current item and the `n-1` items after it.

Here are some examples:
```
>>> numbers = [1, 2, 3, 4, 5, 6]
>>> window(numbers, 2)
[(1, 2), (2, 3), (3, 4), (4, 5), (5, 6)]
>>> window(numbers, 3)
[(1, 2, 3), (2, 3, 4), (3, 4, 5), (4, 5, 6)]
>>> window(numbers, 4)
[(1, 2, 3, 4), (2, 3, 4, 5), (3, 4, 5, 6)]
```
Your window function should return an empty list if the given `n` is 0. It should also be able to accept strings, tuples and other sequences.

This week's problem is challenging. I recommend solving the base problem before either of the bonuses this week.

### Bonus 1

As a bonus, make sure your function accepts any iterables, not just sequences. ✔️

For example your function should accept iterators and other lazy iterables:
```
>>> numbers = [1, 2, 3, 4, 5, 6]
>>> squares = (n**2 for n in numbers)
>>> window(squares, 3)
[(1, 4, 9), (4, 9, 16), (9, 16, 25), (16, 25, 36)]
```
### Bonus 2

For a second bonus, make sure your function returns an iterator instead of a list. ✔️
```
>>> numbers = [1, 2, 3, 4, 5, 6]
>>> next(window(numbers, 3))
(1, 2, 3)
```
## Hints

Hints for **when you get stuck** (hover over links to see what they're about):

1. [Looping with indexes in Python](https://treyhunner.com/2016/04/how-to-loop-with-indexes-in-python/#What_if_we_need_indexes?)
2. [Looping over multiple sequences at a time](https://treyhunner.com/2019/05/python-builtins-worth-learning/#zip)
3. [A queue-like data stricture](https://docs.python.org/3/library/collections.html#collections.deque)
4. [The iterator protocol (what iterators are)](http://treyhunner.com/2016/12/python-iterator-protocol-how-for-loops-work/)
5. [Creating your own iterators](https://treyhunner.com/2018/06/how-to-make-an-iterator-in-python/)

## Tests

Automated tests for this week's exercise [can be found here](https://www.pythonmorsels.com/exercises/4c2a9fe5bcbb49e69f2fabb03a178bee/tests/). You'll need to write your function in a module named window.py next to the test file. To run the tests you'll run "python test_window.py" and check the output for "OK". You'll see that there are some "expected failures" (or "unexpected successes" maybe). If you'd like to do the bonus, you'll want to comment out the noted lines of code in the tests file to test them properly.
