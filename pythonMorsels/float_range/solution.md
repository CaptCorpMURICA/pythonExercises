# Solution: float_range
###### 12/16/2019
Hey!

If you haven't attempted to solve float_range yet, close this email and go do that now before reading on. If you have attempted solving float_range, read on...

This week you were supposed to write a callable (whether a function, generator function, or a class) that accepts start, stop, and step and returns an iterable of numbers representing the range specified.

A first attempt at this problem might look like this:
```
def float_range(start, stop, step):
    """Return iterable of numbers from start to stop by step."""
    i = start
    things = []
    while i < stop:
        things.append(i)
        i += step
    return things
```
Here we're returning a list of numbers that we build up by starting with our start, incrementing by our step, and stopping once our we've passed the stop value.

This doesn't pass our tests. One reason is that we are requiring three arguments to our function, but we're supposed to be able to take 1, 2, or 3.

Normally you might use default arguments to make some of your arguments optional. But you can't do this:
```
def float_range(start=0, stop, step=1):
    i = start
    things = []
    while i < stop:
        things.append(i)
        i += step
    return things
```
Python doesn't know what that means because all required arguments have to come before any arguments with default values.

We could do this:
```
def float_range(*args):
    if len(args) == 3:
        (start, stop, step) = args
    elif len(args) == 2:
        (start, stop, step) = (*args, 1)
    elif len(args) == 1:
        (start, stop, step) = (0, *args, 1)
    else:
        raise TypeError("Must be called with 1, 2, or 3 arguments.")
    i = start
    things = []
    while i < stop:
        things.append(i)
        i += step
    return things
```
Here we're accepting any number of arguments and then manually handling the cases for 3, 2, or 1 arguments. Then we're raising an exception if too many or too few arguments were provided.

That's a bit complicated though. We could do this instead:
```
def float_range(start, stop=None, step=1):
    if stop is None:
        start, stop = 0, start
    i = start
    things = []
    while i < stop:
        things.append(i)
        i += step
    return things
```
Here we're setting start to 0 and stop to start if the stop value wasn't specified when `float_range` was called.

I find this more readable than the `*args` approach.

This passes more of our tests but it still fails two of them.

One of the tests requires that when we call our function that we don't create a list of numbers (or a tuple or any other non-lazy iterable).

The return value from our `float_range` callable is supposed to be lazy.

One way to do that is to make a generator function:
```
def float_range(start, stop=None, step=1):
    if stop is None:
        start, stop = 0, start
    i = start
    while i < stop:
        yield i
        i += step
```
This way the return value will be a lazy generator.

The other failing test is attempting to call our function with a negative step value.

When the `step` value is negative we need to change our less than sign to a greater than sign:
```
def float_range(start, stop=None, step=1):
    if stop is None:
        start, stop = 0, start
    i = start
    if step > 0:
        while i < stop:
            yield i
            i += step
    else:
        while i > stop:
            yield i
            i += step
```
Instead of manually incrementing numbers either up or down, we could instead figure out how many items are needed and then add our step over and over in a generator expression:
```
from math import ceil

def float_range(start, stop=None, step=1):
    if stop is None:
        start, stop = 0, start
    item_count = ceil((stop-start) / step)
    return (
        start+i*step
        for i in range(item_count)
    )
```
This is a lazy iterable also.

Notice that we don't need to handle a negative step specifically here because stop-start should be negative when the step is negative so the item count ends up being the correct positive number even with a negative step.

If we wanted to be clever we could use the `count` and `takewhile` functions from [itertools](https://docs.python.org/3/library/itertools.html) to do the same thing without pre-computing how many items we need:
```
from itertools import count, takewhile

def float_range(start, stop=None, step=1):
    if stop is None:
        start, stop = 0, start
    numbers = (
        start+i*step
        for i in count()
    )
    if step > 0:
        return takewhile(lambda n: n < stop, numbers)
    else:
        return takewhile(lambda n: n > stop, numbers)
```
I think the approach of pre-computing the length is more clear than this one though. It's also likely a bit faster. Plus it doesn't need that extra `if` statement.

But the generator function with a while loop is the most clear in my opinion.

## Bonus #1
We're going to talk about the first bonus in a moment.

But first we need to take a different approach to this problem.

The first bonus requires us to continue returning a lazy iterable, but also to make sure our lazy iterable has a length.

Generators don't have a length and neither do iterators in general. So we're going to need to create an iterable class instead of creating a callable that returns an iterable.
```
class float_range:

    def __init__(self, start, stop=None, step=1):
        if stop is None:
            start, stop = 0, start
        (self.start, self.stop, self.step) = (start, stop, step)

    def __iter__(self):
        i = self.start
        if self.step > 0:
            while i < self.stop:
                yield i
                i += self.step
        else:
            while i > self.stop:
                yield i
                i += self.step
```
When we call `float_range` now, we'll get back a float_range object (that is an instance of the `float_range` class).

Every `float_range` object is iterable, meaning they can be iterated over. This is the case because we've made a `__iter__` method that returns an iterator.

You might be thinking: our `__iter__` doesn't return an iterator... there's no return at all!

Our `__iter__` uses a yield statement which means it's a generator function. When you call a generator function, you get a generator back. Generators are iterators. So calling our `__iter__` does give an iterator back.

If you need a refresher on iterators here's [an article I wrote on for loops and the iterator protocol](http://treyhunner.com/2016/12/python-iterator-protocol-how-for-loops-work/). For even more you might want to watch my [Loop Better talk](https://www.youtube.com/watch?v=V2PkkMS2Ack) or read [the article based on it](https://opensource.com/article/18/3/loop-better-deeper-look-iteration-python).

Okay now let's implement a `__len__` method:
```
class float_range:

    def __init__(self, start, stop=None, step=1):
        if stop is None:
            start, stop = 0, start
        (self.start, self.stop, self.step) = (start, stop, step)

    def __len__(self):
        diff = self.stop-self.start
        if diff*self.step < 0:
            return 0
        div = diff // self.step
        mod = diff % self.step
        if mod == 0:
            return int(div)
        else:
            return int(div + 1)

    def __iter__(self):
        i = self.start
        if self.step > 0:
            while i < self.stop:
                yield i
                i += self.step
        else:
            while i > self.stop:
                yield i
                i += self.step
```
Here we're first making sure `(stop-start)/step` is a positive number. Otherwise we return `0`.

Then we're subtracting `start` from `stop` and then checking to see if they evenly divide (if they remainder is 0). We incrementing the number of values by one conditionally if they don't.

We're also converting the number that comes back from this division into an integer to ensure it isn't a floating point number because lengths must be integers in Python.

We could make this a bit shorter by combining the division and the modulo (that `%` operator, which does a remainder) using the built-in `divmod` function:
```
    def __len__(self):
        div, mod = divmod(self.stop-self.start, self.step)
        if div < 0:
            return 0
        elif mod == 0:
            return int(div)
        else:
            return int(div + 1)
```
We could also shorten that `if-else` by using an "inline if statement":
```
    def __len__(self):
        div, mod = divmod(self.stop-self.start, self.step)
        return max(0, int(div if mod == 0 else div+1))
```
Inline if statements in Python are our equivalent of the [ternary operator](https://en.wikipedia.org/wiki/%3F:) (?: in other languages).

Note that we're ensuring our length is never negative (which it shouldn't be) by using the built-in `max` function.

You might have been thinking wait didn't we already compute the length before using `math.ceil`? And we did!
```
from math import ceil

class float_range:

    def __init__(self, start, stop=None, step=1):
        if stop is None:
            start, stop = 0, start
        (self.start, self.stop, self.step) = (start, stop, step)

    def __len__(self):
        return max(ceil((self.stop-self.start) / self.step), 0)

    def __iter__(self):
        i = self.start
        while i < self.stop:
            yield i
            i += self.step
```
When we computed the length before we didn't handle negative values specifically because we were delegating work to the range built-in and when we called range(n) with a negative n, it always returned 0 items to us.

This version of `__len__` is the one I'd recommend because it's one line and it's pretty easy to read and understand: `(stop-start)` divided by `step`, rounded up, with a minimum value of 0.

Before we move to the next bonus, let's refactor `__iter__`.

Now that we've implemented `__len__`, we could refactor `__iter__` to remove the special condition for a negative step:
```
    def __iter__(self):
        i = self.start
        for _ in range(len(self)):
            yield i
            i += self.step
```
That `_` there is a convention for a variable that we don't care about (since we don't ever look at the number coming out of the range).

Okay. Onto the next bonus.

## Bonus #2
For this bonus we were supposed to make our `float_range` return value reversible using the built-in `reversed` function.

If we want to customize the functionality of reversed on our object, we can implement a `__reversed__`.

You might have looked at `__iter__`:
```
    def __iter__(self):
        i = self.start
        for _ in range(len(self)):
            yield i
            i += self.step
```
And thought why don't I just do that in reverse:
```
    def __reversed__(self):
        i = self.stop-1
        for _ in range(len(self)):
            yield i
            i -= self.step
```
That doesn't quite work though because our stop may not always actually by our last value.

We have to calculate the last value before we start counting downward.

We can do that using the length of our float_range object:
```
    def __reversed__(self):
        i = self.start + (len(self)-1)*self.step
        for _ in range(len(self)):
            yield i
            i -= self.step
```
Note that this `__reversed__` function also returns an iterator because we're yielding which means we've created a generator function and generator functions return generators which are iterators. That sentence was a bit of a confusing way to say "a generator function produces an iterator when called because generators are iterators".

Bonus #3
Okay for this one we had to make sure that `float_range` objects could be compared to each other using the `==` and `!=` operators.

We can do that by implementing `__eq__`:
```
    def __eq__(self, other):
        return (
            self.start == other.start and
            self.stop == other.stop and
            self.step == other.step
        )
```
This doesn't quite pass our tests though.

For one thing, this doesn't handle cases where the start or stop aren't exactly the same but the items are the same:
```
>>> list(float_range(1, 4)), list(float_range(1, 3.8))
([1, 2, 3], [1, 2, 3])
```
For one thing, we don't properly handle cases where the two given ranges have different steps but just one item:
```
>>> list(float_range(1, 2, 5)), list(float_range(1, 2, 10))
([1], [1])
```
We could handle these cases by making a special case for ranges with a length of 0 or 1 and by checking whether the first item, last item, and step are the same:
```
    def __eq__(self, other):
        if len(self) == len(other) <= 1:
            return list(self) == list(other)
        first_same = next(iter(self)) == next(iter(other))
        last_same = next(reversed(self)) == next(reversed(other))
        return first_same and last_same and self.step == other.step
```
Our tests still don't quite pass though.

Our tests are expecting our `float_range` object to allow other objects to compare as equal to it. Meaning if it doesn't know how to compare itself to the other object, it should allow the check to be delegated to the other object.

We can do that by returning `NotImplemented` if we are comparing ourselves to something that we don't know how to work with:
```
    def __eq__(self, other):
        if isinstance(other, (float_range, range)):
            if len(self) == len(other) <= 1:
                return list(self) == list(other)
            first_same = next(iter(self)) == next(iter(other))
            last_same = next(reversed(self)) == next(reversed(other))
            return first_same and last_same and self.step == other.step
        else:
            return NotImplemented
```
Note that we're using an `isinstance` check here to check whether our object is either a `float_range` or one of the built-in range objects. If it isn't then we return `NotImplemented` and Python knows that it needs to go ask the other object whether it knows how to do this comparison.

We could practice duck typing by dealing with any object that has a `start`/`stop`/`step`:
```
    def __eq__(self, other):
        try:
            if len(self) == len(other) <= 1:
                return list(self) == list(other)
            first_same = next(iter(self)) == next(iter(other))
            last_same = next(reversed(self)) == next(reversed(other))
            return first_same and last_same and self.step == other.step
        except TypeError:
            return NotImplemented
```
But operators are often one of those times where type checking is considered acceptable in the Python world. So I'm going to use `isinstance` checks for the types we know how to handle instead.

We could rewrite this equality check like this:
```
    def __eq__(self, other):
        if isinstance(other, (float_range, range)):
            if len(self) == len(other) <= 1:
                return list(self) == list(other)
            my_vals = (next(iter(self)), next(reversed(self)), self.step)
            other_vals = (next(iter(other)), next(reversed(other)), other.step)
            return my_vals == other_vals
        else:
            return NotImplemented
```
Here we're taking the first and last values and the step from our object and from the other object and making tuples out of them and then comparing the tuples. Using tuples like this can sometimes make things more clear. In this case I don't think it helps too much.

We could instead add a `_attrs` property to our class and use it as a key of sorts to determine whether two `float_range` objects are equal:
```
    @property
    def _attrs(self):
        if len(self) == 0:
            return ()
        return (next(iter(self)), next(reversed(self)), len(self))
```
Here we're returning an empty tuple for a 0-length range and a tuple with the first and last elements and the length for a non-empty range. We're using the length instead of the step to uniquely identify our ranges because two ranges can have the same single element while having a different step value.

We'd access `_attrs` like this:
```
    def __eq__(self, other):
        if isinstance(other, (float_range, range)):
            return self._attrs == other._attrs
        else:
            return NotImplemented
```
This doesn't work for range objects though! Our `float_range` objects are supposed to be comparable to range objects and range objects don't have a `_attrs` attribute.

So we could use a helper function outside our class instead:
```
from math import ceil

def _attrs(self):
    if len(self) == 0:
        return ()
    return (next(iter(self)), next(reversed(self)), len(self))

class float_range:

    def __init__(self, start, stop=None, step=1):
        if stop is None:
            start, stop = 0, start
        (self.start, self.stop, self.step) = (start, stop, step)

    def __eq__(self, other):
        if isinstance(other, (float_range, range)):
            return _attrs(self) == _attrs(other)
        else:
            return NotImplemented

    def __len__(self):
        return max(ceil((self.stop-self.start) / self.step), 0)

    def __iter__(self):
        i = self.start
        for _ in range(len(self)):
            yield i
            i += self.step

    def __reversed__(self):
        i = self.start + (len(self)-1)*self.step
        for _ in range(len(self)):
            yield i
            i -= self.step
```
Here we're passing each of the objects to `_attrs` to get the start, stop, and step attributes from each as a tuple for our comparison.

That `_` before attrs is a convention you'll see sometimes with functions or attributes that aren't meant for use outside this object. This is a "private" function in that the authors of this `float_range` class and module are the only ones that are likely to use it.

This might be one of those rare times when a `staticmethod` makes sense in Python. If we wanted our `_attrs` function to be customizable by subclasses we could do this instead:
```
from math import ceil

class float_range:

    def __init__(self, start, stop=None, step=1):
        if stop is None:
            start, stop = 0, start
        (self.start, self.stop, self.step) = (start, stop, step)

    @staticmethod
    def _attrs(self):
        if len(self) == 0:
            return ()
        return (next(iter(self), None), next(reversed(self, None)), len(self))

    def __eq__(self, other):
        if isinstance(other, (float_range, range)):
            return self._attrs(self) == self._attrs(other)
        else:
            return NotImplemented

    def __len__(self):
        return max(ceil((self.stop-self.start) / self.step), 0)

    def __iter__(self):
        i = self.start
        for _ in range(len(self)):
            yield i
            i += self.step

    def __reversed__(self):
        i = self.start + (len(self)-1)*self.step
        for _ in range(len(self)):
            yield i
            i -= self.step
```
I pretty much never use the `staticmethod` decorator. This might be an appropriate use case here, but don't get too hung up on it.

If you'd like to brush up on properties, static methods, and various other class stuff in Python, you might consider watching Raymond Hettinger's PyCon 2013 talk, [Python's Class Development Toolkit](https://www.youtube.com/watch?v=HTLu2DFOdTg).

I hope you learned something new this week. :)
