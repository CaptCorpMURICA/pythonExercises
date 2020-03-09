# suppress
##### Assigned from Mixed Level on 02/10/2020
Hey!

This week I'd like you to create a context manager. Context managers use a `with` block to bookend a block of code with automatic setup and teardown steps.

Your context manager, `suppress`, should suppress exceptions of a given type:
```
>>> with suppress(NameError):
...     print("Hi!")
...     print("It's nice to meet you,", name)
...     print("Goodbye!")
...
Hi!
>>> with suppress(TypeError):
...     print("Hi!")
...     print("It's nice to meet you,", name)
...     print("Goodbye!")
...
Hi!
Traceback (most recent call last):
  File "<stdin>", line 3, in <module>
NameError: name 'name' is not defined
>>> x = 0
>>> with suppress(ValueError):
...     x = int('hello')
...
>>> x
0
```
What I mean by "suppress" is that if the given exception type is raised, that exception should be caught and _muted_ in a sense.

To solve this exercise, you'll want to lookup how to create a context manager in Python.

### Bonus 1

For the first bonus, I'd like you to make your `suppress` context manager accept any number of exceptions to suppress ✔️:
```
>>> with suppress(ValueError, TypeError):
...     x = int('hello')
...
>>> with suppress(ValueError, TypeError):
...     x = int(None)
...
```
### Bonus 2

For the second bonus, I'd like you to allow your context manager to store the exception and traceback information on an object that can be accessed using the `with X as Y` syntax ✔️:
```
>>> with suppress(ValueError, TypeError) as context:
...     x = int('hello')
...
>>> context.exception
ValueError("invalid literal for int() with base 10: 'hello'",)
>>> context.traceback
<traceback object at 0x7fe829bc3bc8>
```
The `exception` and `traceback` attributes should be `None` when no exception was suppressed.

### Bonus 3

If you manage to solve the main problem and both of the first two bonuses with time remaining, I have a third bonus for you.

For the third bonus I'd like you to allow your context manager to be used as a decorator as well ✔️:
```
>>> @suppress(TypeError)
... def len_or_none(thing):
...     return len(thing)
...
```
This decorator should essentially wrap your function in a call to the suppress context manager:
```
>>> len_or_none('hello')
5
>>> len_or_none(64)
>>> len_or_none([2, 4, 8])
3
>>> len_or_none()
>>> len_or_none([])
0
```
## Hints

Hints for **when you get stuck** (hover over links to see what they're about):

1. [What is a context manager?](https://jeffknupp.com/blog/2016/03/07/python-with-context-managers/)
2. [How to write a context manager](https://youtu.be/-aKFBoZpiqA)
3. [Handling exceptions in a context manager](https://stackoverflow.com/questions/35483359/handling-exceptions-inside-context-managers/35483461#35483461)
4. [Checking if an item is an exception](https://stackoverflow.com/questions/707674/how-to-compare-type-of-an-object-in-python)
5. [A helper for writing context managers](https://docs.python.org/3/library/contextlib.html#contextlib.contextmanager)
6. [Accepting any number of exception types](https://treyhunner.com/2018/10/asterisks-in-python-what-they-are-and-how-to-use-them/#Asterisks_for_unpacking_into_function_call)
7. [Returning an object with the with ... as syntax](https://stackoverflow.com/questions/34749943/python-with-as-for-custom-context-manager)
8. [Helper tools for writing decorators](https://stackoverflow.com/questions/308999/what-does-functools-wraps-do)
9. [Using a context manager as a decorator](https://stackoverflow.com/questions/9213600/function-acting-as-both-decorator-and-context-manager-in-python)

## Tests

Automated tests for this week's exercise [can be found here](https://www.pythonmorsels.com/exercises/ded322173d47424581be45adaeeca90d/tests/). You'll need to write your code in a module named suppress.py next to the test file. To run the tests you'll run "python test_suppress.py" and check the output for "OK". You'll see that there are some "expected failures" (or "unexpected successes" maybe). If you'd like to do the bonus, you'll want to comment out the noted lines of code in the tests file to test them properly.
