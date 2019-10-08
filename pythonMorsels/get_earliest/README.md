# get_earliest
#### Assigned from Mixed Level on 09/23/2019
Hey!

This exercise is a somewhat silly one. I'd like you to compare the date strings, but allow invalid dates while comparing them.

Make sure you read to the end of this email because I've linked to some automated tests to help you ensure you've solved this exercise correctly.

I want you to write a function that takes two strings representing dates and returns the string that represents the earliest point in time. The strings are in the US-specific MM/DD/YYYY format... just to make things harder. Note that the month, year, and day will always be represented by 2, 4, and 2 digits respectively.

Your function should work like this:
```
>>> get_earliest("01/27/1832", "01/27/1756")
"01/27/1756"
>>> get_earliest("02/29/1972", "12/21/1946")
"12/21/1946"
>>> get_earliest("02/24/1946", "03/21/1946")
"02/24/1946"
>>> get_earliest("06/21/1958", "06/24/1958")
"06/21/1958"
```
There's a catch though. Your exercise **should work with invalid month and date combinations**. What I mean by that is that dates like 02/40/2006 should be supported. By that I mean 02/40/2006 is before 03/01/2006 but after 02/30/2006 (dates don't roll over at all). I'm adding this requirement so you can't rely on Python's datetime module.

There are many ways to solve this. See if you can figure out the clearest and most idiomatic way to solve this exercise.

### Bonus

If you complete the main exercise, there's also a bonus for you to attempt: allow the function to accept any number of arguments and return the earliest date string of all provided. ✔️

So if you complete the bonus, this should work:
```
>>> get_earliest("02/24/1946", "01/29/1946", "03/29/1945")
"03/29/1945"
```
## Hints

Hints for when you get stuck (hover over links to see what they're about):

1. [Extracting parts of a string](https://youtu.be/ajrtAuDg3yw?t=469)
2. [Slice notation on sequences](https://stackoverflow.com/questions/509211/understanding-slice-notation)
3. [Splitting a string by a specific character](https://stackoverflow.com/questions/37484624/split-string-at-delimiter-in-python)
4. [Avoiding manual list index access](https://treyhunner.com/2018/03/tuple-unpacking-improves-python-code-readability/)
5. [Comparing items (dates), in the form of a tuple](https://treyhunner.com/2019/03/python-deep-comparisons-and-code-readability/#Tuple_comparisons)
6. [Writing a function which accepts unlimited arguments](https://treyhunner.com/2018/10/asterisks-in-python-what-they-are-and-how-to-use-them/#Asterisks_for_packing_arguments_given_to_function)

## Tests

I've written some tests to make it easier to ensure your code functions as expected. You can [download the test file here](https://www.pythonmorsels.com/exercises/01435c5112a04587ae52d8529ba8b2eb/tests/). You'll need to write your function in a file named earliest.py next to where you've saved that test file. To run the tests you'll run "python test_earliest.py" and check the output for "OK". You'll see that there are some "expected failures" (or "unexpected successes" maybe). If you'd like to do the bonus, you'll want to comment out a line to test them properly. You'll see that noted in the test file.

You'll receive some answers and links to resources explaining ways to solve this exercise within a few days. Don't peek at the answers before attempting to solve this on your own.
