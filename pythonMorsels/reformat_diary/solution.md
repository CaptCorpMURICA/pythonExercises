# Solution: reformat_diary.py
###### 02/17/2020
Hey!

If you haven't attempted to solve `reformat_diary.py` yet, close this email and go do that now before reading on. If you have attempted solving `reformat_diary.py`, read on...

This week you were supposed to start creating a program that would split up a single diary file into many separate diary files.

The first step was to create an `entries_by_date` function that accepts a diary file and returns a list of tuples containing the date (as a string) and the entry (as text).

Here's one solution:
```
def entries_by_date(diary_file):
    entries_by_date = []
    for line in diary_file:
        if line.startswith('201'):
            entries_by_date.append((line.rstrip(), ""))
        else:
            date, entry = entries_by_date[-1]
            entries_by_date[-1] = (date, f"{entry}{line}")
    return [
        (date, entry.strip())
        for (date, entry) in entries_by_date
    ]
```
Here we're building up an `entries_by_date` list. For each line in our file we either add a new item to our list or we modify the last item.

Whenever we see a line that looks like a date (it starts with `201`) we append a tuple to our `entries_by_date` list that contains the date (from the current line) and an empty string.

When we see a line that doesn't start with a date, we unpack the last item in `entries_by_date`, create a new tuple by adding which has one more line concatenated to our string, and then assign that new tuple back into the last item in our list.

After we've built up our `entries_by_date` list we loop over it to strip the extra linebreaks from the beginning and end of our entry.

You might notice that our function is called the same thing as the list that our function contains. This name shadowing is often a bad idea, though it doesn't matter too much in this case since we're not ever calling our function recursively.

Pretty much all of our solutions that read line by line will involve logic in the form of add-an-item for a new date and modify-the-last-item otherwise.

Here's another solution:
```
def entries_by_date(diary_file):
    entries_by_date = []
    for line in diary_file:
        if line.startswith('201'):
            entry = []
            entries_by_date.append((line.rstrip(), entry))
        else:
            entry.append(line.rstrip())
    return [
        (date, "\n".join(entry).strip())
        for (date, entry) in entries_by_date
    ]
```
Here our `entries_by_date` list contains two-tuples of the date and a list, whereas before it contained two-tuples of the date and a string.

Now when we see a line that looks like a date we create a new list which we store in the variable entry and also append that list inside a new tuple to the `entries_by_date` list.

When we see a line that doesn't start with a date, we append the line to our `entry` list.

If it seems odd that appending to entry seems to modify `entries_by_date`, I recommend spending some time researching how Python's variables and values work. I'd recommend reading Ned Batchelder's [Facts and myths about Python's names and values](https://nedbatchelder.com/text/names.html) or watching [the talk based on it](https://nedbatchelder.com/text/names1.html).

So far we've been looping over our file line by line and building up a new list to return.

If we're lazily looping line-by-line, we might also want to have our function output a lazy iterator. We can do this by creating a generator function.
```
def entries_by_date(diary_file):
    date, entry = None, ""
    for line in diary_file:
        if line.startswith('201'):
            if date:
                yield date, entry.strip()
            date, entry = line.rstrip(), ""
        else:
            entry += line
    yield date, entry.strip()
```
Generator functions create generators, which are iterators (and iterables). So instead of returning a list, we yield the individual items in our list.

Note that we're not yielding at the same place we were appending before. We only want to yield an item once we know it has a completed entry.

So we start by setting a `date` variable to `None` and then when we encounter a new date line, we yield our old `date` and `entry` if the `date` is not `None`.

Whenever we see a line that isn't a date line, we add more to our entry.

We have to yield again at the end of our loop because we won't see a date after our last entry, since it's the last thing in the file.

You may have decided to use something a bit more sophisticated and accurate for identifying the date lines used for our entry headers.

For example we could use a regular expression to look for these date lines:
```
import re

DATE_RE = re.compile(r'^ \d{4} - \d{2} - \d{2} $', re.VERBOSE)

def entries_by_date(diary_file):
    date = None
    for line in diary_file:
        if DATE_RE.search(line):
            if date:
                yield date, entry.strip()
            date, entry = line.rstrip(), ""
        else:
            entry += line
    yield date, entry.strip()
```
Here we're saving a compiled regular expression in the `DATE_RE` variable. The regular expression looks for 4 digits, 2 digits, and 2 digits, with each group separated by dashes. The entire line most consist of those characters.

We've enabled `VERBOSE` mode in this regular expression so that we can space out our pattern a bit. Whitespace can greatly improve code readability and `VERBOSE` mode allows us to use whitespace to improve readability in our regular expressions as well.

Another thing that's different about this code is that we've removed the initial value for the `entry` variable. This should work fine, though my code linter complains that the "entry" variable is used before it's assigned so be cautious because doing this can make your linter unhappy.

We could actually give up this whole loop line-by-line approach and use one big regular expression to identify every date-entry group.

Here's a regular expression that finds date lines followed by two line breaks and then matches as little text as possible such that the next thing after our expression is either another date line or the end of the file.
```
import re

def entries_by_date(diary_file):
    return re.findall(r'''
        ( \d{4} - \d{2} - \d{2} )
        [\n] [\n]
        ( .+? )
        (?= \n \n \d{4} - \d{2} - \d{2} | $ )
    ''', diary_file.read(), re.VERBOSE | re.DOTALL)
```
We're using `VERBOSE` mode here to allow spacing out this regular expression and break it over multiple lines. We're also using `DOTALL` so that our `.+?` expression matches newlines via the `.` character.

If you're unfamiliar with the non-greediness from `.+?` or the lookahead syntax we're using with `(?= ... )`, you might want to make some time to skim through [the regular expression tutorial I gave at PyCon 2017](http://pycon2017.regex.training/).

Note that this solution processes the whole file at once (we're using `diary_file.read()` here) which shouldn't be a problem in our case because it's unlikely our diary file will be enormous.

Personally I like the last generator function approach best, but the regular expression approach isn't bad either. I'm often wary of using regular expressions because they make code tricky to maintain for those who don't know them very well.

Let's take a look at the first bonus now.

### Bonus #1
For the first bonus we were supposed to make sure the entries returned from our `entries_by_date` function had particular HTML entities removed from them. The space, double quote, and ampersand entities (`&nbsp;`, `&quot;`, and `&amp;`) should all be replaced with their respective ASCII characters.

Since our generator function yields entries in two places, writing a function to do this and calling it twice makes quite a bit of sense.

We can write a function that replaces these HTML entities appropriately like this:
```
def clean_entry(text):
    text = text.replace("&nbsp;", " ")
    text = text.replace("&quot;", '"')
    text = text.replace("&amp;", '&')
    return text.strip()
```
Then we can call that function like this:
```
def entries_by_date(diary_file):
    date = None
    for line in diary_file:
        if DATE_RE.search(line):
            if date:
                yield date, clean_entry(entry)
            date, entry = line.rstrip(), ""
        else:
            entry += line
    yield date, clean_entry(entry)
```
Note that trimming whitespace from the ends of our entry is now done in our new `clean_entry` function.

If we were worried about expanding that `clean_entry` function for other types of replacements, we might want to make a list of the replacements and loop over them individually:
```
REPLACEMENTS = [
    ("&nbsp;", " "),
    ("&quot;", '"'),
    ("&amp;", '&'),
]

def clean_entry(text):
    for original, replacement in REPLACEMENTS:
        text = text.replace(original, replacement)
    return text.strip()
```
We're using an uppercased variable name for `REPLACEMENTS` to note that it's essentially a "constant" by convention, meaning it's an object that should be read but not written to by the function or classes that access it.

We could also do this replacement using a regular expression:
```
import re

REPLACEMENTS = {
    "&nbsp;": " ",
    "&quot;": '"',
    "&amp;": '&',
}
REPLACEMENTS_RE = re.compile('|'.join(REPLACEMENTS.keys()))

def clean_entry(text):
    return REPLACEMENTS_RE.sub(lambda m: REPLACEMENTS[m.group()], text).strip()
```
Here we're looking for the pattern `&nbsp;|&quot;|&amp;` (which matches any of our three HTML entities) and we're using `re.sub` to replace each match with correct ASCII character.

The re.sub function can accept a function as its replacement value. If a function is given, it's called with each match object found and the return value of the function is used as the replacement text. This is briefly mentioned toward the end of that regular expression tutorial I mentioned earlier.

Here's another approach:
```
import html
import re

DATE_RE = re.compile(r'^ \d{4} - \d{2} - \d{2} $', re.VERBOSE)

def clean_entry(text):
    return html.unescape(text).replace("\xa0", " ").strip()

def entries_by_date(diary_file):
    date = None
    for line in diary_file:
        if DATE_RE.search(line):
            if date:
                yield date, clean_entry(entry)
            date, entry = line.rstrip(), ""
        else:
            entry += line
    yield date, clean_entry(entry)
```
Here we're using the same `entries_by_date` function as before, but our `clean_entry` function is quite a bit different.

We're using the standard library's `html` module to unescape our HTML entities. We then have to replace all instances of ASCII character `\xa0` with the space character. Character 160 (A0) in ASCII is a non-breaking space, which is what `&nbsp;` actually represents. We've decided to use a regular space instead (ASCII character 32 in decimal, 20 in hex).

Using `html.unescape` will unescape more than just the three HTML entities we're concerned about. This is a slightly different bit of functionality, but it will likely work well for our use cases.

This is my favorite solution, despite the fact that it does a bit more than we asked for.

### Bonus #2
For the second bonus, we had to make a main function that accepted the filename for our diary file and wrote a bunch of separate diary files (in the current working directory), one for each date.

Our main function could look like this:
```
def main(diary_filename):
    with open(diary_filename) as diary_file:
        for date, entry in entries_by_date(diary_file):
            filename = f'{date}.txt'
            with open(filename, mode='wt') as diary_file:
                diary_file.write(entry)
```
Here we're using our `entries_by_date` function to retrieve dates and entries.

We're creating one file for each entry. The filename is `{date}.txt` where `{date}` is the date and the contents of the file are the entry text.

A fully working version of our program might look like this:
```
import html
import re
import sys


DATE_RE = re.compile(r'^ \d{4} - \d{2} - \d{2} $', re.VERBOSE)


def clean_entry(text):
    return html.unescape(text).replace("\xa0", " ").strip()


def entries_by_date(diary_file):
    date = None
    for line in diary_file:
        if DATE_RE.search(line):
            if date:
                yield date, clean_entry(entry)
            date = line.strip()
            entry = ""
        else:
            entry += line
    yield date, clean_entry(entry)


def main(diary_filename):
    with open(diary_filename) as diary_file:
        for date, entry in entries_by_date(diary_file):
            filename = f'{date}.txt'
            with open(filename, mode='wt') as diary_file:
                diary_file.write(entry)


if __name__ == "__main__":
    main(*sys.argv[1:])
```
Note that we're checking whether our module is being executed from the command-line with that `__name__ == "__main__"` check.

Also note that instead of parsing our single command-line argument using argparse, we're just passing all the arguments given to us (`sys.argv[1:]` gives all arguments after the module filename) onto the main function. If there's not exactly one argument, our program will fail but the error message won't be very helpful.

If we're not the only one who will be running this program, we'll probably want to use some slightly more robust argument parsing.

I hope you got some good Python practice this week! 😄
