# multimax
#### Assigned from Mixed Level on 10/14/2019

Hi friend!

This week I want you to write a function that takes an iterable and returns all maximum values found in the given iterable.

By "all maximum values" I mean that if there are multiple values in a list that are all seen as maximums, then a list of all the values should be returned. If there is just one maximum value, then a list with the single value should be returned.

For example:
```
>>> multimax([1, 2, 4, 3])
[4]
>>> multimax([1, 4, 2, 4, 3])
[4, 4]
>>> multimax((1, 1, 1))
[1, 1, 1]
```
For the first bonus, I want you to make sure your multimax function returns an empty list if the given iterable is empty: ✔️
```
>>> multimax([])
[]
```
For the second bonus, I want you to make sure your multimax function will work with iterators (lazy iterables) such as files, zip objects, and generators ✔️:
```
>>> numbers = [1, 3, 8, 5, 4, 10, 6]
>>> odds = (n for n in numbers if n % 2 == 1)
>>> multimax(odds)
[5]
```
For the third bonus, I want you to make your multimax function accept a keyword argument called "key" that is a function which will be used to determine the key by which to compare values as maximums. For example the key function could be used to find the longest words in a list of words ✔️:
```
>>> words = ["cheese", "shop", "ministry", "of", "silly", "walks", "argument", "clinic"]
>>> multimax(words, key=len)
['ministry', 'argument']
```
Automated tests for this week's exercise [can be found here](https://www.pythonmorsels.com/exercises/b8edf79165344da29933573d9f2813a7/tests/). You'll need to write your function in a module named multimax.py next to the test file. To run the tests you'll run "python test_multimax.py" and check the output for "OK". You'll see that there are some "expected failures" (or "unexpected successes" maybe). If you'd like to do the bonus, you'll want to comment out the noted lines of code in the tests file to test them properly.

Once you've written a solution for the exercise, [submit your solution](https://www.pythonmorsels.com/exercises/b8edf79165344da29933573d9f2813a7/submit/) to *track your progress!*
