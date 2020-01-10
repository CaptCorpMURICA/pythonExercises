# Solution: csv_columns
###### 12/30/2019
Hey friend!

If you haven't attempted to solve `csv_columns` yet, close this email and go do that now before reading on. If you have attempted solving `csv_columns`, read on...

This week you needed to make a function that takes a file object containing CSV data and returns a dictionary mapping headers (as keys) to corresponding lists of column data (as values).

A first attempt at this problem might look like this:
```
def csv_columns(csv_file):
    """Return dictionary mapping headers to corresponding columns."""
    headers = None
    columns = {}
    for line in csv_file:
        row = line.rstrip().split(',')
        if headers is None:
            headers = row
            continue
        for header, item in zip(headers, row):
            if header not in columns:
                columns[header] = []
            columns[header].append(item)
    return columns
```
Here we're looping over each line in our file, stripping whitespace from the right-hand side (to remove any linefeed and carriage return characters from the end of the line) and splitting on commas to get a list of the cells in a single row.

If the headers variable is at its default of `None` then we treat this first row as a header row and then skip to the new row.

Otherwise, we zip together our headers and the current row and add each item to a list linked to its corresponding header. This requires an if statement to check whether we've seen that header yet.

This doesn't quite pass our tests though. The reason is that splitting on commas won't allow us to parse all CSV files. CSV cells are allowed to have commas within them if the cell is surrounded in double quotes.

We need to use the reader class from the csv module to get our tests to pass:
```
import csv

def csv_columns(csv_file):
    """Return dictionary mapping headers to corresponding columns."""
    reader = csv.reader(csv_file)
    headers = None
    columns = {}
    for row in reader:
        if headers is None:
            headers = row
            continue
        for header, item in zip(headers, row):
            if header not in columns:
                columns[header] = []
            columns[header].append(item)
    return columns
```
The `csv.reader` class accepts a file object and gives us back a list of the data for each row as we loop over it.

This now passes our tests.

We could shorten that awkward "header not in columns" check that makes a new empty list each time we loop:
```
import csv

def csv_columns(csv_file):
    """Return dictionary mapping headers to corresponding columns."""
    reader = csv.reader(csv_file)
    headers = None
    columns = {}
    for row in reader:
        if headers is None:
            headers = row
            continue
        for header, item in zip(headers, row):
            columns.setdefault(header, []).append(item)
    return columns
```
The dictionary `setdefault` method was made for use cases like this. It's not the most efficient though, because we create an empty list unnecessarily for most iterations of this loop (anytime after the first non-header row.

We could instead create an empty list in our dictionary for each header immediately after seeing our header row:
```
import csv

def csv_columns(csv_file):
    """Return dictionary mapping headers to corresponding columns."""
    reader = csv.reader(csv_file)
    headers = None
    for row in reader:
        if headers is None:
            headers = row
            columns = {
                header: []
                for header in headers
            }
            continue
        for header, item in zip(headers, row):
            columns.setdefault(header, []).append(item)
    return columns
```
We're using a dictionary comprehension to do this.

Note that if we instead tried to use `dict.fromkeys` to create an empty list value in our dictionary for each header key, we'd get an error:
```
        if headers is None:
            headers = row
            columns = dict.fromkeys(headers, [])
            continue
```
This is a problem because it would assign each header key to the same empty list value. Meaning when we append to one of these lists, all of them will change because they're actually all the same list!

Here's a different approach to this problem:
```
import csv

def csv_columns(csv_file):
    """Return dictionary mapping headers to corresponding columns."""
    reader = csv.reader(csv_file)
    headers = None
    rows = []
    for row in reader:
        if headers is None:
            headers = row
            continue
        rows.append(row)
    columns = {}
    for header, *column in zip(headers, *rows):
        columns[header] = column
    return columns
```
Here we're collecting our header list and then a list of all our rows first. Then we're zipping together our headers and all of our rows at once to collect the header with its corresponding column. We're using that to build up our new columns dictionary.

That `*` syntax in our [tuple unpacking](http://treyhunner.com/2018/03/tuple-unpacking-improves-python-code-readability/) is a Python 3 only feature.

We could improve this even more by popping the header off of our `csv.reader` object before we loop over the rest of the rows:
```
import csv

def csv_columns(csv_file):
    """Return dictionary mapping headers to corresponding columns."""
    reader = csv.reader(csv_file)
    headers = next(reader)
    rows = []
    for row in reader:
        rows.append(row)
    columns = {}
    for header, *column in zip(headers, *rows):
        columns[header] = column
    return columns
```
The built-in next function allows us to increment an iterator one step, grabbing the next item from it. Python's `csv.reader` objects, like Python's file objects, are iterators. So we can take our headers off first and then deal with just the rest of our rows afterward.

You might notice that both of these "for" loops look like they could be [copy-pasted into a list comprehension](http://treyhunner.com/2015/12/python-list-comprehensions-now-in-color/):
```
import csv

def csv_columns(csv_file):
    """Return dictionary mapping headers to corresponding columns."""
    reader = csv.reader(csv_file)
    headers = next(reader)
    rows = [row for row in reader]
    return {
        header: column
        for header, *column in zip(headers, *rows)
    }
```
I find this code much clearer. It's clear that we're working with a list of rows and that our function ultimately returns a dictionary.

That list comprehension we've written is actually an anti-pattern of sorts though!

Whenever you see something like this:
```
    rows = [row for row in reader]
```
You can always replace that with this:
```
    rows = list(reader)
```
Passing an iterable to the list constructor will loop over it and make a new list from it. That's exactly what that list comprehension was doing.

This is still an anti-pattern of sorts though because we don't actually care whether we're working with a list of rows. We care that we can iterate over our rows. But that's what the reader object gives us back as we loop over it.

So we could just loop over our reader object directly:
```
import csv

def csv_columns(csv_file):
    """Return dictionary mapping headers to corresponding columns."""
    reader = csv.reader(csv_file)
    headers = next(reader)
    return {
        header: column
        for header, *column in zip(headers, *reader)
    }
```
This is more efficient and I'd argue that it's more idiomatic.

Turning an iterable into a list just to loop over it once and discard it is a little silly. If you're only going to loop over an iterable once, it doesn't matter whether it's a list or not.

There's a tool you might have discovered while searching for how to solve this: `csv.DictReader`. We're trying to make a dictionary of the columns, but `DictReader` gives us an iterable of dictionaries for each row. That isn't quite what we're looking for but it might still be helpful.
```
from collections import defaultdict
from csv import DictReader


def csv_columns(csv_file):
    columns = defaultdict(list)
    reader = DictReader(csv_file)
    for row in reader:
        for name, value in row.items():
            columns[name].append(value)
    return columns
```
The row variable in our loop will be a dictionary mapping headers to values for a specific row. We're taking each of the key-value pairs in that dictionary and appending them to the corresponding headers list in our columns dictionary.

We're using `collections.defaultdict` here so that our columns dictionary will add a new list to our dictionary whenever we access a key that hasn't been seen yet.

## Bonus #1
For this bonus you were supposed to make sure your `csv_columns` function always returned dictionary items in the same order as the columns in our CSV file.

Regular dictionaries were unordered before Python 3.6 so you needed to use a custom `OrderedDict` dictionary-like object to do this:
```
from collections import OrderedDict
import csv

def csv_columns(csv_file):
    """Return dictionary mapping headers to corresponding columns."""
    reader = csv.reader(csv_file)
    headers = next(reader)
    return OrderedDict([
        (header, column)
        for header, *column in zip(headers, *reader)
    ])
```
In Python 3.6 and greater you can just use the built-in `dict` type though. So your tests may already be passing for this bonus if you're on the latest version of Python!

## Bonus #2
For the second bonus, an optional headers argument was supposed to be accepted.

We can modify our code to accept headers like this:
```
import csv

def csv_columns(csv_file, headers=None):
    """Return dictionary mapping headers to corresponding columns."""
    reader = csv.reader(csv_file)
    if headers is None:
        headers = next(reader)
    return {
        header: column
        for header, *column in zip(headers, *reader)
    }
```
This headers argument will work either positionally or as a keyword argument.

If no headers are specified, the first row is assumed to be the headers, just as before.

Note that we could have written this instead:
```
import csv

def csv_columns(csv_file, headers=None):
    """Return dictionary mapping headers to corresponding columns."""
    reader = csv.reader(csv_file)
    if not headers:
        headers = next(reader)
    return {
        header: column
        for header, *column in zip(headers, *reader)
    }
```
Here we're relying on the truthiness of headers. If headers is falsey, we take the first row as headers.

This has a slightly different meaning though. If headers were specified to be an empty list for example, we'd still assume the first row were the headers.

We didn't test for that case because passing in an empty list would be sort of an odd thing to do since we'd get an empty dictionary back if that empty list was treated as our actual headers.

So since that scenario is ill-defined, we could choose to rely on truthiness (as recommended by PEP 8) instead of checking whether our value is exactly `None` (which is not explicitly discouraged by PEP 8 and not uncommon either).

If we were using `DictReader` we could actually pass `headers` directly to it and it'll do the work of consuming that first headers row (or not) as appropriate:
```
from collections import defaultdict
from csv import DictReader


def csv_columns(rows, *, headers=None):
    columns = defaultdict(list)
    reader = DictReader(rows, fieldnames=headers)
    for row in reader:
        for name, value in row.items():
            columns[name].append(value)
    return columns
```
I don't use `DictReader` often, but it can come in handy sometimes!

## Bonus #3
This bonus was a bit trickier.

For this bonus we were supposed to accept CSV files with some columns that are shorter than others.

If some headers don't have corresponding cells in our CSV file, we're meant to fill them with None or with the "missing" value specified to our function.

Here's one way to do that:
```
import csv

def csv_columns(rows, *, headers=None, missing=None):
    """Return dictionary mapping headers to corresponding columns."""
    reader = csv.reader(rows)
    if headers is None:
        headers = next(reader)
    columns = {header: [] for header in headers}
    for row in reader:
        for i, header in enumerate(headers):
            if i < len(row):
                columns[header].append(row[i])
            else:
                columns[header].append(missing)
    return columns
```
This is kind of complicated. We're manually looping over our rows and for each row we're looping over our headers and getting a corresponding index so we can either grab the row item (if the row is long enough) or fill in the missing value if the row is too short.

Instead of using an "if" statement to deal with the row items or missing values, we could use a try-except:
```
import csv

def csv_columns(rows, *, headers=None, missing=None):
    """Return dictionary mapping headers to corresponding columns."""
    reader = csv.reader(rows)
    if headers is None:
        headers = next(reader)
    columns = {header: [] for header in headers}
    for row in reader:
        for i, header in enumerate(headers):
            try:
                columns[header].append(row[i])
            except IndexError:
                columns[header].append(missing)
    return columns
```
Here we're optimizing for the case where there are few missing items. Handling exceptions takes longer than checking a condition, but if it doesn't happen often that extra time cost may be worth it.

If we don't need to worry much about time, I'd probably recommend the try- except approach anyway because we're practicing EAFP instead of LBYL and practicing [EAFP](https://docs.python.org/3/glossary.html#term-eafp) is often recommended in Python.

A more idiomatic approach to this problem involves reverting back to using zip, but using a variation of zip called zip_longest, in the itertools module:
```
import csv
from itertools import zip_longest

def csv_columns(rows, *, headers=None, missing=None):
    """Return dictionary mapping headers to corresponding columns."""
    reader = csv.reader(rows)
    if headers is None:
        headers = next(reader)
    return {
        header: column
        for header, *column in zip_longest(headers, *reader, fillvalue=missing)
    }
```
The `itertools.zip_longest` function allows us to specify a "fill value" that should be used if our rows don't all have the same number of items.

Here's another way to write this:
```
import csv
from itertools import zip_longest

def csv_columns(rows, *, headers=None, missing=None):
    """Return dictionary mapping headers to corresponding columns."""
    reader = csv.reader(rows)
    if headers is None:
        headers = next(reader)
    columns = zip_longest(*reader, fillvalue=missing)
    return {
        header: list(column)
        for header, column in zip(headers, columns)
    }
```
This is nearly the same, but actually slightly different in functionality.

If we have fewer headers than columns, our function before would have filled in the missing value for our headers, which may have been a bit odd.

Here if we have fewer headers than columns, we'll truncate our column data to just the initial headers specified.

Note that we're passing "column" to the list constructor in our dictionary comprehension on that last line. We're doing this because zip returns tuples back to us, but we want lists. Using tuple unpacking with * gave us lists before, but we're relying on the items in zip directly here so we need to convert each tuple to a list to get our tests to pass (our tests are possibly needlessly picky here).

I prefer this solution over the others for this bonus.

There's one solution I prefer the most though. It involves `DictReader` again:
```
from collections import defaultdict
from csv import DictReader


def csv_columns(rows, *, headers=None, missing=None):
    columns = defaultdict(list)
    reader = DictReader(rows, fieldnames=headers, restval=missing)
    for row in reader:
        for name, value in row.items():
            columns[name].append(value)
    return columns
```
The `DictReader` class accepts a `restval` argument which does pretty much exactly what our missing argument is supposed to do!

I hope you got some good practice with zip, iterators, tuple unpacking, csv files, or comprehensions this week!
