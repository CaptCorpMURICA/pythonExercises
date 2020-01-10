# sum_timestamps
#### Assigned from Mixed Level on 12/23/2019

Hi! ✨

For this week's exercise I want you to write a function that accepts a list of timestamps (in the format `MM:SS`) and returns a timestamp that is a sum of all the given times.

For example:
```
>>> sum_timestamps(['5:32', '4:48'])
'10:20'
>>> sum_timestamps(['03:10', '01:00'])
'4:10'
>>> sum_timestamps(['2:10', '1:59'])
'4:09'
```
Note that the minutes are single digit unless they need to be double digit.

There are two bonuses for this exercise. I recommend solving the exercise without the bonuses first and then attempting each bonus separately.

### Bonus 1

For the first bonus, I'd like you to format the resulting sum differently if there are more than 59 minutes. If the sum of the timestamps results in an hour or more, there should be a third component of the returned timestamp. ✔️
```
>>> sum_timestamps(['15:32', '45:48'])
'1:01:20'
```
When formatting time, the minutes should show as 2 digit if there's an hour to display. The hour should show as 2 digit only as needed.

### Bonus 2

For the second bonus, you should allow your function to accept timestamps that include an hour. ✔️
```
>>> sum_timestamps(['6:15:32', '2:45:48'])
'9:01:20'
>>> sum_timestamps(['6:35:32', '2:45:48', '40:10'])
'10:01:30'
```
## Hints

1. [Storing minutes and second in separate variables](https://www.hacksparrow.com/python/split-string-method-and-examples.html)
2. [Converting string to integer](https://treyhunner.com/2019/05/python-builtins-worth-learning/#int)
3. [Always showing 2 digit numbers](https://stackoverflow.com/questions/134934/display-number-with-leading-zeros)
4. [Dividing time into minutes and seconds](https://stackoverflow.com/a/27751293/2633215)
5. [Automatic summing of timestamps](https://treyhunner.com/2019/05/python-builtins-worth-learning/#sum)
6. [Matching time intervals using regex](https://stackoverflow.com/a/24347898/2633215)

## Tests

Automated tests for this week's exercise [can be found here](https://www.pythonmorsels.com/exercises/c761e15e7ba84d63a18c79929e2dfccf/tests/). You'll need to write your function in a module named sum_timestamps.py next to the test file. To run the tests you'll run "python test_sum_timestamps.py" and check the output for "OK". You'll see that there are some "expected failures" (or "unexpected successes" maybe). If you'd like to do the bonus, you'll want to comment out the noted lines of code in the tests file to test them properly.
