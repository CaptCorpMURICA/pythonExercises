# Solution: unicode_escape.py
###### 11/18/2019
Hey!

If you haven't attempted to solve unicode_escape.py yet, close this email and go do that now before reading on. If you have attempted solving `unicode_escape.py`, read on...

The `unicode_escape.py` script is supposed to escape unicode characters in a given file and print out the resulted escaped text.

Let's take a look at a working program:
```
import sys

filename = sys.argv[1]
with open(filename, mode='rt', encoding='utf-8') as text_file:
    escaped = ""
    for char in text_file.read():
        code = ord(char)
        if code > 127:
            escaped += rf"\U{hex(code)[2:]:0>8}"
        else:
            escaped += char
    print(escaped, end='')
```
We're opening a file using a context manager (the `with` block) to make sure our file is always closed after we're done with it.

Note that we're explicitly opening the file with read text mode (`rt`) even though that's the default mode. It never hurts to be explicit! We're also explicitly specifying the encoding as utf-8.

We loop over every character of the file and use the built-in ord function to get the unicode code point for each character, then we append to our escaped string either an escaped (with `\U`) version of the character, or the original character.

We're using an `rf` prefix in front of our string to make it both a raw string (so `\U` doesn't get escaped) and a format string (so the `{` and `}` can be used for string interpolation).

Note that format strings (f-strings) were added in Python 3.6. The equivalent code on an earlier version of Python looks like this:
```
escaped += r"\U{code:0>8}".format(code=hex(code)[2:])
```
After we've processed every character, we print out the new string.

We're checking whether the code point is above 127 because ASCII characters are 127 and below.

There's kind of a lot going on in this code. Let's split our program up into a couple functions.

First we'll write a function that handles the body of that for character-escaping loop:
```
import sys

def escape(char):
    code = ord(char)
    if code > 127:
        return rf"\U{hex(code)[2:]:0>8}"
    else:
        return char

filename = sys.argv[1]
with open(filename, mode='rt') as text_file:
    contents = text_file.read()
    escaped = ""
    for char in contents:
        escaped += escape(char)
    print(escaped, end='')
```
In this code, I find it more immediately clear at a glance that our `for` loop is only processing one character at a time.

We can break this up even more though. Let's write a function that accepts our file and returns an escaped string:
```
import sys

def escape(char):
    code = ord(char)
    if code > 127:
        return rf"\U{hex(code)[2:]:0>8}"
    else:
        return char

def escape_file(text_file):
    contents = text_file.read()
    escaped = ""
    for char in contents:
        escaped += escape(char)
    return escaped

filename = sys.argv[1]
with open(filename, mode='rt') as text_file:
    print(escape_file(text_file), end='')
```
We've now split our code up into three parts here. We have a function that returns an escaped version of our file, a function that escapes a single character in our file, and the code that actually accepts a filename, opens a file, calls our `escape_file` function, and prints the resulting escaped file.

To avoid import side effects in this module when importing to a Python shell, we could add a check to see whether we're running at the command-line:
```
import sys

def escape(char):
    code = ord(char)
    if code > 127:
        return rf"\U{hex(code)[2:]:0>8}"
    else:
        return char

def escape_file(text_file):
    contents = text_file.read()
    escaped = ""
    for char in contents:
        escaped += escape(char)
    return escaped

if __name__ == "__main__":
    [filename] = sys.argv[1:]
    with open(filename, mode='rt') as text_file:
        print(escape_file(text_file), end='')
```
This `__name__ == "__main__"` check will only return True if our program is being run from the command-line. If instead, we've imported our unicode_escape module from another Python module, the `__name__` variable will be "unicode_escape".

We're also do something else different here: we're slicing `sys.argv` and using multiple assignment to ensure only a filename is specified. If you haven't seen this form of multiple assignment before, check out [my article on multiple assignment and tuple unpacking](http://treyhunner.com/2018/03/tuple-unpacking-improves-python-code-readability/).

Another possibly improvement we could make is to rewrite our escape function to use an "inline if" statement:
```
def escape(character):
    code = ord(character)
    return (
        rf"\U{hex(code)[2:]:0>8}"
        if code > 127
        else character
    )
```
This is equivalent to the [ternary operator](https://en.wikipedia.org/wiki/%3F:) in other programming languages.

You may not consider this an improvement, but I often like to rewrite code this way to make it clear that we're not just checking a condition but specifically assigning one of two values to a variable based on a condition.

Another refactor we could do is modify the `escape_file` function to append to a list instead of concatenate to a string:
```
def escape_file(text_file):
    contents = text_file.read()
    characters = []
    for c in contents:
        characters.append(escape(c))
    return ''.join(characters)
```
This might seem like an odd change. Before we were using `+=` to concatenate to a string but now we're using append to append to a list and then eventually we're using the string join method to join together the strings in the list.

We're doing this so that we can [copy-paste our way into a list comprehension](http://treyhunner.com/2015/12/python-list-comprehensions-now-in-color/):
```
def escape_file(text_file):
    contents = text_file.read()
    characters = [
        escape(c)
        for c in contents
    ]
    return ''.join(characters)
```
Whenever we can rewrite our code to look like an empty list being built-in by appending values taken (and often modified slightly) from an old list, we can use a list comprehension instead of a `for` loop.

We could move this list comprehension right into our join method call, removing the need for that `characters` variable:
```
def escape_file(text_file):
    contents = text_file.read()
    return ''.join([
        escape(c)
        for c in contents
    ])
```
And because we're only looping over this list once, we could drop those square brackets and turn that comprehension into a generator expression:
```
def escape_file(text_file):
    contents = text_file.read()
    return ''.join(
        escape(c)
        for c in contents
    )
```
Generator expressions need to be wrapped in parenthesis, but if we have two sets of parenthesis around them we can remove one, even if one of them is for a function call.

If you're still shaky on list comprehensions and generator expressions, my [Comprehensible Comprehensions](https://www.youtube.com/watch?v=5_cJIcgM7rw) talk explains them both in detail.

## Bonus #1
For the first bonus we'd like to make sure unicode characters that can be represented with 4 hexadecimal digits or fewer use the small u (`\u`) notation instead of the big U (`\U`) notation.

Python's unicode escape encoding does something similar. We could encode using the `unicode_escape` encoding and then decode using utf-8:
```
def escape(character):
    return (
        character.encode('unicode_escape').decode('utf-8')
        if ord(character) > 127
        else character
    )
```
This doesn't pass our tests though.

The problem is that unicode characters that are fewer than 2 hexadecimal digits end up being represented using `\x` instead of `\u`.

For example Ñ should be `\u00d1` but it comes out as `\xd1` (valid, but not what we're looking for).

We could instead do this:
```
def escape(character):
    code = ord(character)
    if code <= 127:
        return character
    elif code <= 0xffff:
        return rf"\u{hex(code)[2:]:0>4}"
    else:
        return rf"\U{hex(code)[2:]:0>8}"
```
We're checking keeping our ASCII characters (127 and below) as-is, just as before. We've added a condition for characters greater than 127 but representable by 4 hexadecimal digits.

Writing 0xffff is equivalent to writing 65535, but it's more clear that we're representing 4 hexadecimal digits with 0xffff.

We could choose to rewrite this if-elif-else statement this way:
```
def escape(character):
    code = ord(character)
    if code <= 0b0111_1111:
        return character
    elif code <= 0xffff:
        return rf"\u{hex(code)[2:]:0>4}"
    else:
        return rf"\U{hex(code)[2:]:0>8}"
```
Here we've rewritten 127 using a binary notation to make it clear that we're looking for largest 8-bit number that starts with a 0 (which is the ASCII range).

Note also that we've put an underscore in the binary representation of this number to separate the groups of 4 bytes (each representing one hexadecimal number).

We could have written 0x7f instead of 0b01111111, but that may be a bit less clear. We also could have written 0b1111_1111_1111_1111 instead of 0xffff but that doesn't seem more clear either.

## Bonus #2
For the second bonus we had to accept read from standard input when the given filename was "-".

We can do this by modifying our program entry point like this:
```
if __name__ == "__main__":
    [filename] = sys.argv[1:]
    if filename == '-':
        print(escape_file(sys.stdin), end='')
    else:
        with open(filename, mode='rt') as text_file:
            print(escape_file(text_file), end='')
```
Here we're checking whether filename is `-` and passing `sys.stdin` to `escape_file` if it is. Otherwise we open up the given file and pass it to `esacpe_file` as before.

This `-` thing might seem strange but this is actually a pretty strong convention in the Unix world. In fact, Python's argparse module understands this convention by default.

So we could refactor our code to use argparse for handling that file argument:
```
from argparse import ArgumentParser, FileType

def escape(character):
    code = ord(character)
    if code <= 0b0111_1111:
        return character
    elif code <= 0xff_ff:
        return rf"\u{hex(code)[2:]:0>4}"
    else:
        return rf"\U{hex(code)[2:]:0>8}"

def escape_file(text_file):
    contents = text_file.read()
    return ''.join(
        escape(c)
        for c in contents
    )

if __name__ == "__main__":
    parser = ArgumentParser()
    parser.add_argument('file', type=FileType('rt'))
    args = parser.parse_args()
    print(escape_file(args.file), end='')
```
Here we're using the `FileType('rt')` argument to open the given file in read text mode. Note that we're not doing any check for `-`. That's automatically done by `argparse.FileType`.

Note that we're not closing our file here as we were before with our context manager. We could close our file in a try-finally block, but leaving our file open for the entirety of our program likely won't be problematic for a small script like this one.

As you run the tests you may see unclosed file warnings. You can ignore them (as long as your tests pass).

While we're changing things, let's refactor our code to bundle up our entry logic into a main function and call that main function in our main `if` statement:
```
def main():
    parser = ArgumentParser()
    parser.add_argument('file', type=FileType('rt'))
    args = parser.parse_args()
    print(escape_file(args.file), end='')

if __name__ == "__main__":
    main()
```
## Bonus #3
For the third bonus we had to accept an optional style argument. This was a bigger change than our other bonuses.

We could add the argument like this:
```
parser.add_argument(
    '--style',
    choices=['default', 'html'],
    default='default',
)
```
To use the argument, we need our functions to accept a style attribute.

Here's one way we could do this:
```
from argparse import ArgumentParser, FileType

def escape(character, style):
    code = ord(character)
    if code <= 127:
        return character
    elif style == "html":
        return f"&#{hex(code)[1:]};"
    elif code > 0xffff:
        return rf"\U{hex(code)[2:]:0>8}"
    else:
        return rf"\u{hex(code)[2:]:0>4}"

def escape_file(text_file, style):
    contents = text_file.read()
    return ''.join(
        escape(c, style)
        for c in contents
    )

def main():
    parser = ArgumentParser()
    parser.add_argument('file', type=FileType('rt'))
    parser.add_argument(
        '--style',
        choices=['default', 'html'],
        default='default',
    )
    args = parser.parse_args()
    print(escape_file(args.file, style=args.style), end='')

if __name__ == "__main__":
    main()
```
Here we're passing style into `escape_file` and then into escape.

We're handling the HTML escaping by using a non-zero padded hexadecimal string.
