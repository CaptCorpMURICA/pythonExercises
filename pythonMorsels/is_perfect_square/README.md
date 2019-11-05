# is_perfect_square
#### Assigned from Mixed Level on 11/04/2019

Hello! 👋

First, I'd like to congratulate you on starting your twelfth week of Python Morsels. 🎉 You've been solving one Python exercise every week for almost three months now! 😄 Seriously, give yourself a hug or a pat on the back or do whatever it is you do to congratulate yourself on forming productive life habits. 💗

This week I want you to write a function that might seem simple at first, but there's a number of ways to solve it.

This week's function, is_perfect_square, accepts a number and returns True if it's a perfect square and False if it's not. A perfect square is any number which has an integer square root. So 25 is a perfect square but 24 is not, 9 is a perfect square but 8 is not, 100 is a perfect square but 1000 is not.

Here's an example:
```
>>> is_perfect_square(64)
True
>>> is_perfect_square(65)
False
>>> is_perfect_square(100)
True
>>> is_perfect_square(1000)
False
```
I also want you to make sure that this function raises a TypeError when an invalid type is given (integers and floats are fine but strings aren't) and I want you to make sure your function works with sensible alternative numeric types like decimal.Decimal.

There are a few bonuses this week. The first one should be easy, but the other two will be a little tricky.

The first bonus is to make sure your function returns False for negative numbers. ✔️
```
>>> is_perfect_square(-1)
False
>>> is_perfect_square(-4)
False
```
The second bonus is to make sure your function works for really big numbers. ✔️
```
>>> is_perfect_square(4624000000000000)
True
>>> is_perfect_square(4623999999999999)
False
```
The third bonus is to make your function accept an optional "complex" keyword-only argument which will allow your function to check whether the given number is a perfect square in the complex number system. ✔️
```
>>> is_perfect_square(-4, complex=True)
True
>>> is_perfect_square(-5, complex=False)
False
>>> is_perfect_square(512j, complex=True)
True
```
To be a complex perfect square, the complex square root of the number must have integers for both its real part and its imaginary part.

Automated tests for this week's exercise [can be found here](https://www.pythonmorsels.com/exercises/ad57c49f8a0042729e347cc34e8ac0e6/tests/). You'll need to write your function in a module named perfect_square.py next to the test file. To run the tests you'll run "python test_perfect_square.py" and check the output for "OK". You'll see that there are some "expected failures" (or "unexpected successes" maybe). If you'd like to do the bonus, you'll want to comment out the noted lines of code in the tests file to test them properly.
