# Month
#### Assigned from Mixed Level on 01/13/2020

Hi there,

This week I'd like you to create a `Month` class which represents a specific month in a specific year.
```
>>> dec99 = Month(1999, 12)
>>> dec99
Month(1999, 12)
>>> print(dec99)
1999-12
```
`Month` objects should have two different string representations (see above). They should also be comparable to each other using equality and ordering operators:
```
>>> sorted([Month(1998, 12), Month(2000, 1), Month(1999, 12)])
[Month(1998, 12), Month(1999, 12), Month(2000, 1)]
```
`Month` objects _should not_ be comparable to tuples, lists, `date` objects, or other non-`Month` objects (just as tuples and lists are not comparable to each other).
```
>>> Month(1998, 12) < (1998, 12)
Traceback (most recent call last):
  File "<stdin>", line 1, in <module>
TypeError: '<' not supported between instances of 'Month' and 'tuple'
```
If you have time to add more features to your `Month` class, I have three bonuses for you.

### Bonus 1

For the first bonus, I'd like your `Month` object to have `first_day` and `last_day` attributes which hold the `datetime.date` object representing the first and last days of the month:
```
>>> dec99 = Month(1999, 12)
>>> dec99.first_day
datetime.date(1999, 12, 1)
>>> dec99.last_day
datetime.date(1999, 12, 31)
```
### Bonus 2

For the second bonus, I'd like your `Month` object to have a factory method for creating `Month` objects from `datetime.date` objects and an instance method for converting `Month` objects to a specific string representation (using the same [strftime](http://strftime.org/) syntax as `datetime.date` objects).
```
>>> from datetime import date
>>> nye99 = date(1999, 12, 31)
>>> dec99 = Month.from_date(nye99)
>>> dec99
Month(1999, 12)
>>> dec99.strftime('%b %Y')
Dec 1999
```
### Bonus 3

For the third bonus, your `Month` objects should be immutable, hashable, and memory efficient (you shouldn't have a `__dict__` for each `Month` object):
```
>>> dec99 = Month(1999, 12)
>>> dec99.year = 1998
Traceback (most recent call last):
  File "<stdin>", line 1, in <module>
AttributeError: can't set attribute
>>> {Month(1999, 12), dec99, Month(2000, 1)}
{Month(1999, 12), Month(2000, 1)}
```
## Hints

Hints for **when you get stuck** (hover over links to see what they're about):

1. [Customizing string representations](https://www.youtube.com/watch?v=5cvM-crlDvg)
2. [An alternative to string concatenation](https://www.youtube.com/watch?time_continue=652&v=nghuHvKLhJA&feature=emb_title)
3. [Comparing multiple attributes at once](https://treyhunner.com/2019/03/python-deep-comparisons-and-code-readability/#Deep_ordering)
4. [Ensuring operators only work on Month objects](https://docs.python.org/3/library/constants.html#NotImplemented)
5. [One approach to the base problem](https://youtu.be/epKegvx_Jws?t=87)
6. [A hint if you're on Python 3.7+](https://docs.python.org/3/library/dataclasses.html)
7. [One way to solve the base problem quickly](https://youtu.be/epKegvx_Jws?t=986)
8. [Bonus 1: representing a date in Python](https://pymotw.com/3/datetime/index.html#dates)
9. [Bonus 1: date arithmetic in Python](https://pymotw.com/3/datetime/index.html#timedeltas)
10. [Bonus 1: one way to calculate first_day and last_day](https://stackoverflow.com/questions/36155332/how-to-get-the-first-day-and-last-day-of-current-month-in-python)
11. [Bonus 1: making first_day and last_day dynamic attributes](https://treyhunner.com/2019/05/python-builtins-worth-learning/#property)
12. [Bonus 2: methods that work on classes (not instances)](https://stackoverflow.com/questions/1950414/what-does-classmethod-do-in-this-code/1950927#1950927)
13. [Bonus 2: writing an alternative constructor](https://stackoverflow.com/questions/136097/what-is-the-difference-between-staticmethod-and-classmethod)
14. [Bonus 3: making memory efficient classes](https://www.datadependence.com/2016/07/pythonic-code-video-series-slots/)
15. [Bonus 3: a hint on implementing immutability](https://stackoverflow.com/a/48055480/98187)
16. [Bonus 3: on implementing hashability](https://stackoverflow.com/a/2909119/98187)

## Tests

Automated tests for this week's exercise [can be found here](https://www.pythonmorsels.com/exercises/e1ba106369e24029b5723a855ae5c735/tests/). You'll need to write your code in a module named month.py next to the test file. To run the tests you'll run "python test_month.py" and check the output for "OK". You'll see that there are some "expected failures" (or "unexpected successes" maybe). If you'd like to do the bonus, you'll want to comment out the noted lines of code in the tests file to test them properly.
