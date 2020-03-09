# reformat_diary.py
##### Assigned from Mixed Level on 02/17/2020
Hi again! 👋

I kept a daily diary on a site called Ohlife. They shutdown, but they allowed me to export all my data before they shut down. The export was a very large text file and unwieldy. So I decided to write a Python script to help me split it up into separate files, one for each day. This week I want you to do something similar.

I'd like you to write a Python script, `reformat_diary.py`, that turns a text file with many diary entries into many text files, one for each entry.

I'd like you to start by writing a function called `entries_by_date` which will take diary text file and return a list of two-tuples containing the date and corresponding entry.

If our file `my_diary.txt` contains the following:
```
2018-01-01

Coded.

Did laundry.

2018-01-02

Slept all day.
```
We should be able to call our function like this:
```
>>> diary_file = open('my_diary.txt')
>>> entries_by_date(diary_file)
[("2018-01-01", "Coded.\n\nDid laundry."), ("2018-01-02", "Slept all day.")]
```
## Bonus 1

For the first bonus I'd like you to clean up HTML artifacts in the diary entries. ✔️

I'd like you to modify your `entries_by_date` function so that it replaces three HTML escape entities (`&nbsp;` and `&quot;` and `&amp;`) with their plain text equivalents (space character, single quote character, and ampersand).

Assuming the following file contents:
```
2018-01-01

I said &quot;rabbit, rabbit&quot; today.

2018-01-02

I slept all day.  I ate fish & chips.
```
The function should work like this:
```
>>> diary_file = open('my_diary.txt')
>>> for date, entry in entries_by_date(diary):
...     print(f"Entry for {date}\n")
...     print(f"{entry}\n")
...
```
```
Entry for 2018-01-01

I said "rabbit, rabbit" today.

Entry for 2018-01-02

I slept all day.  I ate fish & chips.
```
You may want to write a helper function to help with the HTML entity handling.

## Bonus 2

For the second bonus, create a "main" function that accepts the diary filename and creates a separate file for each diary entry. ✔️

The new files should be in the current working directory and the name of each file should be `YYYY-MM-DD.txt`, where `YYYY-MM-DD` is the date of the entry. The contents of the file should be the entry text.

So if the original diary is named `my_diary.txt` and it contains:
```
2018-01-01

Coded today.

2018-01-02

Slept
```
Then your `main` function should accept `my_diary.txt` and should create two new files: `2018-01-01.txt` containing "Coded today." and `2018-01-02.txt` containing "Slept".

You'll need to do a bit more to accept a command-line argument and pass it to your main function. Your final working program should work like this:
```
$ python reformat_diary.py my_diary.txt
2018-01-01.txt written
2018-01-02.txt written
```
## Hints

1. [Removing trailing character from a line](https://python-reference.readthedocs.io/en/latest/docs/str/rstrip.html)
2. [Regular expression to match a date](https://stackoverflow.com/a/9978804/2633215)
3. [Bonus 1: Replacing substrings](https://stackoverflow.com/a/8270146/2633215)
4. [Bonus 1: Escaping HTML entities](https://stackoverflow.com/a/2360639/2633215)
5. [Bonus 2: How to access command-line arguments](https://stackoverflow.com/a/35421024/2633215)

## Tests

Automated tests for this week's exercise [can be found here](https://www.pythonmorsels.com/exercises/60e1acc807664feb8597712ac6a314f5/tests/). You'll need to write your code in a module named reformat_diary.py next to the test file. To run the tests you'll run "python test_reformat_diary.py" and check the output for "OK". You'll see that there are some "expected failures" (or "unexpected successes" maybe). If you'd like to do the bonus, you'll want to comment out the noted lines of code in the tests file to test them properly.
