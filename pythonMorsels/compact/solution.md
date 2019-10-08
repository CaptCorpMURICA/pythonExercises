# Solution: compact
###### 06/24/2019
Hey there!

If you haven't attempted to solve compact yet, close this email and go do that now before reading on. If you have attempted solving compact, read on...

If you assume the input to `compact` is a list, a string, or another sequence (indexed from 0 upward), you could use indexes to check whether the current item is the same as the item before it:
```
def compact(sequence):
    """Return new iterable with adjacent duplicate values removed."""
    deduped = []
    for i, item in enumerate(sequence):
        if i == 0 or item != sequence[i-1]:
            deduped.append(item)
    return deduped
```
We're always appending item 0 and then only appending subsequent items if they are not equal to the item just before their index.

Another way you could solve this is to zip together the original sequence with itself shifted by one so that the each item can compare itself with the one before:
```
def compact(sequence):
    """Return new iterable with adjacent duplicate values removed."""
    deduped = []
    for item, previous in zip(sequence, [object(), *sequence]):
        if item != previous:
            deduped.append(item)
    return deduped
```
Note that we're using `*` unpacking inside a list. This feature was added in Python 3.5. The `*` here is "unpacking" each of the items in our sequence into this new list, after object(). We're making object() the first item in our second list because a new object will not be compared as equal to any of the items in sequence (each object is only equal to itself by default).

This solution is weird and maybe a little too clever. I think I prefer the index-based solution more than this one, even though I love using the zip function.

Let's take a look at the first bonus.

### Bonus #1
So our first bonus requires that we accept any iterable, not just sequences. Our first two solutions don't work with any iterable. The first one only works with iterables that can be indexed from 0 upward (like lists, tuples, strings, and other sequences). Our second solution doesn't work with lazy iterables (which will be consumed immediately after the * operator loops over our iterable, so the zip results will be empty).

We could try converting our incoming iterable to a list and then looping over it the same way as before:
```
def compact(iterable):
    """Return new iterable with adjacent duplicate values removed."""
    sequence = list(iterable)
    deduped = []
    for i, item in enumerate(sequence):
        if i == 0 or item != sequence[i-1]:
            deduped.append(item)
    return deduped
```
But this isn't very efficient because we're creating a new list just to loop over it once and discard it.

If we want our solution to work with all iterables in an efficient way, we need to loop over our iterable once (unlike our zip solution) and not depend on indexing (unlike the solution above):
```
def compact(iterable):
    """Return new iterable with adjacent duplicate values removed."""
    deduped = []
    previous = None
    for item in iterable:
        if item != previous:
            deduped.append(item)
            previous = item
    return deduped
```
This doesn't pass our tests yet though! This doesn't work because if our list starts with None values, we've got a problem because previous starts at None so it'll look like the first value from our iterable should be removed.

We could fix this by setting a variable to keep track of whether we're at the beginning of our iterable:
```
def compact(iterable):
    """Return new iterable with adjacent duplicate values removed."""
    deduped = []
    first = True
    for item in iterable:
        if first or item != previous:
            deduped.append(item)
            previous = item
            first = False
    return deduped
```
This "first" variable is True only at the very beginning, ensuring that we always add the first item instead of checking a "previous" variable.

We could also take the approach of setting our "previous" variable to a value that is only every equal to itself (like the object() thing we used before):
```
def compact(iterable):
    """Return new iterable with adjacent duplicate values removed."""
    deduped = []
    previous = object()
    for item in iterable:
        if item != previous:
            deduped.append(item)
            previous = item
    return deduped
```
This is nice because we don't have to both check for "first" and compare each item to the previous one each time we loop.

I mentioned that object() thing briefly above, but I'd like to briefly demonstrate why we're using that.

Our "previous" variable has to be initialized to some value before we can read it. If we set "previous = None" (for example), then this will happen:
```
>>> compact([None, None, 1, 2, 2, 3])
[1, 2, 3]
```
This happens because if our previous value is set to something that might be seen as "equal" to the first value in our list, then those first values will be removed. That's no good!

If we set "previous = object()", then previous will never evaluate as "equal" to anything except for itself. This is because classes in Python define object as identity/identicalness by default. So we're only using that object() to initialize previous to a completely unique value.

Okay let's look at the second bonus.

### Bonus #2
For the second bonus we're supposed to return [an iterator](https://treyhunner.com/2018/06/how-to-make-an-iterator-in-python/). A generator function is one way to make an iterator.

We can make a generator function by changing all our list appends into yield statements instead of returning a list:
```
def compact(iterable):
    """Return new iterable with adjacent duplicate values removed."""
    previous = object()
    for item in iterable:
        if item != previous:
            yield item
            previous = item
```
We're using a generator function here. Generator functions are unlike regular functions. They return a generator object which will return items every time a yield statement is hit in our generator function.

Note that for this one our tests will actually fail if you loop over the whole iterable passed to us before starting to yield values. Take this for example:
```
def compact(iterable):
    """Return new iterable with adjacent duplicate values removed."""
    sequence = list(iterable)
    for i, item in enumerate(sequence):
        if i == 0 or item != sequence[i-1]:
            yield item
```
The tests fail for this function. The tests expect that we consume as few items as possible before we yield another value from the generator we return.

If the iterable given to our function is an iterator, that iterator will be lazy and single-use (because all iterators are). The generator returned from our function will also be lazy and single-use (because generators are iterators). We want the laziness of our function to honor the laziness of the iterator given to us so we need to loop over the given iterable item-by-item. If you'd like to learn more about how iterators and generators work, you might want to read my blog post on [how for loops work](http://treyhunner.com/2016/12/python-iterator-protocol-how-for-loops-work/) or watch my [Loop Better](https://www.youtube.com/watch?v=JYuE8ZiDPl4) conference talk.

Generator functions work in a fundamentally different way from regular functions in Python. You might want to check them out if you've never heard of them before.

Okay I want to go over one last solution to this exercise before wrapping this email up. This one involves a utility in the standard library
```
from itertools import groupby

def compact(iterable):
    return (
        item
        for item, group in groupby(iterable)
    )
```
The [itertools.groupby](https://docs.python.org/3/library/itertools.html#itertools.groupby) function is well-suited to help us write our `compact` function. I rarely find excuses to use the `groupby` function, but our `compact` function is a good use for it. The `groupby` function groups consecutive items in an iterable that are equivalent (the behavior can be customized slightly if a key function is specified, but we're not doing that). We're squashing consecutive duplicate items into just 1, so the keys coming back from the `groupby` function are all we really need to make our new iterable.

We're making a generator expression above to make sure we're returning an iterator from our function. Generator expressions are to generator functions as list comprehensions are to lists... sort of. I discussed list comprehensions and generator expressions in my [Comprehensible Comprehensions](https://www.youtube.com/watch?v=5_cJIcgM7rw) talk.

Also note that we're unpacking the values we get back from `groupby` into item and group variables using multiple assignment. [Multiple assignment can really improve the code readability](http://treyhunner.com/2018/03/tuple-unpacking-improves-python-code-readability/).

I hope you learned something new this week!
