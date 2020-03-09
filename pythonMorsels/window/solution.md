# Solution: window
###### 01/27/2020
Hey!

If you haven't attempted to solve window yet, close this email and go do that now before reading on. If you have attempted solving window, read on...

This week you needed to make a function that takes a list and returns a list of tuples, each containing "windows" of `n` consecutive items.

Let's take a look at a solution that uses indexes to look up the next n items for each item in the iterable.
```
def window(sequence, n):
    """Return list of tuples of items in given list and next n-1 items."""
    items = []
    for i in range(len(sequence)):
        if i+n <= len(sequence):
            items.append(tuple(sequence[i:i+n]))
    return items
```
Note that we're converting the slice we're getting into a tuple here. We're doing this so we get a consistent return value. Slicing a list gives us a list and slicing a string gives us a string. If we always want a tuple returned, we need to convert the slice to a tuple.

Also note that we're using `range(len(sequence))` here. You should pretty much [never use range(len(...)) in your code](http://treyhunner.com/2016/04/how-to-loop-with-indexes-in-python/). This is an exceptional case because we don't need tho actual item here, but a slice that involves the item index.

Notice that we're ignoring the case of `n=0`. Currently we return a list full of empty tuples in this case (one for each item in our list). There's no test for this case so this behavior can be ignored for now (maybe we'll define what should happen for this case in a later exercise).

This solution passes all our tests.

We could have instead solved this problem this way:
```
def window(sequence, n):
    """Return list of tuples of items in given list and next n-1 items."""
    sequences = [sequence[i:] for i in range(n)]
    return zip(*sequences)
```
Here we're making n copies of the given sequence, each with 1 fewer item than the one before it. Then we zip them together and return their items. If you're unfamiliar with zip, you might want to read the article I linked above.

Let's try the first bonus.

### Bonus #1
For the first bonus we needed to make sure our function accepted any iterable, not just sequences.

To do this we need to keep track of the `n-1` items before ours and only start adding items to our returned list once we're at the `n-th` item.

This solution works:
```
def window(iterable, n):
    """Return list of tuples of items in given iterable and next n-1 items."""
    items = []
    current = ()
    for item in iterable:
        if len(current) < n:
            current = current + (item,)
        else:
            current = current[1:] + (item,)
        if len(current) == n:
            items.append(current)
    return items
```
Here we're using the variable current to keep track of a tuple that will have an item added to the end until we have `n` items in the tuple. Once there are `n` items we'll make a new tuple with the oldest item removed and our new item added to the end.

The reason we have that awkward looking (item,) thing is that we need to make a single item tuple to add it to our existing tuple and make a new one.

We could instead make a new tuple by unpacking the current tuple into a new tuple and adding item to the end, like this:
```
def window(iterable, n):
    """Return list of tuples of items in given iterable and next n-1 items."""
    items = []
    current = ()
    for item in iterable:
        if len(current) < n:
            current = (*current, item)
        else:
            current = (*current[1:], item)
        if len(current) == n:
            items.append(current)
    return items
```
We're using `*` to [unpack one tuple into another tuple](https://treyhunner.com/2018/10/asterisks-in-python-what-they-are-and-how-to-use-them/#Asterisks_in_list_literals).

You might have tried to collapse that if statement into a single slice that took the last `n-1` items in the current tuple and then added a new one, like this:
```
def window(iterable, n):
    """Return list of tuples of items in given iterable and next n-1 items."""
    items = []
    current = ()
    for item in iterable:
        current = (*current[-(n-1):], item)
        if len(current) == n:
            items.append(current)
    return items
```
This fails our tests though. The reason this fails our tests is that when `n` is 1, `-(n-1)` will be 0, and if we slice from 0 onward, we'll get the entire tuple back. Meaning whenever `n` is 1, our tuples will expand in size instead of always only having `n` items in them.

We could get around this problem by subtracting from the length of the current tuple to make a positive.
```
def window(iterable, n):
    """Return list of tuples of items in given iterable and next n-1 items."""
    items = []
    current = ()
    for item in iterable:
        current = (*current[len(current)-n+1:], item)
        if len(current) == n:
            items.append(current)
    return items
```
But that fails another one of our tests because `len(current)-n+1` can become negative sometimes. We could fix that with the `max` function:
```
def window(iterable, n):
    """Return list of tuples of items in given iterable and next n-1 items."""
    items = []
    current = ()
    for item in iterable:
        current = (*current[max(len(current)-n+1, 0):], item)
        if len(current) == n:
            items.append(current)
    return items
```
Okay let's try the second bonus now.

### Bonus #2
For the second bonus, we needed to [return an iterator](https://treyhunner.com/2018/06/how-to-make-an-iterator-in-python/) from our function.

We can do that by turning our function into a generator function, which will cause a generator to be returned when our function is called (generators are iterators).
```
def window(iterable, n):
    current = ()
    for item in iterable:
        current = (*current[max(len(current)-n+1, 0):], item)
        if len(current) == n:
            yield current
```
The yield keyword makes our function into a generator function. It's sort of magical and that fact is a little confusing, but it's true. The presence of a yield statement magically makes our function a fundamentally different thing.

You might have noticed that we've been treating our "current" tuple sort of like it's a queue. Queues are ordered structures which can have their oldest item removed from the beginning and the newest item added to the end.

Python's collections module has a [deque](https://docs.python.org/3/library/collections.html#collections.deque) which is a double-ended queue:
```
from collections import deque

def window(iterable, n):
    """Yield tuples including iterable item and the next n-1 items."""
    current = deque(maxlen=n)
    for item in iterable:
        current.append(item)
        if len(current) == n:
            yield tuple(current)
```
We've made deque object with a maximum length of `n`. When we append something to the end of our `deque`, if it is already length `n`, it will remove the last inserted item from the beginning to make room for the new one.

Note that we have a check for the length of our deque inside our loop, but we really only need that check for the first `n-1` items. Our loop would be faster if we didn't have to do this check for every item.

We could prepopulate our deque like this:
```
from collections import deque

def window(iterable, n):
    iterator = iter(iterable)
    current = deque(maxlen=n)
    for _ in range(n):
        current.append(next(iterator))
    yield tuple(current)
    for item in iterator:
        current.append(item)
        yield tuple(current)
```
Here we're getting an iterator from our iterable and dealing with that iterator in the rest of our function. This is important because iterators are consumed as you work with them. After we've taken each item out of our iterator, it will be removed from it permanently.

We're relying on the single-use behavior of iterators here by removing the first n items and then taking the rest of the items using our for loop.

This might seem a bit uglier, but our `for` loop at the end doesn't have to do as much work this way.

We could instead populate the deque using [islice](https://docs.python.org/3/library/itertools.html#itertools.islice):
```
from collections import deque
from itertools import islice

def window(iterable, n):
    iterator = iter(iterable)
    current = deque(islice(iterator, n), maxlen=n)
    yield tuple(current)
    for item in iterator:
        current.append(item)
        yield tuple(current)
```
The `islice` utility will grab the next `n` items in our iterator, which is exactly what we were doing manually in our `for` loop before.

If you're not familiar with `collections.deque` and `itertools.islice`, I'd highly recommend looking into both of them. They're very useful helpers.

That `deque`/`islice` solution is my preferred solution. But I'd like to show another solution to this problem that is very clever and possibly a little confusing:
```
from itertools import islice, tee

def window(iterable, n):
    """Yield tuples including iterable item and the next n-1 items."""
    iterators = [
        islice(iterator, i, None)
        for i, iterator in enumerate(tee(iterable, n))
    ]
    return zip(*iterators)
```
If you haven't seen [tee](https://docs.python.org/3/library/itertools.html#itertools.tee) before, this is likely quite confusing. It's a little tricky to explain what `tee` does.

Essentially we're getting `n` iterators over our iterable, which appear to move independently because they cache the values they receive from the given iterable.

The `for` loop we've written is removing 0 items from the first iterator, 1 item from the next, and so on until it removes `n-1` items from the `n-th` iterator. Then we `zip` the iterators together to get our answer. This use of `zip` is very similar to the one we used above. We're essentially working with slices of these iterables here instead of slices of sequences.

I find these solutions with tee to be overly clever. I rarely find that the itertools.tee utility improves code clarity, so **I usually recommend against using tee**.

My preferred solution to this problem is this one:
```
from collections import deque
from itertools import islice

def window(iterable, n):
    iterator = iter(iterable)
    current = deque(islice(iterator, n), maxlen=n)
    yield tuple(current)
    for item in iterator:
        current.append(item)
        yield tuple(current)
```
I hope you learned something from this somewhat lengthy adventure.
