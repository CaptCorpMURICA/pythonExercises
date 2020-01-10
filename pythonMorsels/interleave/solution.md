# Solution: interleave
###### 01/06/2020
Hey!

If you haven't attempted to solve interleave yet, close this email and go do that now before reading on. If you have attempted solving interleave, read on...

If we want to loop over two lists at the same time, you might think to use `range(len(...))` to look up corresponding items as you loop:
```
def interleave(sequence1, sequence2):
    """Return iterable of one item at a time from each list."""
    interleaved = []
    for i in range(len(sequence1)):
        interleaved.append(sequence1[i])
        interleaved.append(sequence2[i])
    return interleaved
```
But this isn't very idiomatic.

Whenever you see `range(len(...))` you should probably consider using `enumerate(...)` instead, which gives us both an index and items from the iterable we're looping over:
```
def interleave(iterable1, iterable2):
    """Return iterable of one item at a time from each list."""
    interleaved = []
    for i, item in enumerate(iterable1):
        interleaved.append(item)
        interleaved.append(iterable2[i])
    return interleaved
```
But this also isn't the most idiomatic way to solve this problem.

We're using indexes here to loop over two lists at the same time. Python actually has a built-in zip function for doing exactly that:
```
def interleave(iterable1, iterable2):
    interleaved = []
    for item1, item2 in zip(iterable1, iterable2):
        interleaved.append(item1)
        interleaved.append(item2)
    return interleaved
```
The zip function gives us back an iterable which provides tuples of two items as we loop over it. We're unpacking the items into item1 and item2 and then appending each to the new list.

If you ever find yourself wanting to loop over multiple lists at once, don't reach for enumerate or indexes in general, reach for zip.

If you're not familiar with zip or enumerate and their uses, you might want to read (or re-read) [this article on looping in Python with indexes](https://treyhunner.com/2016/04/how-to-loop-with-indexes-in-python/).

Let's talk about the first bonus.

## Bonus #1
For the first bonus we were supposed to accept any iterables to our function, not just lists or other sequences.

When we were using indexes to loop over two sequences at the same time, that would have been a problem. But now that we're using zip, our function actually already passes this first bonus. The zip function doesn't use indexes to loop over multiple items at once, it's a bit more sophisticated than that.

So the solution we already have works:
```
def interleave(iterable1, iterable2):
    interleaved = []
    for item1, item2 in zip(iterable1, iterable2):
        interleaved.append(item1)
        interleaved.append(item2)
    return interleaved
```
You might think could we use a list comprehension for this? After all, we've got an empty list and a loop and we're appending and.

This does look like something that could be turned into a list comprehension but we have a problem: we have two append calls! If we want to get closer to being able to [copy-paste our way into a list comprehension](http://treyhunner.com/2015/12/python-list-comprehensions-now-in-color/), we could do this:
```
def interleave(iterable1, iterable2):
    interleaved = []
    for item1, item2 in zip(iterable1, iterable2):
        for item in [item1, item2]:
            interleaved.append(item)
    return interleaved
```
We now only have one append in our loops.

If we copy-paste this into a list comprehension, we'll get this:
```
def interleave(iterable1, iterable2):
    interleaved = [
        item
        for item1, item2 in zip(iterable1, iterable2)
        for item in [item1, item2]
    ]
    return interleaved
```
Notice that this list comprehension has two for clauses. This isn't two list comprehensions nested inside each other, it's one list comprehension with nested for loops in it. Also notice the order of the for clauses. That order is important and it's hard to remember unless you copy-paste your way into a comprehension from for loops. If you need a refresher on list comprehensions, read [this article on list comprehensions](https://treyhunner.com/2015/12/python-list-comprehensions-now-in-color/).

You might realize that we could actually rewrite this a bit.

We could do this instead:
```
def interleave(iterable1, iterable2):
    return [
        item
        for pair in zip(iterable1, iterable2)
        for item in pair
    ]
```
Here we're returning our list right away instead of assigning it to an unnecessary and unhelpful variable. We're also capturing the response from zip into an items variable instead of unpacking the items into two values and then repacking them to loop over each.

We'll look at one last solution for this bonus.

Instead of using a list comprehension, we could take the items from zip and extend our list using the list extend method or the `+=` operator:
```
def interleave(iterable1, iterable2):
    interleaved = []
    for items in zip(iterable1, iterable2):
        interleaved += items
    return interleaved
```
When you see an empty list being appended to over and over in a for loop, you should think of list comprehensions. When you see "`some_list.extend(an_iterable)`" or "`some_list += an_iterable`", you won't be able to convert that to a list comprehension directly. We need to unroll that in order to copy-paste our way into a comprehension.
```
def interleave(iterable1, iterable2):
    interleaved = []
    for items in zip(iterable1, iterable2):
        for item in items:
            interleaved.append(item)
    return interleaved
```

## Bonus #2
Let's attempt about the second bonus now.

For the second bonus we were asked to return an iterator (a lazy iterable) from our function instead of a list.

We could make a lazy iterable instead of a list by turning our list comprehension into a generator expression. We can do this by replacing the square brackets with parentheses:
```
def interleave(iterable1, iterable2):
    return (
        item
        for pair in zip(iterable1, iterable2)
        for item in pair
    )
```
This function returns a generator object instead of a list.

If we decided we didn't like that extra loop, we could use `itertools.chain`:
```
from itertools import chain

def interleave(iterable1, iterable2):
    return chain.from_iterable(pair for pair in zip(iterable1, iterable2))
```
I've collapsed this generator expression to a single line here because it's short enough that it's not particularly difficult to read this way.

Also note that we don't have two sets of parenthesis around this generator expression, one for the function call and one for the generator expression inside. Python actually allows us to remove extra parenthesis for generator expressions when they're the only thing inside a function call.

This isn't a great solution though. Mostly because we don't actually need this generator expression.

We could instead do this:
```
from itertools import chain

def interleave(iterable1, iterable2):
    return chain.from_iterable(zip(iterable1, iterable2))
```
Whenever you see a generator expression that doesn't change anything or filter anything, remove it. That generator expression was pointless because we can just loop over the original iterable instead.

If you're curious about `chain.from_iterable` vs `chain`, you can think of `chain.from_iterable(iterable)` as `chain(*iterable)` except more efficient. More in the [documentation on chain](https://docs.python.org/3/library/itertools.html#itertools.chain.from_iterable).

Another way to get our function to return a generator object is to create a generator function.

The easiest way to do that is to take our solution with two appends and replace the appends with yield statements.

Here's our two append solution:
```
def interleave(iterable1, iterable2):
    interleaved = []
    for item1, item2 in zip(iterable1, iterable2):
        interleaved.append(item1)
        interleaved.append(item2)
    return interleaved
```
We can replace the appends with yield statements and remove the list entirely.
```
def interleave(iterable1, iterable2):
    for item1, item2 in zip(iterable1, iterable2):
        yield item1
        yield item2
```
We don't need a list in that example because our generator function will return a generator that will yield new items as it's looped over.

This is probably my favorite solution to this problem because I find it the most clear.

My next favorite solution is the last `chain.from_iterable` solution, which I find fairly clear as well.

I hope you learned something this week!
