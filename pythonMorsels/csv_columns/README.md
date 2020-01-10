# csv_columns
#### Assigned from Mixed Level on 12/30/2019

Hello there! 👋

I'd like you to write a function called `csv_columns` that accepts a file object and returns a dictionary mapping CSV headers to column data for each header.

If our CSV file contains this data:
```
h1,h2
1,2
3,4
```
Then our function will work like this:
```
>>> csv_columns(open('my_file.csv'))
{'h1': ['1', '3'], 'h2': ['2', '4']}
```
Your `csv_columns` function shouldn't close the file given to it.

Note that the CSV data may have commas inside the cells as well, so a simple split-by-commas won't work.

### Bonus 1

For the first bonus, I'd like you to make sure the dictionary returned from `csv_columns` is has keys in the same order as the columns that were in our CSV file. ✔️

Depending on your version of Python this may be easy or it may require digging through the standard library.

### Bonus 2

For the second bonus, you should allow an optional `headers` argument to our function. ✔️
```
>>> csv_columns(open('my_file.csv'), headers=['header1', 'header2'])
{'header1': ['h1', '1', '3'], 'header2': ['h2', '2', '4']}
```
If no headers are provided, the first row is used as the headers just as before.

### Bonus 3

For the third bonus, make `csv_columns` work with CSV files that have different numbers of columns in each row. ✔️

Missing values should default to `None`, but you should also accept an optional `missing` argument to allow a different value to be used.

So, if our CSV file contains this data:
```
h1,h2
1,2
3,4
5,
```
Then it will fill in the missing value at the end of the second column with the given `missing` value:
```
>>> csv_columns(open('my_file.csv'), missing='0')
{'h1': ['1', '3', '5'], 'h2': ['2', '4', '0']}
>>> csv_columns(open('my_file.csv'))
{'h1': ['1', '3', '5'], 'h2': ['2', '4', None]}
```
## Hints

1. [Reading CSV files in Python](https://pymotw.com/3/csv/index.html)
2. [Getting the header for each data cell](https://treyhunner.com/2016/04/how-to-loop-with-indexes-in-python/#zip)
3. [Setting default values in a dictionary](https://docs.quantifiedcode.com/python-anti-patterns/correctness/not_using_setdefault_to_initialize_a_dictionary.html)
4. [Transposing all of the CSV rows at once](https://stackoverflow.com/a/49103300/2633215)
5. [Reading CSV files by their header names](https://stackoverflow.com/a/29432995/2633215)
6. [Bonus 1: A hint for Python 3.5 and below](https://pymotw.com/3/collections/ordereddict.html)
7. [Bonus 3: a helper for looping over different length iterables](https://docs.python.org/3/library/itertools.html#itertools.zip_longest)
8. [Bonus 3: an even simpler way to solve bonus 3](https://stackoverflow.com/a/34777914/2633215)

## Tests

Automated tests for this week's exercise [can be found here](https://www.pythonmorsels.com/exercises/850c1b8fce804b3ea99fffe26273051d/tests/). You'll need to write your function in a module named csv_columns.py next to the test file. To run the tests you'll run "python test_csv_columns.py" and check the output for "OK". You'll see that there are some "expected failures" (or "unexpected successes" maybe). If you'd like to do the bonus, you'll want to comment out the noted lines of code in the tests file to test them properly.
