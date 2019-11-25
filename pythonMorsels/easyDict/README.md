# EasyDict
##### Assigned from Mixed Level on 11/25/2019
Hello!

This week I'd like you to create a class called `EasyDict` which creates objects have keys and attributes that can be used interchangeably:
```
>>> person = EasyDict({'name': "Trey Hunner", 'location': "San Diego"})
>>> person.name
'Trey Hunner'
>>> person['location']
'San Diego'
```
At first your object should only worry about accessing keys and attributes and accepting a single dictionary as its one optional argument.

For the first bonus, I'd like you to make sure key and attribute assignment to works ✔️:

### Bonus 1
```
>>> person = EasyDict({'name': "Trey Hunner", 'location': "San Diego"})
>>> person.location = "Portland"
>>> person['location']
'Portland'
>>> person['location'] = "San Diego"
>>> person.location
'San Diego'
```
### Bonus 2

For the second bonus, I'd like you to also allow your `EasyDict` class to accept keyword arguments, I'd like you to make `EasyDict` objects comparable to each other via equality, and I'd like you to implement a `get` method that works sort of like the `get` method on dictionaries ✔️:
```
>>> person = EasyDict(name="Trey Hunner", location="San Diego")
>>> person.location
'San Diego'
>>> person == EasyDict(name="Trey", location="San Diego")
False
>>> person == EasyDict(name="Trey Hunner", location="San Diego")
True
>>> person.get('profession')
>>> person.get('profession', 'unknown')
'unknown'
>>> person.get('name', 'unknown')
'Trey Hunner'
```
### Bonus 3

For the third bonus, I'd like you to allow your `EasyDict` class to accept a `normalize` keyword argument which, if true, will "normalize" the spaces in keys to underscores in attributes ✔️:
```
>>> person = EasyDict(name="Trey Hunner", location="San Diego", normalize=True)
>>> person['company name'] = "Truthful Technology LLC"
>>> person.company_name
'Truthful Technology LLC'
>>> person['company name']
'Truthful Technology LLC'
```
## Hints

Hints for **when you get stuck** (hover over links to see what they're about):

1. [One way to create a dictionary-like class](https://stackoverflow.com/a/2466232/2633215)
2. [How `[...]` lookups work](https://stackoverflow.com/a/1957793/2633215)
3. [Customizing what attribute lookups (obj.thing) do](https://stackoverflow.com/questions/3278077/difference-between-getattr-vs-getattribute)
4. [Bonus 1: making x[key] = value work](https://gist.github.com/turicas/1510860)
5. [Bonus 2: keyword-only arguments](https://treyhunner.com/2018/04/keyword-arguments-in-python/#Keyword-only_arguments_without_positional_arguments)
6. [Bonus 2: overriding equality between objects](https://stackoverflow.com/questions/390250/elegant-ways-to-support-equivalence-equality-in-python-classes)
7. [Bonus 2: the get method](https://docs.python.org/3/library/stdtypes.html#dict.get)
8. [Bonus 3: A helper for making dictionary-like classes](https://treyhunner.com/2019/04/why-you-shouldnt-inherit-from-list-and-dict-in-python/#UserList/UserDict:_lists_and_dictionaries_that_are_actually_extensible)

## Tests

Automated tests for this week's exercise [can be found here](https://www.pythonmorsels.com/exercises/687c35b248d14dd1acab9c6f55cdec20/tests/). You'll need to write your class in a module named easydict.py next to the test file. To run the tests you'll run `python test_easydict.py` and check the output for `OK`. You'll see that there are some `expected failures` (or `unexpected successes` maybe). If you'd like to do the bonus, you'll want to comment out the noted lines of code in the tests file to test them properly.
