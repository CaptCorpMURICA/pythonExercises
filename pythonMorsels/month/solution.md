# Solution: Month
###### 01/13/2020
Heya,

If you haven't attempted to solve Month yet, close this email and go do that now before reading on. If you have attempted solving Month, read on...

This week you needed to make a `Month` class which represents a month. Your `Month` class needed to work with comparison operators and it needs to have nice string representations.

Here's one way to do this:
```
class Month:

    def __init__(self, year, month):
        self.year = year
        self.month = month

    def __str__(self):
        return "{}-{:02}".format(self.year, self.month)

    def __repr__(self):
        return "Month({}, {})".format(self.year, self.month)

    def __eq__(self, other):
        if not isinstance(other, Month):
            return NotImplemented
        return self.year == other.year and self.month == other.month

    def __lt__(self, other):
        if not isinstance(other, Month):
            return NotImplemented
        if self.year < other.year:
            return True
        elif self.year > other.year:
            return False
        elif self.month < other.month:
            return True
        else:
            return False

    def __le__(self, other):
        return self.__eq__(other) or self.__lt__(other)
```
We've made `__str__` and `__repr__` methods here to make a nice string representation. We're using the string `format` method for this.

We've also made `__eq__`, `__lt__`, and `__le__` methods which allow `==`, `<`, and `<=` to work. We're being clever here by making `__le__` method delegate to `__eq__` and `__lt__`.

In these comparison methods we need to return `NotImplemented` to make sure errors aren't raised for types we don't understand.

Note that these 3 comparison methods actually also allow `!=`, `>=`, and `<` to work because comparison operators in Python 3 are assumed to be commutative by default. This means that `x >= y` will call `y < x` if `__ge__` isn't defined.

We've written these methods the _long way_. There's a shorter way to write these by relying on tuple comparisons:
```
class Month:

    def __init__(self, year, month):
        self.year, self.month = year, month

    def __str__(self):
        return f"{self.year}-{self.month:02}"

    def __repr__(self):
        return f"Month({self.year}, {self.month})"

    def __eq__(self, other):
        if not isinstance(other, Month):
            return NotImplemented
        return (self.year, self.month) == (other.year, other.month)

    def __lt__(self, other):
        if not isinstance(other, Month):
            return NotImplemented
        return (self.year, self.month) < (other.year, other.month)

    def __le__(self, other):
        return self.__eq__(other) or self.__lt__(other)
```
We've removed that big `if-elif-else` block from the `__lt__` method here and instead we're checking the year and then the month using [tuple comparisons](https://stackoverflow.com/questions/5292303/how-does-tuple-comparison-work-in-python).

We're also using tuple comparisons for our `__eq__` method, which isn't as big an improvement as our `__lt__` method, but I do find the symmetry on each side of the `==` improves readability.

Two other things we're doing differently here are that we're using f-strings in our `__str__` and `__repr__` methods and we've chosen to use tuple unpacking to assign our two class attributes in our `__init__` method.

Usually when implementing comparison operators, I'd recommend using the `functools.total_ordering` decorator:
```
from functools import total_ordering


@total_ordering
class Month:

    def __init__(self, year, month):
        self.year, self.month = year, month

    def __str__(self):
        return f"{self.year}-{self.month:02}"

    def __repr__(self):
        return f"Month({self.year!r}, {self.month!r})"

    def __eq__(self, other):
        if not isinstance(other, Month):
            return NotImplemented
        return (self.year, self.month) == (other.year, other.month)

    def __lt__(self, other):
        if not isinstance(other, Month):
            return NotImplemented
        return (self.year, self.month) < (other.year, other.month)
```
This class decorator allows us to implement `__eq__` and `__lt__` and we'll get the other comparison methods for free.

In our case where we're only comparing `Month` objects to other `Month` objects, this really only saves us from writing that `__le__` method. Personally I'd still use `functools.total_ordering` anyway as I've adopted a habit of always using it when implementing comparisons.

The last way to solve the base problem is considerably shorter than the previous solutions.

If you're on Python 3.7, you can use dataclasses to achieve all of this functionality:
```
from dataclasses import dataclass


@dataclass(order=True)
class Month:

    year: int
    month: int

    def __str__(self):
        return f"{self.year}-{self.month:02}"
```
The `__repr__` isn't identical to the one we had before, but our tests don't check for an exact string match.

Python 3.7's dataclasses give you an initializer, `__repr__`, and equality/inequality for free. We have to implement our own `__str__` and if we want the various `<`/`>` ordering operators (which we do), we can pass `order=True` to the `dataclass` decorator to enable them.

I gave a talk you may want to watch on how data classes can be used for writing "friendly classes". It's called [Easier Classes: Python Classes Without All The Cruft](https://youtu.be/epKegvx_Jws) and I'd recommend watching it if you're new to dataclasses.

### Bonus #1
For the first bonus, you needed to create `first_day` and `last_day` attributes on your `Month` object.

If we're solving `Month` the non-dataclass way, we could modify our `__init__` method to add `first_day` and `last_day` attributes like this:
```
from datetime import date, timedelta


class Month:

    # ...

    def __init__(self, year, month):
        self.year, self.month = year, month
        self.first_day = date(year, month, 1)
        next_month = self.first_day + timedelta(days=31)
        self.last_day = next_month.replace(day=1) - timedelta(days=1)

    # ...
```
The `first_day` is easy (just day `1` in the given month).

For the `last_day` we're doing something sort of hacky: we're moving 31 days into the future, replacing the given day in that `date` with `1`, and then subtracting 1 day to get the last day of our current month.

There's a tool in the standard library that can do this for us:
```
from calendar import monthrange
from datetime import date


class Month:

    # ...

    def __init__(self, year, month):
        self.year, self.month = year, month
        self.first_day = date(year, month, 1)
        self.last_day = date(year, month, monthrange(year, month)[1])
```
The `calendar.monthrange` function returns a two-tuple of the weekday of the first day of the month and the last date of the month. That might seem weird, but this function is usually used as a helper function for creating rows in a calendar.

We can make our code slightly less mysterious by using tuple unpacking instead of that `[1]` indexing:
```
    _, last_date = monthrange(year, month)
    self.last_day = date(year, month, last_date)
```
That `_` variable name represents the fact that we don't care about that object. This really isn't much more helpful and I don't have a strong preference for which way this problem is solved.

Regardless of how you solve this one, you could use properties instead of hard-coded attributes:
```
    @property
    def first_day(self):
        return date(self.year, self.month, 1)

    @property
    def last_day(self):
        weekday_of_first_date, last_date = monthrange(self.year, self.month)
        return date(self.year, self.month, last_date)
```
Instead of hard-coding `first_day` and `last_day` in our `__init__` method we're making two property attributes which calculate these dates each time they're called.

Using properties is the easiest way to solve this one if you're also using a dataclass:
```
from calendar import monthrange
from dataclasses import dataclass
from datetime import date


@dataclass(order=True)
class Month:

    year: int
    month: int

    def __str__(self):
        return f"{self.year}-{self.month:02}"

    @property
    def first_day(self):
        return date(self.year, self.month, 1)

    @property
    def last_day(self):
        weekday_of_first_date, last_date = monthrange(self.year, self.month)
        return date(self.year, self.month, last_date)
```
If we preferred to use hard-coded class attributes instead of properties, that's also possible with dataclasses but you'll need to know about the dataclass-specific `__post_init__` method:
```
from calendar import monthrange
from dataclasses import dataclass
from datetime import date


@dataclass(order=True)
class Month:

    year: int
    month: int

    def __post_init__(self):
        weekday_of_first_date, last_date = monthrange(self.year, self.month)
        self.first_day = date(self.year, self.month, 1)
        self.last_day = date(self.year, self.month, last_date)

    def __str__(self):
        return f"{self.year}-{self.month:02}"
```
This code is shorter and accesses to the `first_day` and `last_day` attributes will be faster with this code.

Personally I somewhat prefer the `property` approach though. With properties, if our `first_day` and/or `last_day` attributes are never accessed, we'll never even compute what they should be (so we're optimizing for a different use case). Also I find the dependency between our `year` and `month` a bit more explicit when using properties (if `year` or `month` change our property will change too).

### Bonus #2
For the second bonus, you needed to create a `from_date` factory method for creating `Month` objects from `date` objects and you needed to create a `strftime` method for your `Month` class, like the `date` object's `strftime` method.

We'll implement `from_date` first. For `from_date` we needed to allow our method to be called on the `Month` class itself. We could do that like this:
```
    def from_date(date_obj):
        return Month(date_obj.year, date_obj.month)
```
Note that we don't have a `self` here. When `Month.from_date` is called, no instance will be passed in so we just accept the `date` object given to our method.

This method has an issue though: when it's called on a `Month` instance it'll act funny.
```
>>> from datetime import date
>>> month = Month(2000, 1)
>>> month.from_date(date(1999, 12, 1))
Traceback (most recent call last):
  File "<stdin>", line 1, in <module>
TypeError: from_date() takes 1 positional argument but 2 were given
```
We might want an error in this case, but the `datetime.date` object has a few methods that work on the class itself which _also_ work on the instance:
```
>>> date.fromordinal(3)
datetime.date(1, 1, 3)
>>> date(1999, 12, 1).fromordinal(3)
datetime.date(1, 1, 3)
```
So we're going to make our method work that way too. One way to do that is using the `staticmethod` decorator:
```
    @staticmethod
    def from_date(date_obj):
        return Month(date_obj.year, date_obj.month)
```
The `classmethod` decorator is more appropriate though:
```
    @classmethod
    def from_date(cls, date_obj):
        return cls(date_obj.year, date_obj.month)
```
While the `staticmethod` decorator ensures no `self` attribute is ever passed in, the `classmethod` decorator makes the first argument of our method always be the type of the object we're working with. This will allow our `Month` class to be inherited from and still return the correct type of object (our child class).

Alright let's make that `strftime` method now.

We'll sort of cheat on this one by delegating to the `date.strftime` method:
```
    def strftime(self, fmt):
        return self.first_day.strftime(fmt)
```
This will allow the day of the month to be passed in, which will always be `1` and that might seem inappropriate, but the `date` object actually seems to delegate to the `datetime.strftime` method because hours and minutes can be passed in as well (they all default to `0`) so our behavior here isn't atypical:
```
>>> from datetime import date
>>> date(1999, 12, 15).strftime('%Y-%m-%d %H:%M')
'1999-12-15 00:00'
>>> Month(1999, 12).strftime('%Y-%m-%d %H:%M')
'1999-12-01 00:00'
```
### Bonus #3
For the third bonus, you needed to make your `Month` objects immutable, hashable, and memory efficient (using `__slots__`).

The trickiest part is immutability. You could try to inherit from `typing.NamedTuple` (or `collections.namedtuple` if you prefer the older way) but that would break some of our comparison tests.

Another way to achieve "immutability" (of our `year` and `month` attributes at least) would be to create properties that wrap around "private" attributes (private only by convention).
```
    # ...

    def __init__(self, year, month):
        self._year = year
        self._month = month

    @property
    def year(self):
        return self._year

    @property
    def month(self):
        return self._month

    # ...
```
We could of course still change the `_year` and `_month` attributes, but those `_` indicate that we _shouldn't_. If we put two underscores before that attribute Python will actually use name mangling to make those attributes much closer to "private"... but don't do this as [name mangling isn't recommended](https://stackoverflow.com/a/7456865/98187).

Another way to make this class effictively immutable is to override the `__setattr__` and `__delattr__` methods:
```
    def __setattr__(self, name, value):
        raise NotImplementedError

    def __delattr__(self, name):
        raise NotImplementedError
```
This will be a problem in our initializer though because we need to set those `year` and `month` attributes _somehow_. We can force those `year` and `month` attributes to be initially set in our `__init__` by delegating to our parent class' `__setattr__` (our parent is `object`), which does still work:
```
    def __init__(self, year, month):
        super().__setattr__('year', year)
        super().__setattr__('month', month)
```
If we're using a dataclass, all we have to do is set `frozen=True` when decorating our class:
```
@dataclass(order=True, frozen=True)
```
Let's talk about `__slots__` now. The `__slots__` attribute is a way of saying "this class will only ever have these particular attributes, so use a tuple to store their values instead of a dictionary".

If we were using the `property`-wrapping solution before with `_year` and `_month` attributes, our `__slots__` would need to look like this:
```
class Month:

    __slots__ = ['_year', '_month']

    # ...
```
With `year` and `month` attributes, our `__slots__` will look like this:
```
class Month:

    __slots__ = ['year', 'month']

    # ...
```
If you're setting `first_day` and `last_day` attributes on your object as well, you'll need those in `__slots__` also:
```
class Month:

    __slots__ = ['year', 'month', 'first_day', 'last_day']

    # ...
```
Dataclasses don't have any special way of setting `__slots__`, so we'll need to set it ourselves if we want a more memory-efficient object.

By the way, once `__slots__` is set, our `_year` and `_month` solution is actually a bit nicer because Python won't allow adding other arbitrary attributes to our class (so it'll seem much more "immutable").

Okay let's talk about hashability now.

For hashability we'll need to implement a `__hash__` method that returns a unique(-ish) hash value (an integer) for our `Month` object. One way to do that is to calculate and return a unique number:
```
    def __hash__(self):
        return self.year*12 + self.month
```
Or we could just delegate to the tuple `__hash__` method by making a year-month tuple and calling the built-in `hash` function on it:
```
    def __hash__(self):
        return hash((self.year, self.month))
```
If we're using dataclasses, we actually don't even need to make a `__hash__` function because setting `frozen=True` gives us a hash value for free (immutable types are essentially always hashable).

Here's a non-dataclass solution (which you'll need to use if you're not on Python 3.7 yet):
```
from calendar import monthrange
from datetime import date
from functools import total_ordering


@total_ordering
class Month:

    __slots__ = ['year', 'month']

    def __init__(self, year, month):
        super().__setattr__('year', year)
        super().__setattr__('month', month)

    def __str__(self):
        return f"{self.year}-{self.month:02}"

    def __repr__(self):
        return f"Month({self.year}, {self.month})"

    def __eq__(self, other):
        if not isinstance(other, Month):
            return NotImplemented
        return (self.year, self.month) == (other.year, other.month)

    def __lt__(self, other):
        if not isinstance(other, Month):
            return NotImplemented
        return (self.year, self.month) < (other.year, other.month)

    def __setattr__(self, name, value):
        raise NotImplementedError

    def __delattr__(self, name):
        raise NotImplementedError

    def __hash__(self):
        return hash((self.year, self.month))

    @property
    def first_day(self):
        return date(self.year, self.month, 1)

    @property
    def last_day(self):
        weekday_of_first_date, last_date = monthrange(self.year, self.month)
        return date(self.year, self.month, last_date)

    @classmethod
    def from_date(cls, date_obj):
        return cls(date_obj.year, date_obj.month)

    def strftime(self, fmt):
        return self.first_day.strftime(fmt)
```
And here's a dataclass solution (which I very much prefer):
```
from calendar import monthrange
from dataclasses import dataclass
from datetime import date


@dataclass(order=True, frozen=True)
class Month:

    __slots__ = ['year', 'month']

    year: int
    month: int

    def __str__(self):
        return f"{self.year}-{self.month:02}"

    @property
    def first_day(self):
        return date(self.year, self.month, 1)

    @property
    def last_day(self):
        weekday_of_first_date, last_date = monthrange(self.year, self.month)
        return date(self.year, self.month, last_date)

    @classmethod
    def from_date(cls, date_obj):
        return cls(date_obj.year, date_obj.month)

    def strftime(self, fmt):
        return self.first_day.strftime(fmt)
```
And here's another dataclass solution which hard-codes the `first_day` and `last_day` attributes instead of using properties for them:
```
from calendar import monthrange
from dataclasses import dataclass
from datetime import date


@dataclass(order=True, frozen=True)
class Month:

    __slots__ = ['year', 'month', 'first_day', 'last_day']

    year: int
    month: int

    def __post_init__(self):
        weekday_of_first_date, last_date = monthrange(self.year, self.month)
        super().__setattr__('first_day', date(self.year, self.month, 1))
        super().__setattr__('last_day', date(self.year, self.month, last_date))

    def __str__(self):
        return f"{self.year}-{self.month:02}"

    @classmethod
    def from_date(cls, date_obj):
        return cls(date_obj.year, date_obj.month)

    def strftime(self, fmt):
        return self.first_day.strftime(fmt)
```
I don't have a strong preference for one of those dataclass solutions over the other. They optimize for slightly different things (both performance and readability-wise).
