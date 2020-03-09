# Solution: chunked
###### 02/03/2020
Heya,

If you haven't attempted to solve chunked yet, close this email and go do that now before reading on. If you have attempted solving chunked, read on...

This week you needed to write a function that "chunks" a sequence (and eventually any iterable) into `n` pieces.

Here's one way to do this:
```
def chunked(sequence, n):
    """Return n-length chunks of the given sequence."""
    chunks = []
    for i in range(0, len(sequence), n):
        chunks.append(sequence[i:n+i])
    return chunks
```
Here we're using `range` to take `n`-sized steps from `0` until the length of our sequence so that we can take `n`-sized slices from our sequence and append them to a new list.

This could be [copy-pasted into a list comprehension](https://treyhunner.com/2015/12/python-list-comprehensions-now-in-color/) like this:
```
def chunked(sequence, n):
    """Return n-length chunks of the given sequence."""
    return [
        sequence[i:n+i]
        for i in range(0, len(sequence), n)
    ]
```
In general [when we need indexes](https://treyhunner.com/2016/04/how-to-loop-with-indexes-in-python/) we should use `enumerate`. The reason we're not using `enumerate` here is that we aren't looking at the individual items at all but instead calculating indexes for the sake of making slices.

This is one of the rare cases where using `range` to get an index is actually appropriate.

### Bonus #1
For the first bonus we needed to allow our function to work with any iterable, not only sequences.

One way to do this is to cheat and make a tuple out of whatever iterable is passed into our function:
```
def chunked(iterable, n):
    """Return n-length chunks of the given iterable."""
    sequence = tuple(iterable)
    return [
        sequence[i:n+i]
        for i in range(0, len(sequence), n)
    ]
```
We're building up a tuple of all the given items just to slice this tuple and then discard it.

Here's an approach that doesn't involve making a tuple:
```
def chunked(iterable, n):
    """Return n-length chunks of the given iterable."""
    chunks = []
    chunk = []
    for item in iterable:
        if len(chunk) < n:
            chunk.append(item)
        if len(chunk) == n:
            chunks.append(chunk)
            chunk = []
    if chunk:
        chunks.append(chunk)
    return chunks
```
Here we're building up a list of chunks, by building each chunk one item at a time. We're looping item-by-item and appending our chunk to our `chunks` list once it's reached a length of `n` and then starting over with an empty chunk.

Here's another way to do solve this bonus:
```
from itertools import zip_longest


def chunked(iterable, n):
    """Return n-length chunks of the given iterable."""
    iterators = [iter(iterable)] * n
    chunks = zip_longest(*iterators)
    return [
        [x for x in chunk if x is not None]
        for chunk in chunks
    ]
```
Here we're making a list that contains the same exact iterator object `n` times. Then we're using `zip_longest` to loop over items in these iterators (which are all the same iterator) at the same time. This will effectively give us `n` sized chunks of our iterable.

Except we also need to filter out any extra values at the end of our iterable. We can do this by checking for `None` values (which is what `zip_longest` will fill the end of our last chunk with by default).

Here's a better way to do this:
```
from itertools import zip_longest


def chunked(iterable, n):
    """Return n-length chunks of the given iterable."""
    sentinel = object()
    iterators = [iter(iterable)] * n
    chunks = zip_longest(*iterators, fillvalue=sentinel)
    return [
        [x for x in chunk if x is not sentinel]
        for chunk in chunks
    ]
```
This is better because if our iterable ends in `None` values, we won't accidentally strip them from the end of our last chunk.

Both of these answers are using a comprehension with a comprehension inside it to build up a lits of lists.

Here's another way to solve this, this time building up a list of tuples:
```
from itertools import islice


def chunked(iterable, n):
    """Return n-length chunks of the given iterable."""
    iterator = iter(iterable)
    chunks = []
    while True:
        items = tuple(islice(iterator, n))
        if not items:
            break
        chunks.append(items)
    return chunks
```
Here we're using `islice` (also from the `itertools` module) to grab the next `n` values from an iterator over and over.

If these iterator things are confusing, you might want to take a look at [my blog post on iterators](https://treyhunner.com/2016/12/python-iterator-protocol-how-for-loops-work/) or watch my [Loop Better](https://www.youtube.com/watch?v=JYuE8ZiDPl4) talk.

### Bonus #2
For the second bonus, you needed to return an iterator from your function. By this I meant that your function should be a lazy iterable helper function, just like the types of functions you'll find in the `itertools` module.

One way to do this is to take our `zip_longest` answer that used list comprehensions and instead use a generator expression:
```
from itertools import zip_longest


def chunked(iterable, n):
    """Return n-length chunks of the given iterable."""
    sentinel = object()
    iterators = [iter(iterable)] * n
    chunks = zip_longest(*iterators, fillvalue=sentinel)
    return (
        [x for x in chunk if x is not sentinel]
        for chunk in chunks
    )
```
Generator expressions are one way to make an iterator. Another is to make a generator function (see [my article on making an iterator](https://treyhunner.com/2018/06/how-to-make-an-iterator-in-python/)).
```
from itertools import islice


def chunked(iterable, n):
    """Return n-length chunks of the given iterable."""
    iterator = iter(iterable)
    while True:
        items = tuple(islice(iterator, n))
        if not items:
            return
        yield items
```
This is the same `islice`-based solution as before but instead of appending to a list, we're yielding items from our generator function.

If you prefer the solution that didn't use `islice` or `zip_longest` but just relied on a list of lists, you could instead to this:
```
def chunked(iterable, n):
    """Return n-length chunks of the given iterable."""
    chunk = []
    for item in iterable:
        chunk.append(item)
        if len(chunk) == n:
            yield chunk
            chunk = []
    if chunk:
        yield chunk
```
Here we're building up a `chunk` list and to `yield` it from our generator function.

### Bonus #3
For the third bonus you needed to accept a `fill` value as a keyword only argument.

We can modify our `islice`-based solution like this:
```
from itertools import islice


SENTINEL = object()


def chunked(iterable, n, *, fill=SENTINEL):
    """Return n-length chunks of the given iterable."""
    iterator = iter(iterable)
    while True:
        chunk = tuple(islice(iterator, n))
        if not chunk:
            return
        if fill is not SENTINEL and len(chunk) < n:
            chunk += (fill,) * (n-len(chunk))
        yield chunk
```
Here we're defaulting `fill` to a unique object. We're doing this so that we can tell whether `fill` has been passed to us or not.

Another way to do this would be to accept `**kwargs` and just check for `fill`, but that's a bit more awkward since we're accepting any keyword arguments at all in that case, even though we won't use them.

That `*,` probably looks funny if you've never seen it. That's a way to note that `fill` is a keyword-only argument. You may want to read my [article on keyword arguments](https://treyhunner.com/2018/04/keyword-arguments-in-python/) if you're unfamiliar with that syntax.

Here's how we could do this with our `zip_longest` solution:
```
from itertools import zip_longest


SENTINEL = object()


def chunked(iterable, n, *, fill=SENTINEL):
    """Return n-length chunks of the given iterable."""
    iterators = [iter(iterable)] * n
    chunks = zip_longest(*iterators, fillvalue=fill)
    return (
        [x for x in chunk if x is not SENTINEL]
        for chunk in chunks
    )
```
I prefer this `zip_longest` solution personally. The `iterators` list is a little clever, which might make for some confusion for newer Python programmers though.
