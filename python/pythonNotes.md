# Python notes
Focus on python 3.x

# Python environment and package management

## pipenv
Manage python packages

- Reference: https://packaging.python.org/en/latest/key_projects/#pipenv

- Initialize a folder
    ```shell
    # It will generate Pipfile if it doesn't find it in the directory.
    # It will install packages based on the Pipfile
    pipenv install
    ```

- Install new packages
    ```shell
    pipenv install <package name>

    # Install dev dependencies
    pipenv install <package name> --dev
    ```

- To run the script
    1. Use pipenv shell
        ```shell
        pipenv shell
        ```

    2. Run the command in pipenv shell
        ```shell
        pipenv run python <your script>.py
        ```

    3. To quit from pipenv shell, just type "exit"


## poetry
TBD

## pyenv
Manage different python versions

- Documentation:

    Homepage: https://github.com/pyenv/pyenv?tab=readme-ov-file#install-additional-python-versions

    Commands: https://github.com/pyenv/pyenv/blob/master/COMMANDS.md#pyenv-version

- List current version and all versions
    ```shell
    # List current used version
    pyenv version

    # List all versions known to pyenv
    pyenv versions
    ```

- Install a new python version
    ```shell
    pyenv install <version>

    # Example: install latest python 3.10.*
    pyenv install 3.10
    ```

- Switch python version
    ```shell
    # Sets a local application-specific Python version by writing the version name to a .python-version file in the current directory. This version overrides the global version, and can be overridden itself by setting the PYENV_VERSION environment variable or with the pyenv shell command.
    pyenv local <python version>

    # Sets the global version of Python to be used in all shells by writing the version name to the ~/.pyenv/version file. This version can be overridden by an application-specific .python-version file, or by setting the PYENV_VERSION environment variable.
    pyenv global <python version>
    ```

## venv
TBD

# Python type annotion

```python3
x: int = 42
```

The type is merely a hint to improve code readability. It can be used by third-party code-checking tools. Otherwise, it is completely ignored. It does not prevent you from assigning a different kind of value later.

## Python type check tools
### MyPy
Reference: https://mypy.readthedocs.io/en/latest/index.html

Need to import typing to make it recognize the type
```python
from typing import List, Dict
myList: List[int] = [1,2,3]
myDict: Dict[str, int] = {"key1": 1, "key2": 2}
```

### VS code plugin
Mypy, use the "Matan Gover" version (as of 20250518)

# Python basics
- Python use indentations (usually 4 spaces) to represent a code block. Unlike C++, Java, Javascript/Typescript uses {}

- back slash "\\" can be used to join multiple physical lines into one logical lines. Two or more physical lines may be joined into logical lines using backslash characters (\\), as follows: when a physical line ends in a backslash that is not part of a string literal or comment, it is joined with the following forming a single logical line, deleting the backslash and the following end-of-line character. For example:

    ```python
    if 1900 < year < 2100 and 1 <= month <= 12 \
    and 1 <= day <= 31 and 0 <= hour < 24 \
    and 0 <= minute < 60 and 0 <= second < 60:   # Looks like a valid date
            return 1
    ```

- [] or {} can split multiple physical lines without using "\\"
    ```python
    month_names = ['Januari', 'Februari', 'Maart',      # These are the
                'April',   'Mei',      'Juni',       # Dutch names
                'Juli',    'Augustus', 'September',  # for the months
                'Oktober', 'November', 'December']   # of the year
    ```

- For an empty clause, use the "pass" statement.
    ```python
    if a < b:
        pass # Do nothing
    else:
        print('Computer says No')
    ```

- Python use '#' as the start mark of comments, not "//" or "/* */"

- Python reserved keywords:

    Reference: https://docs.python.org/3/reference/lexical_analysis.html#keywords

    ```python
    False      await      else       import     pass
    None       break      except     in         raise
    True       class      finally    is         return
    and        continue   for        lambda     try
    as         def        from       nonlocal   while
    assert     del        global     not        with
    async      elif       if         or         yield
    ```

- [Other reserved identifiers](https://docs.python.org/3/reference/lexical_analysis.html#reserved-classes-of-identifiers):

    [\__*__](https://docs.python.org/3/reference/datamodel.html#specialnames): System-defined names, informally known as “dunder” names. These names are defined by the interpreter and its implementation (including the standard library). Current system names are discussed in the Special method names section and elsewhere.

    Example: object.\__init__

- Python primitives: reference: https://docs.python.org/3/library/stdtypes.html

- Arithmetic Operators. One specail "//", Truncating division. In C++, 7 / 4 = 1 since both are int (integers). 7.0 / 4 = 1.75 since one arg is a double and both convert to double and then do the calculation.

    The division operator (/) produces a floating-point number when applied to integers. Therefore, 7/4 is 1.75. The truncating division operator //, also known as floor division, truncates the result to an integer and works with both integers and floating-point numbers.
    ```python
    7 / 4 # = 1.75
    7 // 4 # = 1
    ```

- Assignment expressions: ":="
    
    This is of the form NAME := expr where expr is any valid Python expression other than an unparenthesized tuple, and NAME is an identifier.

    The value of "NAME := expr" is expr and the value of expr is assigned to NAME. A () is required to surround the entire expression
    ```python
    x=0
    while (x := x + 1) < 10: # Prints 1, 2, 3, ..., 9
        print(x)
    ```

    Ref: https://docs.python.org/3/whatsnew/3.8.html#assignment-expressions

- "with" statement:
    
    The with statement that precedes it declares a block of statements (or context) where the file (file) is going to be used.

    ```python
    with open('data.txt') as file:
        for line in file:
            print(line, end='')  # end='' omits the extra newline
    ```

# Python class
## class member access identifier
Python doesn't have public, protected and private keyword. All members are public by default.
There are some conventions:
like __member will have name mangling that internally change its name (see below) and is mainly used to avoid child class overwritten parent class's members, _member (1 underscore) usually indicates that it is a non-public member

Reference: https://docs.python.org/3/tutorial/classes.html#private-variables

- name mangling
    ```python
    class MyClass:
        def __init__(self, member):
            self.__member = member
    
    obj = MyClass()
    obj.__member # Error, no member named __member
    obj._MyClass__member # Name mangled. So __member is a private member and should not be called outside the class. But you can still access it.

    ```
    Reference: https://docs.python.org/3/reference/expressions.html#private-name-mangling

# Python built in types
## string -> string
- f-string, formatted string
    ```python3
    # Using named parameters
    url = "https://api.com/{zip_code}".format(zip_code="90210") # 'https://api.com/90210'

    # Using positional parameters
    url = "https://api.com/{}".format("90210") # 'https://api.com/90210'

    # Using index
    url = "https://api.com/{0}".format("90210") # 'https://api.com/90210'

    # f-string
    zip_code = "90210"
    url = f"https://api.com/{zip_code}" # 'https://api.com/90210'
    ```

Example:
```python
year: int = 1987
principal: double = 12.3489
print(f'{year:>3d} {principal:0.2f}')
# output:
# 1987 12.35
```

- String can be defined using single or double quotes
The same type of quote used to start a string must be used to terminate it. Triple- quoted strings capture all the text until the terminating triple quote—as opposed to single- and double-quoted strings which must be specified on one logical line.
    ```python
    a = 'Hello World'
    b = "Python is groovy"
    c = '''Computer says no.'''
    d = """Computer still says no."""
    ```

- Immediately adjacent string literals are concatenated into a single string. Thus, the above example could also be written as:
    ```python
    print(
    '''Content-type: text/html
    
    <h1> Hello World </h1>
    Clock <a href="http://www.python.org">here</a>'''
    )

    print(
    'Content-type: text/html\n'
    '\n'
    '<h1> Hello World </h1>\n'
    'Clock <a href="http://www.python.org">here</a>'
    )
    ```

- string also has format() function to do similar thing as f-string
    ```python
    print('{0:>3d} {1:0.2f}'.format(year, principal))
    print('%3d %0.2f' % (year, principal))
    ```

- lengh of string: len(str)

- get a substring. Negative index means index from end of string, -1 is last character of the string

    ```python
    a = 'Hello World'
    c = a[:5]
    d = a[6:]
    e = a[3:8]
    f = a[-5:]
    # c = 'Hello'
    # d = 'World'
    # e = 'lo Wo'
    # f = 'World'
    ```

- str1 + str2 means string concatenation

- str() vs repr()

    ```python
    >>> s = 'hello\nworld'
    >>> print(str(s))
    hello
    world
    >>> print(repr(s))
    'hello\nworld'
    >>>
    ```

- python strings are immutable. Cannot change single character in a string

## Array -> List
Python List is an array list.
Reference: https://docs.python.org/3/tutorial/datastructures.html

- List comprehension
    ```python3
    fruits = ["apple", "banana", "cherry", "kiwi", "mango"]
    newlist = [x for x in fruits if "a" in x]
    
    print(newlist)
    # prints ['apple', 'banana', 'mango']
    ```

## Linked List -> No built-in type
Python does not have built in linked list.

the "collections" module includes a deque object, which is implemented as a doubly linked list internally. While deque offers functionalities similar to a linked list, it is primarily designed for double-ended queue operations.

## Queue and stack -> No built-in type, use collections.deque

## HashMap -> dict

## HashSet -> set

## Sorted Map -> No built-in type, use collections.OrderedDict
Starting from Python 3.7, dict already track the insertion order.

## Decorators
Reference: https://book.pythontips.com/en/latest/decorators.html

A Python decorator is a design pattern that allows for the modification or extension of the behavior of functions or classes without altering their original code. It essentially wraps a function or class within another function or class, adding functionality before, after, or around the execution of the original.

```python
def a_new_decorator(a_func):

    def wrapTheFunction():
        print("I am doing some boring work before executing a_func()")

        a_func()

        print("I am doing some boring work after executing a_func()")

    return wrapTheFunction

def a_function_requiring_decoration():
    print("I am the function which needs some decoration to remove my foul smell")

a_function_requiring_decoration()
#outputs: "I am the function which needs some decoration to remove my foul smell"

a_function_requiring_decoration = a_new_decorator(a_function_requiring_decoration)
#now a_function_requiring_decoration is wrapped by wrapTheFunction()

a_function_requiring_decoration()
#outputs:I am doing some boring work before executing a_func()
#        I am the function which needs some decoration to remove my foul smell
#        I am doing some boring work after executing a_func()

@a_new_decorator
def a_function_requiring_decoration():
    """Hey you! Decorate me!"""
    print("I am the function which needs some decoration to "
          "remove my foul smell")

a_function_requiring_decoration()
#outputs: I am doing some boring work before executing a_func()
#         I am the function which needs some decoration to remove my foul smell
#         I am doing some boring work after executing a_func()

#the @a_new_decorator is just a short way of saying:
a_function_requiring_decoration = a_new_decorator(a_function_requiring_decoration)
```

# Python basic syntax
## for loop
Just use range for python 3.*

python 2.* has xrange and range

python 3.* xrange is renamed to range.

```python
range(0,10) # will generate a list: [0,1,2...9]
xrange(0,10) # will generate a number on demand, so more memory efficient

for i in range(0,10):
    print(i)
# output: 0,1,2 ... 9
```
range(0,10)

## Built-in variables
- \__name__: a built-in variable that always contains the name of the enclosing module. If a program is run as the main script with a command such as python readport.py,
the __name__ variable is set to '__main__'. Otherwise, if the code is imported using a statement such as import readport, the __name__ variable is set to 'readport'

## Python class and interfaces
Python does not have 'interface' keyword. To define interfaces, use abstract class with pass keyword for its member functions.
Python class can have member functions that are not implemented (use pass keyword)

# Interactive mode
- The underscore symbol "_"
When you use Python interactively, the variable _ holds the result of the last operation. This is useful if you want to use that result in subsequent statements. This variable only gets defined when working interactively, so don't use it in saved programs.

Example:
```python
>>> 6000 + 4523.50 + 134.25
10657.75
>>> _ + 8192.75
18850.5
>>>
```
