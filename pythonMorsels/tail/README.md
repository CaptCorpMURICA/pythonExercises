# tail
#### Assigned from Mixed Level on 07/01/2019
Hi friend!

This week I want you to make a function that takes a sequence (like a list, string, or tuple) and a number `n` and returns the last n elements from the given sequence, as a list.

For example:
```
>>> tail([1, 2, 3, 4, 5], 3)
[3, 4, 5]
>>> tail('hello', 2)
['l', 'o']
>>> tail('hello', 0)
[]
```
### Bonus 1

As a bonus, make your function return an empty list for negative values of n ✔️:
```
>>> tail('hello', -2)
[]
```
### Bonus 2

As a second bonus, make sure your function works with any iterable, not just sequences. ✔️ For example:
```
>>> squares = (n**2 for n in range(10))
>>> tail(squares, 3)
[49, 64, 81]
```
You should make sure you don't loop at all if `n` is `0` or negative.

See if you can make your function relatively memory efficient (if you're looping over a very long iterable, don't store the entire thing in memory).

## Hints

1. [Getting the last n items from a sequence](https://www.youtube.com/watch?v=ajrtAuDg3yw&feature=youtu.be&t=469)
2. [Turning iterables into lists](https://treyhunner.com/2019/05/python-builtins-worth-learning/#list)
3. [Bonus 1: conditionals in Python](http://anh.cs.luc.edu/handsonPythonTutorial/ifstatements.html#simple-if-statements)
4. [How to loop over any iterable in Python](https://treyhunner.com/2019/06/loop-better-a-deeper-look-at-iteration-in-python/#Generators_are_iterators)
5. [A data structure that could help with bonus 2](https://pymotw.com/3/collections/deque.html#constraining-the-queue-size)

## Tests

Automated tests for this week's exercise [can be found here](https://www.pythonmorsels.com/exercises/83ed2a27b86b41a185036b262fb67c41/tests/). You'll need to write your function in a module named tail.py next to the test file. To run the tests you'll run "python test_tail.py" and check the output for "OK". You'll see that there are some "expected failures" (or "unexpected successes" maybe). If you'd like to do the bonus, you'll want to comment out the noted lines of code in the tests file to test them properly.
