# unicode_escape.py
##### Assigned from Mixed Level on 11/18/2019
Hey,

This week I'd like you to program unicode_escape.py that escapes unicode characters in a given file.

Given this file heart_file.py:
```
print("Sparkle heart! 💖")
```
Your program should work like this:
```
$ python unicode_escape.py heart_file.py
print("Sparkle heart! \U0001f496")
```
Your program should start by always using the \U convention for unicode escaping (capital U).

For the first bonus, I'd like program to change to using the `\u (lowercase u)` convention for unicode characters that can be represented using just 4 hexadecimal digits.

If we have a _smiley_file.py_ file that contains this::
```
print("Smiley face! ☺")
```
Our program should behave like this:
```
$ python unicode_escape.py smiley_file.py
print("Smiley face! \u263a")
```
For the second bonus, I'd like you to allow your program to accept `-` as a command-line argument to mean your program should read the file from standard input.

For example this should work:
```
$ cat heart_file.py | python unicode_escape.py -
print("Sparkle heart! \U0001f496")
```
For the third bonus I'd like you to accept a `--style` argument that should allow customization of the output style of your program.

The style argument should default to `default` and `html`:
```
$ python unicode_escape.py --style=html heart_file.py
print("Sparkle heart! &#x1f496;")
$ python unicode_escape.py --style=default heart_file.py
print("Sparkle heart! \U0001f496")
```
Automated tests for this week's exercise [can be found here](https://www.pythonmorsels.com/exercises/d0106eba613d4f1db946e532359cec57/tests/). You'll need to write your function in a module named unicode_escape.py next to the test file. To run the tests you'll run `python test_unicode_escape.py` and check the output for `OK`. You'll see that there are some `expected failures` (or `unexpected successes` maybe). If you'd like to do the bonus, you'll want to comment out the noted lines of code in the tests file to test them properly.
