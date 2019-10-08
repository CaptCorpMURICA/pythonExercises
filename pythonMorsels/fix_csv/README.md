# fix_csv.py
## Assigned from Mixed Level on 06/17/2019
Hello!

This week we're going to normalize CSV files by writing a program, fix_csv.py, that turns a pipe-delimited file into a comma-delimited file. I'll explain how it should work by example.

Your original file will look like this:
```
Reading|Make|Model|Type|Value
Reading 0|Toyota|Previa|distance|19.83942
Reading 1|Dodge|Intrepid|distance|31.28257
```
You'll then run your script by typing this at the command line:

`$ python fix_csv.py cars-original.csv cars.csv`
**Note:** "$" is not typed; that is simply the beginning of the prompt.

Your fixed file should then look like this:
```
Reading,Make,Model,Type,Value
Reading 0,Toyota,Previa,distance,19.83942
Reading 1,Dodge,Intrepid,distance,31.28257
```
### Bonus 1

For the first bonus, I want you to allow the input delimiter and quote character to be optionally specified. ✔️

For example any of these should work (all specify input delimiter as pipe and the last two additionally specifies the quote character as single quote):
```
$ python fix_csv.py --in-delimiter="|" cars.csv cars-fixed.csv
$ python fix_csv.py cars.csv cars-fixed.csv --in-delimiter="|"
$ python fix_csv.py --in-delimiter="|" --in-quote="'" cars.csv cars-fixed.csv
$ python fix_csv.py --in-quote="'" --in-delimiter="|" cars.csv cars-fixed.csv
```
This bonus will require looking into parsing command-line arguments with Python. There are some standard library modules that can help you out with this. There are 3 different solutions in the standard library actually, but only one I'd recommend.

### Bonus 2

For the second bonus, automatically detect the delimiter if an in-delimiter value isn't supplied (don't assume it's pipe and quote, figure it out). ✔️

This second bonus is a bit trickier and I don't expect it to work correctly for all files. You'll likely want to consult the csv module documentation if you're going to attempt it.

## Hints
1. [How to access command-line arguments](https://stackoverflow.com/a/35421024/2633215)
2. [Reading and writing CSV files in Python](https://pymotw.com/3/csv/index.html)
3. [Short video on working with CSV files in Python](https://www.youtube.com/watch?v=q5uM4VKywbA)
4. [Restricting the number of command-line arguments](https://treyhunner.com/2018/03/tuple-unpacking-improves-python-code-readability/#Multiple_assignment_is_very_strict)
5. [Parsing command-line arguments more robustly](http://zetcode.com/python/argparse/)
6. [Automatically detecting the type of a CSV file](https://docs.python.org/3/library/csv.html#csv.Sniffer%3CPaste%3E)

## Tests

Automated tests for this week's exercise [can be found here](https://www.pythonmorsels.com/exercises/ea6023829c564d21b7d26e7272653a4f/tests/). You'll need to write your function in a module named fix_csv.py next to the test file. To run the tests you'll run "python test_fix_csv.py" and check the output for "OK". You'll see that there are some "expected failures" (or "unexpected successes" maybe). If you'd like to do the bonus, you'll want to comment out the noted lines of code in the tests file to test them properly.
