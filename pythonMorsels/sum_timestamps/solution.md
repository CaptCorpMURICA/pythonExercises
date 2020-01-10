# Solution: sum_timestamps
###### 12/23/2019
Hey!

If you haven't attempted to solve sum_timestamps yet, close this email and go do that now before reading on. If you have attempted solving `sum_timestamps`, read on...

This week you were supposed to make a function that accepts a list of strings that represent timestamps, in the format `MM:SS`, and returns a timestamp that represents the sum of all given timestamps.

Here's one solution:
```
def sum_timestamps(timestamps):
    total_time = 0
    for time in timestamps:
        minutes, seconds = time.split(':')
        total_time += int(minutes) * 60 + int(seconds)
    minutes = total_time // 60
    seconds = total_time % 60
    if seconds < 10:
        return f"{minutes}:0{seconds}"
    else:
        return f"{minutes}:{seconds}"
```
This function loops over the given timestamps, calculates the number of seconds represented by each timestamp, and sums up the seconds in the `total_time` variable.

Then we divide the `total_time` by 60 (rounding downward by using the floor division operator) to calculate the number of minutes. Then we take the remaining seconds after dividing our time by 60 and use it as the number of seconds.

Then we create the timestamp string using minutes and seconds. If we have fewer than 10 seconds, we'll put a 0 in front of our seconds.

We could shorten that timestamp string creation by relying on string formatting parameters to ensure our seconds are always at least 2 digits:
```
def sum_timestamps(timestamps):
    total_time = 0
    for time in timestamps:
        minutes, seconds = time.split(':')
        total_time += int(minutes) * 60 + int(seconds)
    minutes = total_time // 60
    seconds = total_time % 60
    return f"{minutes:01d}:{seconds:02d}"
```
We could also shorten that division and remainder step into one line by using the built-in `divmod` function:
```
def sum_timestamps(timestamps):
    total_time = 0
    for time in timestamps:
        minutes, seconds = time.split(':')
        total_time += int(minutes) * 60 + int(seconds)
    minutes, seconds = divmod(total_time, 60)
    return f"{minutes:01d}:{seconds:02d}"
```
This function isn't very long, but there are multiple components that could be split up and given names.

Here's a rewrite of `sum_timestamps` that relies on two helper functions, `parse_time`, and `format_time`:
```
def parse_time(time_string):
    minutes, seconds = time_string.split(':')
    return int(minutes) * 60 + int(seconds)

def format_time(seconds):
    minutes, seconds = divmod(seconds, 60)
    return f"{minutes:01d}:{seconds:02d}"

def sum_timestamps(timestamps):
    total_time = 0
    for time in timestamps:
        total_time += parse_time(time)
    return format_time(total_time)
```
I find this code a bit more descriptive than the single function we had before.

The `sum_timestamps` function now has a format that would allow us to copy-paste it into a generator expression passed to the built-in `sum` function:
```
def sum_timestamps(timestamps):
    total_time = sum(
        parse_time(time)
        for time in timestamps
    )
    return format_time(total_time)
```
We could even take that a step further:
```
def sum_timestamps(timestamps):
    return format_time(sum(
        parse_time(time)
        for time in timestamps
    ))
```
But I find that a bit less readable than storing `total_time` in a variable. If we are going to nest this function call, I'd prefer to see it written all on one line with a shorter looping variable name:
```
def sum_timestamps(timestamps):
    return format_time(sum(parse_time(t) for t in timestamps))
```
Another way to do this is to use `timedelta` objects instead of integers representing seconds:
```
from datetime import timedelta

def parse_time(time_string):
    minutes, seconds = time_string.split(':')
    return timedelta(minutes=int(minutes), seconds=int(seconds))

def format_time(delta):
    minutes, seconds = divmod(delta.seconds, 60)
    return f"{minutes:01d}:{seconds:02d}"

def sum_timestamps(timestamps):
    deltas = (parse_time(t) for t in timestamps)
    total_time = sum(deltas, timedelta(0))
    return format_time(total_time)
```
The `parse_time` function now creates a `timedelta` object representing the given timestamp.

Python's `timedelta` objects can be added together, but to use them with the `sum` function we need to pass in an initial value of `timedelta(0)` so that we don't end up adding a `timedelta` object to the number `0`.

We're still relying on seconds in `format_time` here because `timedelta` objects don't have separate attributes for seconds and minutes.

Here's another way to write `sum_timestamps` without using a `format_time` function:
```
def sum_timestamps(timestamps):
    deltas = (parse_time(t) for t in timestamps)
    total_time = sum(deltas, timedelta(0))
    time = str(total_time)[2:]
    return time[1:] if time.startswith('0') else time
```
If we convert `timedelta` objects to strings, they'll return a string to us that includes hours, minutes, and seconds (`H:MM:SS`). We're removing the hour and the first colon and then removing the single leading `0` from the beginning if our minutes are less than 10.

The `timedelta` approach is a little more hassle than it's worth I think.

I prefer this more manual approach for understandability:
```
def parse_time(time_string):
    minutes, seconds = time_string.split(':')
    return int(minutes) * 60 + int(seconds)


def format_time(seconds):
    minutes, seconds = divmod(seconds, 60)
    return f"{minutes:01d}:{seconds:02d}"


def sum_timestamps(timestamps):
    total_time = sum(
        parse_time(time)
        for time in timestamps
    )
    return format_time(total_time)
```

## Bonus #1
For the first bonus we needed to format the returned timestamp to include hours when necessary.

We can do that by changing our `format_time` function like this:
```
def format_time(seconds):
    minutes, seconds = divmod(seconds, 60)
    hours, minutes = divmod(minutes, 60)
    if hours:
        return f"{hours}:{minutes:02d}:{seconds:02d}"
    else:
        return f"{minutes:01d}:{seconds:02d}"
```
Here we're using a second `divmod` to get the hours. Then we're returning two different strings depending on whether hours in greater than 0. We're relying on truthiness of hours, which is essentially checking non-zeroness. I rarely like to rely on truthiness for non-zeroness checks (I typically prefer truthiness for non-emptiness checks only) but in this case I think "if hours" reads fairly clearly.

You might try to write this function like this instead:
```
def format_time(seconds):
    minutes, seconds = divmod(seconds, 60)
    hours, minutes = divmod(minutes, 60)
    time = f"{minutes:02d}:{seconds:02d}"
    return f"{hours}:{time}" if hours else time
```
Here we're using an inline if statement to conditionally add hours.

This doesn't pass our tests though because we're always using 2 digits for minutes but we're supposed to only use 2 digits when hours are given.

## Bonus #2
For the second bonus we were supposed to allow hours to be specified in the incoming timestamps we're parsing.

We can do that like this:
```
def parse_time(time_string):
    sections = time_string.split(':')
    try:
        hours, minutes, seconds = sections
    except ValueError:
        hours = 0
        minutes, seconds = sections
    return int(hours)*3600 + int(minutes)*60 + int(seconds)
```
Here we're splitting on colons and then trying to unpack the resulting sections into hours, minutes, and seconds. If that fails, we unpack into minutes and seconds and set hours to 0.

Then we convert each of these to integers and sum them up appropriately.

Here's another way to write this function:
```
def parse_time(time_string):
    sections = [int(s) for s in time_string.split(':')]
    if len(sections) > 2:
        hours, minutes, seconds = sections
    else:
        hours, (minutes, seconds) = 0, sections
    return hours*3600 + minutes*60 + seconds
```
There are two things different here. One is that we're using a list comprehension to convert our values to integers right after we split them. The other is that we're checking the length of our the sections we get back instead of relying on an exception when unpacking them.

Another way we could solve this entirely is to use a regular expression to get the hours, minutes, and seconds:
```
import re

TIME_RE = re.compile(r'^(?:(\d+):)?(\d+):(\d+)$')

def parse_time(time_string):
    hours, minutes, seconds = TIME_RE.search(time_string).groups()
    if not hours:
        hours = 0
    return int(hours)*3600 + int(minutes)*60 + int(seconds)
```
Here we're using a regular expression that captures hours (which are optional), minutes, and seconds. If hours is falsey we set it to `0`. We're doing this because hours will be `None` if no match for it was found.

Here's another way we could write that regular expression:
```
TIME_RE = re.compile(r'''
    ^
    (?:
        ( \d + )
        :
    )?
    ( \d + )
    :
    ( \d + )
    $
''', re.VERBOSE)
```
Here we're using `VERBOSE` mode to space out our regular expression some. I find this more readable because we've split out each of the pieces we're looking for on their own lines.

Here's another possible improvement in our function:
```
def parse_time(time_string):
    hours, minutes, seconds = TIME_RE.search(time_string).groups()
    return int(hours or 0)*3600 + int(minutes)*60 + int(seconds)
```
We're no longer assigning hours to 0 when it's falsey. Now we're using an "`or`" statement to select either hours (if it's truthy) or the number `0` (if hours is falsey).

This might seem a bit cryptic for folks who aren't familiar with short circuit evaluation. I like this trick in some cases, but I'd suggest not relying it if you it might confuse new programmers on your team.

Here's another way to do this:
```
import re

TIME_RE = re.compile(r'''
    ^
    (?:
        (?P<hours> \d + )
        :
    )?
    (?P<minutes> \d + )
    :
    (?P<seconds> \d + )
    $
''', re.VERBOSE)

def parse_time(time_string):
    match = TIME_RE.search(time_string)
    return (
        int(match.group('seconds')) +
        int(match.group('minutes')) * 60 +
        int(match.group('hours') or 0) * 3600
    )
```
This regular expression uses named groups. Our function then references those named groups instead of assigning positional variables.

Personally I think I like this solution best:
```
import re


TIME_RE = re.compile(r'''
    ^
    (?:
        ( \d + )
        :
    )?
    ( \d + )
    :
    ( \d + )
    $
''', re.VERBOSE)


def parse_time(time_string):
    hours, minutes, seconds = TIME_RE.search(time_string).groups()
    return int(hours or 0)*3600 + int(minutes)*60 + int(seconds)


def format_time(seconds):
    minutes, seconds = divmod(seconds, 60)
    hours, minutes = divmod(minutes, 60)
    if hours:
        return f"{hours}:{minutes:02d}:{seconds:02d}"
    else:
        return f"{minutes:01d}:{seconds:02d}"


def sum_timestamps(timestamps):
    return format_time(sum(parse_time(t) for t in timestamps))
```
I hope you learned something about Python this week!
