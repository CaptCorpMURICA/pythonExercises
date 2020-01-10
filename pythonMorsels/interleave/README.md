# interleave
#### Assigned from Mixed Level on 01/06/2020

Hey!

See if you can figure out at least one of the bonuses for this week's exercise if you have time.

I want you to write a function that accepts two lists and returns a new iterable with each of the given items "interleaved" (item 0 from iterable 1, then item 0 from iterable 2, then item 1 from iterable 1, and so on).

Here's an example (which returns lists):
```
>>> interleave([1, 2, 3, 4], [5, 6, 7, 8])
[1, 5, 2, 6, 3, 7, 4, 8]
```
I want you to assume that the lists have the same length. Don't worry at all about what to do with different length lists. We'll actually handle that in a future exercise.

### Bonus 1

As a bonus, I'd like you to make your function accept any iterable, not just lists/sequences ✔️:
```
>>> nums = [1, 2, 3, 4]
>>> interleave(nums, (n**2 for n in nums))
[1, 1, 2, 4, 3, 9, 4, 16]
```
### Bonus 2

For a second bonus, return an iterator (for example a generator) from your interleave function instead of a list. ✔️
```
>>> interleave([1, 2, 3, 4], [5, 6, 7, 8])
<generator object interleave at 0x7f6174c18b48>
>>> list(interleave([1, 2, 3, 4], [5, 6, 7, 8]))
[1, 5, 2, 6, 3, 7, 4, 8]
```
This should allow your interleave function to accept infinitely long iterables (or other lazy iterables).

## Hints

If you **get stuck** this week, give these hints a try:

1. [Looping over multiple lists at the same time](https://treyhunner.com/2016/04/how-to-loop-with-indexes-in-python/#What_if_we_need_to_loop_over_multiple_things?)
2. [Bonus 2: Returning an iterator from a function](https://treyhunner.com/2018/06/how-to-make-an-iterator-in-python/#Generators:_the_easy_way_to_make_an_iterator)
3. [Bonus 2: An interesting tool for solving this](https://docs.python.org/2/library/itertools.html#itertools.chain.from_iterable)

## Tests

Automated tests for this week's exercise [can be found here](https://www.pythonmorsels.com/exercises/d2b9aaca98d845ccaf9905825955e473/tests/). You'll need to write your function in a module named interleave.py next to the test file. To run the tests you'll run "python test_interleave.py" and check the output for "OK". You'll see that there are some "expected failures" (or "unexpected successes" maybe). If you'd like to do the bonus, you'll want to comment out the noted lines of code in the tests file to test them properly.
