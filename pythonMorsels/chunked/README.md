# chunked
##### Assigned from Mixed Level on 02/03/2020
Hello!

I'd like you to write a function that accepts a **sequence** and a number n and returns one list-of-lists containing lists of size `n`.

It should work something like this:
```
>>> for chunk in chunked([1, 2, 3, 4, 5], n=2):
...     print(*chunk)
...
1 2
3 4
5
>>> for chunk in chunked(range(10), 4):
...     print(tuple(chunk))
...
(0, 1, 2, 3)
(4, 5, 6, 7)
(8, 9)
```
Try to solve this exercise without using any third-party libraries (without using more-`itertools` or `boltons` for example).

Before attempting any bonuses, I'd like you to put some effort into figuring out the clearest and most idiomatic way to solve this problem.

There are three bonuses this week.

### Bonus 1

For the first bonus, your `chunked` function should accept any iterable
```
>>> squares = (n**2 for n in range(6))
>>> for chunk in chunked(squares, 3):
...     print(*chunk)
...
0 1 4
9 16 25
```
### Bonus 2

For the second bonus, your `chunked` function should return [an iterator](https://treyhunner.com/2018/06/how-to-make-an-iterator-in-python/).
```
>>> squares = (n**2 for n in range(6))
>>> chunks = chunked(squares, 3)
>>> tuple(next(chunks))
(0, 1, 4)
>>> next(squares)
9
```
The passed in iterable shouldn't be fully looped over immediately, but only consumed bit-by-bit as the returned iterator is looped over.

### Bonus 3

For the third bonus, your `chunked` function should accept an optional `fill` keyword argument that, when specified, will ensure the last chunk is length `n`:
```
>>> for chunk in chunked(range(10), 4, fill=0):
...     print(*chunk)
...
0 1 2 3
4 5 6 7
8 9 0 0
```
The `fill` argument should only be accepted as a keyword argument, meaning it cannot be accepted positionally:
```
>>> chunked(range(10), 4, 0)
Traceback (most recent call last):
  File "<stdin>", line 1, in <module>
TypeError: chunked() takes 2 positional arguments but 3 were given
```
## Hints

1. [Counting as you loop](https://treyhunner.com/2019/05/python-builtins-worth-learning/#range)
2. [A syntax for getting a chunk from a sequence](https://stackoverflow.com/questions/509211/understanding-slice-notation)
3. [Creating a new list from an existing one](https://treyhunner.com/2015/12/python-list-comprehensions-now-in-color/)
4. [Bonus 1: Python's loops don't use indexes](https://treyhunner.com/2019/06/loop-better-a-deeper-look-at-iteration-in-python/#Python%E2%80%99s_for_loops_don%E2%80%99t_use_indexes)
5. [Bonus 1: Manually looping over an iterable](https://treyhunner.com/2016/12/python-iterator-protocol-how-for-loops-work/#Iterables_&_Iterators)
6. [Bonus 1: Grabbing the next n items](https://stackoverflow.com/a/32172654/2633215)
7. [Bonus 2: Returning an iterator](https://treyhunner.com/2018/06/how-to-make-an-iterator-in-python/)
8. [Bonus 3: keyword-only arguments](https://treyhunner.com/2018/04/keyword-arguments-in-python/#Keyword-only_arguments_without_positional_arguments)
9. [Bonus 3: creating a unique default value for fill](https://treyhunner.com/2019/03/unique-and-sentinel-values-in-python/#Creating_unique_non-None_placeholders:_why_object()?)

## Tests

Automated tests for this week's exercise [can be found here](https://www.pythonmorsels.com/exercises/22c939576be74a74a66d42f7e50d2c9e/tests/). You'll need to write your code in a module named chunked.py next to the test file. To run the tests you'll run "python test_chunked.py" and check the output for "OK". You'll see that there are some "expected failures" (or "unexpected successes" maybe). If you'd like to do the bonus, you'll want to comment out the noted lines of code in the tests file to test them properly.
