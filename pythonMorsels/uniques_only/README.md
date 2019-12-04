# uniques_only
#### Assigned from Mixed Level on 12/02/2019

Hiya!

See if you can figure out at least one of the bonuses for this week's exercise if you have time.

I want you to write a function that accepts an iterable and returns a new iterable with all items from the original iterable except for duplicates.

Here's an example:
```
>>> uniques_only([1, 2, 2, 1, 1, 3, 2, 1])
[1, 2, 3]
>>> nums = [1, -3, 2, 3, -1]
>>> squares = (n**2 for n in nums)
>>> uniques_only(squares)
[1, 9, 4]
```
### Bonus 1

As a bonus, return an iterator (for example a generator) from your `uniques_only` function instead of a list. This should allow your `uniques_only` function to accept infinitely long iterables (or other lazy iterables). ✔️

### Bonus 2

Here's another bonus to do after you've made your `uniques_only` function return a lazy iterable: allow your `uniques_only` function to work with [unhashable](https://docs.python.org/3/glossary.html#term-hashable) objects. ✔️

For example when two lists with equal items are provided, they should be seen as duplicates:
```
>>> list(uniques_only([['a', 'b'], ['a', 'c'], ['a', 'b']]))
[['a', 'b'], ['a', 'c']]
```
### Bonus 3

For an extra bonus, make sure your function works efficiently with hashable items while still accepting unhashable items. ✔️

This one is a little harder to test. There's an automated test (included below) that attempts to performance-test your function.

## Hints

Hints for **when you get stuck** (hover over links to see what they're about):

1. [Removing duplicates from a list](https://stackoverflow.com/q/480214/2633215)
2. [Some clever duplicate-removing code](https://twitter.com/raymondh/status/944125570534621185)
3. [Making a function that returns an iterator](https://treyhunner.com/2018/06/how-to-make-an-iterator-in-python/)
4. [What does hashable mean](https://docs.python.org/3/glossary.html#term-hashable)
5. [Determining whether an item is hashable](https://stackoverflow.com/a/3460747/2633215)
6. [Another way to determine hashabiltiy](https://stackoverflow.com/a/3460725/2633215)

## Tests

Automated tests for this week's exercise [can be found here](https://www.pythonmorsels.com/exercises/b684cd595ca745398131f2252ca13064/tests/). You'll need to write your function in a module named `uniques.py` next to the test file. To run the tests you'll run `python test_uniques.py` and check the output for `OK`. You'll see that there are some `expected failures` (or `unexpected successes` maybe). If you'd like to do the bonus, you'll want to comment out the noted lines of code in the tests file to test them properly.
