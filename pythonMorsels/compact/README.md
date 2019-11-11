# compact
##### Assigned from Mixed Level on 06/24/2019
Hi!

For this week's exercise I want you to write a function that accepts a sequence (a list for example) and returns a new iterable (anything you can loop over) with adjacent duplicate values removed.

For example:
```
>>> compact([1, 1, 1])
[1]
>>> compact([1, 1, 2, 2, 3, 2])
[1, 2, 3, 2]
>>> compact([])
[]
```
There are two bonuses for this exercise.

I recommend solving the exercise without the bonuses first and then attempting each bonus separately.

### Bonus 1

For the first bonus, make sure you accept any iterable as an argument, not just a sequence (which means you can't use index look-ups in your answer). ✔️

Here's an example with a generator expression, which is a lazy iterable:
```
>>> compact(n**2 for n in [1, 2, 2])
[1, 4]
```
### Bonus 2

As a second bonus, make sure you return an iterator from your compact function instead of a list. ✔️
```
>>> c = compact(n**2 for n in [1, 2, 2])
>>> iter(c) is c
True
```
This should allow your compact function to accept infinitely long iterables (or other lazy iterables).

## Hints

Hints for when you get stuck (hover over links to see what they're about):

1. [Looping with indexes in Python](https://treyhunner.com/2016/04/how-to-loop-with-indexes-in-python/)
2. [A solution for the base problem](https://stackoverflow.com/a/49182346/2633215)
3. [Using a sentinel value while looping](https://treyhunner.com/2019/03/unique-and-sentinel-values-in-python/#Other_cases_for_non-None_placeholders)
4. [How to create iterators](https://treyhunner.com/2018/06/how-to-make-an-iterator-in-python/)
5. [An interesting solution](https://stackoverflow.com/a/41511571/2633215)

## Tests

Automated tests for this week's exercise [can be found here](https://www.pythonmorsels.com/exercises/02e33401dd3b427d8d379311eac666ac/tests/). You'll need to write your function in a module named compact.py next to the test file. To run the tests you'll run "python test_compact.py" and check the output for "OK". You'll see that there are some "expected failures" (or "unexpected successes" maybe). If you'd like to do the bonus, you'll want to comment out the noted lines of code in the tests file to test them properly.
