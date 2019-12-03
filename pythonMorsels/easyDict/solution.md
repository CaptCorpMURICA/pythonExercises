# Solution: EasyDict
###### 11/25/2019

Hiya!

If you haven't attempted to solve EasyDict yet, close this email and go do that now before reading on. If you have attempted solving `EasyDict`, read on...

This week you needed to create a class, `EasyDict`, which creates objects that have both can be used with a dictionary-like key lookup and with the usual `.` syntax for attribute lookups.

Here's one way we could have done this:
```
class EasyDict:

    """Dictionary-like class that allows attribute lookup."""

    def __init__(self, mapping=None):
        if mapping is not None:
            self.__dict__ = mapping

    def __getitem__(self, key):
        return self.__dict__[key]
```
This class accepts a dictionary (mapping is the generic name for dictionary-like things in Python) and sets our class's `__dict__` attribute to this dictionary. The `__dict__` attribute is where classes store their attributes, so we're basically saying "store our attributes here instead".

We're allowing the `[...]` notation to work for looking up keys in our `EasyDict` object by implementing `__getitem__`, which is the method that powers those operators.

This doesn't quite pass our tests though. The problem is that when we change attributes in our `EasyDict` object, our original dictionary will change too! To fix this we could say `self.__dict__ = mapping.copy()`.

Or we could do this:
```
    def __init__(self, mapping={}):
        self.__dict__.update(mapping)
```
We're using a default value of a dictionary and we're calling the update method on our `__dict__` (which all classes have by default) to add more attributes to our class.

These `__dict__` solutions are clever but you might not have thought of them. You may have instead tried to hook into the attribute lookup machinery inside your class to return something special whenever attributes are looked up.

One way to do that is with `__getattribute__`:
```
class EasyDict:

    """Dictionary-like class that allows attribute lookup."""

    def __init__(self, mapping=None):
        if mapping is None:
            mapping = {}
        self.mapping = mapping

    def __getattribute__(self, key):
        if key.startswith('__') or key in self.__dict__:
            return super().__getattribute__(key)
        return self.mapping[key]

    def __getitem__(self, key):
        return self.mapping[key]
```
This code is complicated because `__getattribute__` is called for every attribute lookup, including lookups on `__dict__`. So we're special-casing `__dunder__` attributes so that we can lookup `__dict__` without causing an infinite recursion issue.

A better way to hook into attribute lookups is to use `__getattr__`:
```
class EasyDict:

    """Dictionary-like class that allows attribute lookup."""

    def __init__(self, mapping=None):
        if mapping is None:
            mapping = {}
        self.mapping = mapping

    def __getattr__(self, key):
        return self.mapping[key]

    def __getitem__(self, key):
        return self.mapping[key]
```
Python only calls `__getattr__` if the attribute lookup fails in the usual places (when checking `__dict__` for example).
### Bonus #1

For the first bonus, you needed to make sure assignments to keys and attributes work.

Here's one way to do this:
```
class EasyDict:

    """Dictionary-like class that allows attribute lookup."""

    def __init__(self, mapping=None):
        self.__dict__.update(mapping)

    def __getitem__(self, key):
        return self.__dict__[key]

    def __setitem__(self, key, value):
        self.__dict__[key] = value
```
Here we're accepting a dictionary and updating our class's `__dict__` dictionary with the dictionary we've been given. We're also allowing the dictionary set item syntax to be used for updating our class's attributes (through `__dict__`).

Note that we haven't been assigning the default value for `mapping` to a dictionary, but instead we've been using `None` as the default value and then making an empty dictionary if the default of `None` is found.

We've been doing this because the expression given to a default value is executed at function definition time, not at function evaluation time. For this reason, using mutable default values in Python functions can be problematic sometimes. The [Hitchhiker's Guide to Python](https://docs.python-guide.org/writing/gotchas/#mutable-default-arguments) discusses this problem.

This isn't problematic in our case because we're looping over the given dictionary and merging each item into our `__dict__` dictionary.

We could do this instead:
```
class EasyDict:

    """Dictionary-like class that allows attribute lookup."""

    def __init__(self, mapping={}):
        self.__dict__.update(mapping)

    def __getitem__(self, key):
        return self.__dict__[key]

    def __setitem__(self, key, value):
        self.__dict__[key] = value
```
Instead of reaching into `__dict__` directly, we could also do this instead:
```
class EasyDict:

    """Dictionary-like class that allows attribute lookup."""

    def __init__(self, mapping={}):
        self.__dict__.update(mapping)

    def __getitem__(self, key):
        return getattr(self, key)

    def __setitem__(self, key, value):
        return setattr(self, key, value)
```
Here we're getting and setting the attributes using `getattr` and `setattr` instead of accessing and setting items in our `EasyDict` object's `__dict__` dictionary.
### Bonus #2

For the second bonus we needed to accept keyword arguments to our initializer in addition to a dictionary argument and we needed to include a `get` method on our `EasyDict` object, which works the same way as dictionary get methods. We also needed our `EasyDict` objects to compare as equal to each other when they have the same key-value pairs.

We could do that this way:
```
class EasyDict:

    """Dictionary-like class that allows attribute lookup."""

    def __init__(self, mapping={}, **kwargs):
        self.__dict__.update(mapping)
        self.__dict__.update(kwargs)

    def __getitem__(self, key):
        return getattr(self, key)

    def __setitem__(self, key, value):
        return setattr(self, key, value)

    def __eq__(self, other):
        return self.__dict__ == other.__dict__

    def get(self, key, default=None):
        return getattr(self, key, default)
```
Here we're updating our `__dict__` dictionary in our initializer with both the given mapping and the dictionary of all given keyword arguments.

Our `get` method does the same thing as our `__getitem__` method, except that it provides a default value, just like dictionary `get` methods normally do.

Our `__eq__` just compares the `__dict__` attribute of the other object given to us, which is a very simplistic approach because we're not type checking at all. This passes our tests because we aren't checking for types besides `EasyDict`. We could do this for a more correct implementation:
```
    def __eq__(self, other):
        if not isinstance(other, EasyDict):
            return NotImplemented
        return self.__dict__ == other.__dict__
```
### Bonus #3

For the third bonus you needed to accept a `normalize` argument that would normalize spaces in dictionary values to underscores in class attributes.

This one required changing `__init__`, `__getitem__`, `__setitem__`, and `get`. Instead of making similar changes to all of these, we could make a helper method that "normalizes" keys-with-spaces to attributes-with-underscores.

Here's one way to do this:
```
class EasyDict:

    """Dictionary-like class that allows attribute lookup."""

    def __init__(self, mapping={}, normalize=False, **kwargs):
        self._normalize = normalize
        for key, value in mapping.items():
            self[key] = value
        for key, value in kwargs.items():
            self[key] = value

    def normalized(self, key):
        if self._normalize:
            return key.replace(' ', '_')
        else:
            return key

    def __getitem__(self, key):
        return getattr(self, self.normalized(key))

    def __setitem__(self, key, value):
        setattr(self, self.normalized(key), value)

    def __eq__(self, other):
        return self.__dict__ == other.__dict__

    def get(self, key, default=None):
        return getattr(self, self.normalized(key), default)
```
The initializer method is now manually setting key-value pairs on our `EasyDict` object instead of calling `update` on `__dict__`. This is important because this relies on `__setitem__` which calls a new `normalized` method to normalize the key before it's set.

The `normalized` method replaces spaces with underscores and returns the result. It's used by `__getitem__`, `__setitem__`, and `get`.

This is getting quite involved. At this point we could decide that it makes more sense to inherit from another dict-like object and handle attribute assignment specially instead.

Here we're inheriting from `UserDict` to do this:
```
from collections import UserDict


class EasyDict(UserDict):

    def __init__(self, mapping={}, normalize=False, **kwargs):
        self._normalize = normalize
        super().__init__(mapping, **kwargs)

    @property
    def data(self):
        return self.__dict__

    @data.setter
    def data(self, data):
        self.__dict__.update(data)

    def normalized(self, key):
        return key.replace(' ', '_') if self._normalize else key

    def __getitem__(self, key):
        return self.__dict__[self.normalized(key)]

    def __setitem__(self, key, value):
        self.__dict__[self.normalized(key)] = value
```
The `UserDict` class has an `data` attribute that stores the actual data this dictionary-like class by default. We're using properties to ensure that this `data` attribute is really just an alias for the `__dict__` attribute. We're overriding the setting of the `data` attribute to update the `__dict__` dictionary instead. This is a little complicated and I'd personally rather we ignored the default initializer and disallowed setting on the `data` attribute instead.

I prefer this:
```
from collections import UserDict


class EasyDict(UserDict):

    def __init__(self, mapping={}, normalize=False, **kwargs):
        self._normalize = normalize
        self.update(mapping)
        self.update(kwargs)

    @property
    def data(self):
        return self.__dict__

    def normalized(self, key):
        return key.replace(' ', '_') if self._normalize else key

    def __getitem__(self, key):
        return self.__dict__[self.normalized(key)]

    def __setitem__(self, key, value):
        self.__dict__[self.normalized(key)] = value
```
Our dictionary-like object has an `update` method because `UserDict` has an update method. The `UserDict update` and get methods rely on the `__setitem__` and `__getitem__` methods respectively, which we're overriding. And the `__eq__` method already does what we want by default because we're inheriting from `UserDict`.

That last solution is my preferred solution to this problem.

An important note:

You might have tried to inherit from the built-in `dict` class. This is a bad idea.

The `dict` class doesn't delegate to `__getitem__` and `__setitem__` when you call other methods like `get`. So you'll need to manually call `normalized` in your initializer and in your `get` method. If an `update` or `pop` method were required, they would need to be overridden too, whereas `UserDict` will delegate to your class's `__getitem__` and `__setitem__`.

By the way, if you're wondering whether anyone actually uses classes like this the answer is yes. The `pydal` library (which is used by the very odd `web2py` framework) has a `Row` class that [works similarly to our EasyDict](https://github.com/web2py/pydal/blob/dda7c65dd7a9782d3378b4d30e8ce9402f2bdbd1/pydal/objects.py#L54).

I hoped you learned something about dictionaries and classes this week!
