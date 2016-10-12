# Python for Converts

This page outlines a number of protocols and conventions for programming in Python. Although novice programmer might find it helpful, the document is intended for programmers of other languages who wish to start programming in Python. 

This document is updated on a regular basis. 


**Written by: Pouria Hadjibagheri**

Contents of this document is released under [MIT licence](https://opensource.org/licenses/mit). You are free to copy, use, or otherwise redistribute it as you see fit; without the need for attribution.


## Naming Conventions

### Constants

Always in BLOCK CAPITAL. 

_Bad_
```python
>>> SecondsInMinute = 60
```

_Bad_
```python
>>> secondsInMinute = 60
```

_Better_
```python
>>> seconds_in_minute = 60
```

_Good_
```python
>>> SECONDS_IN_MINUTE = 60
```

### Variables and Functions 

Always in small characters, with words separated using underscores. 

_Bad_
```python
>>> var1 = 0
```

_Better_
```python
>>> var_1 = 0
```

_Good_
```python
>>> current_state = 0
```

_Good_
```python
>>> five_min2sec = SECONDS_IN_MINUTES * 5
```

_Good_
```python
>>> def do_something(var_1, var_2=0):
...     pass
```

Arguments passed to the function:

_Bad_
```python
>>> def do_something(var_1=0, var_2):
...     pass
# Throws SyntaxError. Defualt argument cannot preceed non-default argument.
```

_Bad_
```python
>>> def do_something(*args):
...    pass
```

_Better_
```python
>>> def do_something(var_1, var_2, var_3, var_4, var_5, var_6):
...    pass
```

_Good_
```python
>>> def do_something(size, length, height, width, value, weight):
...    pass
```

If, and only if, you do not know the number of arguments or keyword arguments that the user will be passing, you should use `*args` and `**kwargs`; for instance:

when calculating the sum of the all arguments passed:

_Good_
```python
>>> def sum_up(*args):
...     total_sum = 0
...     args_list = list(args)  # tuple to list
...     while args_list:
...         total_sum += args_list.pop(0)
...     return total_sum

>>> sum_up(1, 2, 3, 4, 5)
15

>>> sum_up(1, 3, 5, 7, 9, 11, 53, 96)
185
```

or when making a call to parent classes:

_Good_
```python
>>> class MySpecialClass(MyOtherClass):
...     def __init__(self, *args, **kwargs)
...         super(MySpecialClass, self).__init__(*args, **kwargs)
```

**Only in Python 3.4+**: Default arguments can be passed between `*args` and `**kwargs`. If not set by the user, it will exist in the context of the function as a named variable; however, if set by the user, it will be passed to `kwargs`.

```python
>>> def do_something(length, *args, weight=1, **kwargs):
...     print(length, args, weight, kwargs)

>>> do_something(3, 1, 2, 3, some_value=9)
3 (1, 2, 3) 1 {'some_value': 9}

>>> do_something(3, 5, 7, weight=5, some_value=9)
3 (5, 7) 5 {'some_value': 9}

>>> do_something(3, 1)
3 (1,) 1 {}

>>> do_something(2)
2 () 1 {}
```

### Class 

Always in CamelCase, starting with capital letters. 

**Note**: There is no naming system in Python that uses camelCase starting with a small letter.

Protected properties and methods in a class start with one underscore; e.g:

_Good_
```python
>>> class MyClass(object):
...     def _protected_member(self):
...         pass
...
>>> my_class = MyClass()
>>> my_class._protected_member()  # Will work but is against convention and IDEs will issue warnings.
```

_Bad_
```python
>>> class myClass(object):
...     pass
```

_Bad_
```python
>>> class My_Class(object):
...     pass
```

_Bad_
```python
>>> class my_class(object):
...     pass
```

_Bad_
```python
>>> class MY_CLASS(object):
...     pass
```


## Protected and Private methods/proterties

Protected and Private properties and methods in a `class` start with two underscores `__` are inaccessible from outside the class, including through inheritance, from parents, or meta classes. Private properties and methods on the other hand, start with `_`, and although accessible from other classes and instances thereof, accessing them is not recommended and is against the convention, with most IDEs issuing warnings to that effect.

```python
>>> class MyClass(object):
...     def _protected_member(self):
...         var = getattr(self, ‘my_property’)  # This will work.
...         return getattr(self, '__private_member')  # Won’t work - raises AttributeError.
...

>>> class MyOtherClass(MyClass):
...     my_property = ’Some value'
...
...     def __private_member(self):
...         self._protected_member()  # This works.
...         local_value = getattr(self, ‘_protected_member’)  # This works too.
```

Python Packaging Index: [The Python Package Index](https://pypi.python.org/)


See previously installed packages are shown using:

```bash
~ $ python3 -m pip freeze
```

List of Python default library: [Python Standard Library](https://docs.python.org/3/library/index.html)

Install packages from the package index:

```bsah
~ $ python3 -m pip install NameOfPackage
```

Install package from the source:

Download the source code. Go the the directory and run:

```bsah
~ $ python3 setup.py install
```


### Tab vs Space

Although not agianst the convention, using tabs for indentation is considered as bad practice in Python. This is because different operating systems have different definitions of tab spacing, and it causes visually indistinguishable errors if mixed with spaces by another programmer working on the same script (even though they both can be used, one must be use within one script). Most IDEs resolve this issue by automatically converting all tabs to a set number of spaces.

```python
	# This line is indented using a tab.
    # This line is indented using 4 spaces. 
```

A single tab is defined by the machine as `\t` whilst 4 spaces are defined as `\s\s\s\s`.


### Boolean testing vs `None`

It is not recommended to test variables as boolean to see whether they are filled or not; e.g:

```python
>>> x = 0
>>> if x: print(x)  # Prints nothing.
...     

>>> if x is not None: print(x)
0 

>>> if not x: print(x)
0  
```

## Files

In Python, the `open` command is very rarely used on its own; mainly for buffering on the network, handling chunck of files separately, or issuing an instance of the file and passing it between different object. It is usually the case that files are handled using a context manager block, for instance:

_Bad (unless necessary)_
```python
>>> data_file = open(‘path_to_file’, ‘r’)
>>> data = data_file.readlines()
>>> data_file.close()
```

_Good_
```python
>>> with open(‘path_to_file’, ‘r’) as data_file:
...     # To read a file as a list.
...     data = data_file.readlines()  

>>> with open(‘path_to_file’, ‘r’) as data_file:
...     # To read a file as string.
...     data = data_file.read() 

>>> with open(‘path_to_file’, ‘r’) as data_file:
...		# To read individual lines from the file (and perform operations on them).
...     data = [line.strip() for line in data_file]  

>>> data = [‘abc’, ‘def’, ‘ghi']
>>> with open(‘path_to_file’, ‘w’) as data_file:
...     # To write a file. 
...     print(data, file=data_file)  
```

### Imports

It is generally not a good idea to import an entire library. This is because it increases the chance of clashes in your namespace, can make your application slower, and can occupy quite a lot of memory:

_Bad_
```python
import math
```

_Bad_
```python
from math import *
```

_Good_
```python
from math import pi, sqrt
```

_Good_
```python
from math import pi as math_pi, sqrt
```

Don't import too many modules or functions in one line.

_Bad_
```python
from math import pi, sqrt, sin, cos, log, tan, ceil, factorial, exp
```

_Good_
```python
from math import (
	pi, sqrt, sin, cos, log, 
	tan, ceil, factorial, exp
)
