---
layout: post
title:  "Data Types in Python"
number: 75
date:   2018-09-29 0:00
categories: data
---
In Python, everything is an object. Data types are really classes and the variables we define are instances of those classes. Data types in Python differ based on number of values they can contain, their mutability, and their order.

There are two functions available to check the type of a variable. The `type()` function will return the data type of the variable that is passed to it. The `isinstance()` function can check if a variable's data type matches the one we provide. I'll use these functions below to illustrate the various data types.

## Numbers
There are 3 kinds of numbers in Python: **integers** (`int`), **floating-points** (`float`), and **complex numbers** (`complex`).

Integers are simple whole numbers, such as `0` or `1`. Their maximum value is limited only by the amount of memory available. For example:

```python
>>> my_var = 1
>>> type(my_var)
<class 'int'>
>>> isinstance(my_var, int)
True
```

Floating-point numbers are those which contain an integer part and a fractional part and contain a decimal symbol. They are accurate upto 15 decimal places and are truncated after that. For example:

```python
>>> my_var = 1.0
>>> type(my_var)
<class 'float'>
>>> isinstance(my_var, float)
True

>>> my_var = 1.01234567891011121314151617181920
>>> print(my_var)
1.0123456789101113
```

Complex numbers are those which contain a real part and an imaginary part. They are usually denoted in the format `x+yj`.

```python
>>> my_var = 1 + 2j
>>> type(my_var)
<class 'complex'>
>>> isinstance(my_var, complex)
True
```

## Strings 
A **string** in Python is an immutable and ordered sequence of items. They can be defined using single-quotes (`'`) or double-quotes (`"`). A string spanning multiple lines can be defined using triple single-quotes (`'''`) or triple double-quotes (`"""`). For example:

```python
>>> my_var = 'This is a string'
>>> type(my_var)
<class 'str'>
>>> isinstance(my_var, str)
True

>>> my_string = """This is
... my
... first
... string"""
>>> print(my_string)
This is
my
first
string
>>> my_string_2 = '''
... This
... is
... my
... second
... string
... '''
>>> print(my_string_2)

This
is
my
second
string
```

Since strings in Python are ordered, we can extract individual characters from a string using their integer indices, starting with `0`. The first letter of a string is always at position `0`, and the positions numerically increase after that. For example:

```python
>>> my_var = 'This is a string'
>>> my_var[0]
'T'
>>> my_var[10]
's'
```

Strings in Python also support *slicing*. Slicing is a technique which is used to extract a part of a variable using the notation `[start_position:end_position]`, where `start_position` and `end_position` are integers indicating the length of the slice. If `start_position` is omitted, then slicing begins at the beginning of the string, and if `end_position` is omitted, then slicing ends at the end of the string. For example:

```python
>>> my_var[0:1]
'T'
>>> my_var[0:]
'This is a string'
>>> my_var[:-1]
'This is a strin'
>>> my_var[:len(my_var)]
'This is a string'
```

## Lists and Tuples
A **list** is an iterable, ordered, and mutable collection of items. It is like an `array()` in other programming languages. A list can contain any type of items.

```python
>>> my_var = [1, 2, 3, 4, 5]
>>> type(my_var)
<class 'list'>
>>> isinstance(my_var, list)
True
```

Like strings, lists are ordered as well. Which means that they support extracting individual items using numerical indices. Using an index that is larger than the length of the string will produce an `index out of range` error.

```python
>> my_var[0]
1
>>> my_var[4]
5
>>> my_var[10]
Traceback (most recent call last):
  File "<stdin>", line 1, in <module>
IndexError: list index out of range
```

Lists also support slicing. For example:

```python
>>> my_var[0:]
[1, 2, 3, 4, 5]
>>> my_var[0:1]
[1]
>>> my_var[:-1]
[1, 2, 3, 4]
>>> my_var[:len(my_var)]
[1, 2, 3, 4, 5]
```

A **tuple** is exactly the same as a list, but with one difference: tuples are *immutable*. This means that once a tuple is defined, its value cannot be changed.

```python
>>> my_var = (1, 2, 3, 4, 5)
>>> type(my_var)
<class 'tuple'>
>>> isinstance(my_var, tuple)
True
```

Tuples are great for write-protecting data, and are usually faster than lists because they cannot be dynamically altered. In the example below, trying to change the value of a tuple produces an error.

```python
>>> my_var[0] = 100
Traceback (most recent call last):
  File "<stdin>", line 1, in <module>
TypeError: 'tuple' object does not support item assignment
```

Tuples, like lists, also support extracting elements using integer indices.

```python
>>> my_var[0]
1
>>> my_var[4]
5
>>> my_var[10]
Traceback (most recent call last):
  File "<stdin>", line 1, in <module>
IndexError: tuple index out of range
```

Tuples also support slicing.

```python
>>> my_var[0:]
(1, 2, 3, 4, 5)
>>> my_var[0:1]
(1,)
>>> my_var[:-1]
(1, 2, 3, 4)
>>> my_var[:len(my_var)]
(1, 2, 3, 4, 5)
```

## Sets & Dictionaries
A **set** is an unordered collection of unique items.

```python
>>> my_var = {1, 2, 3, 4, 5}
>>> type(my_var)
<class 'set'>
>>> isinstance(my_var, set)
True
```

It is used when we want to automatically remove duplicates from a list of items. 

```python
>>> no_duplicates = {1, 1, 2, 2, 3, 3, 4, 4, 5, 5, 5}
>>> print(no_duplicates)
{1, 2, 3, 4, 5}
```

Because it is unordered, it does not support extracting elements using integer indices or slicing. It does, however, support other methods, such as `union()`, `intersection()`, and `difference()`.

```python
>>> my_var = {1, 2, 3}
>>> my_var_2 = {4, 5}
>>> my_var.union(my_var_2)
{1, 2, 3, 4, 5}

>>> my_var_2 = {1, 2, 3}
>>> my_var.intersection(my_var_2)
{1, 2, 3}

>>> my_var_2 = {4, 5}
>>> my_var.difference(my_var_2)
{1, 2, 3}
```

A **dictionary** in Python is an iterable, mutable, and unordered collection of key-value pairs. They are great for storing a large amount of information.

```python
>>> my_var = {"a": 1, "b": 2, "c": 3, "d": 4, "e": 5}
>>> type(my_var)
<class 'dict'>
>>> isinstance(my_var, dict)
True
```

Like Sets, the keys in a dictionary must be unique.

```python
>>> no_duplicates = {"a": 1, "a": 11, "b": 2, "b": 22, "c": 3, "c": "33", "d": 4, "e": 5}
>>> print(no_duplicates)
{'c': '33', 'e': 5, 'a': 11, 'd': 4, 'b': 22}
```

Dictionary values can be retrieved using the key.

```python
>>> my_var = {"a": 1, "b": 2, "c": 3, "d": 4, "e": 5}
>>> my_var['a']
1
>>> my_var['d']
4
```

## Type Casting
The various data types listed above can be cast, or converted, from one to the other. Casting is performed by using the data type's class constructor, such as `int()`, `float()`, `str()`, `list()`, `tuple()`, `set()`, and `dict()`.

#### Casting to `int`
Floats and strings can be converted to integers.

```python
>>> print(int(1.0))
1
>>> print(int("1"))
1
```

#### Casting to `float`
Integers and strings can be converted to floating-points.

```python
>>> print(float(1))
1.0
>>> print(float("1"))
1.0
```

#### Casting to `complex`
Integers, floating-points, and strings can be converted to complex numbers.

```python
>>> print(complex(1))
(1+0j)
>>> print(complex(1.0))
(1+0j)
>>> print(complex("1+1j"))
(1+1j)
```

#### Casting to `list`
Strings, tuples, sets, and dictionaries can be converted to lists.

```python
>>> print(list("12345"))
['1', '2', '3', '4', '5']
>>> 
>>> print(list(tuple("12345")))
['1', '2', '3', '4', '5']
>>> 
>>> print(list({1, 2, 3, 4, 5}))
[1, 2, 3, 4, 5]
>>> 
>>> print(list({"a": 1, "b": 2, "c": 3, "d": 4, "e": 5}))
['c', 'd', 'e', 'b', 'a']
```

#### Casting to `tuple`
Strings, lists, sets, and dictionaries can be converted to tuples.

```python
>>> print(tuple("12345"))
('1', '2', '3', '4', '5')
>>>
>>> print(tuple(list("12345")))
('1', '2', '3', '4', '5')
>>>
>>> print(tuple({1, 2, 3, 4, 5}))
(1, 2, 3, 4, 5)
>>>
>>> print(tuple({"a": 1, "b": 2, "c": 3, "d": 4, "e": 5}))
('c', 'd', 'e', 'b', 'a')
```
#### Casting to `set`
Strings, lists, tuples, and dictionaries can be converted to tuples.

```python
>>> print(set("12345"))
{'4', '5', '2', '1', '3'}
>>>
>>> print(set([1, 2, 3, 4, 5]))
{1, 2, 3, 4, 5}
>>>
>>> print(set((1, 2, 3, 4, 5)))
{1, 2, 3, 4, 5}
>>>
>>> print(set({"a": 1, "b": 2, "c": 3, "d": 4, "e": 5}))
{'c', 'd', 'e', 'b', 'a'}
```

#### Casting to `dictionary`
Lists, tuples, or sets can be converted to a dictionary if they are provided in pairs. Sets can only be converted to dictionaries if they are provided within a list or a tuple.

```python
>>> print(dict([[1, 2], [3, 4], [5, 6]]))
{1: 2, 3: 4, 5: 6}
>>>
>>> print(dict(((1, 2), (3, 4), (5, 6))))
{1: 2, 3: 4, 5: 6}
>>>
>>> print(dict([{1, 2}, {3, 4}, {5, 6}]))
{1: 2, 3: 4, 5: 6}
```

## Conclusion
As you can see, the various number and string data types can be easily converted from one to the other. Similarly, the iterable data types can be converted from one to the other. Note, however, that certain conversions lead to loss of data, such as when converting from `float` to `int` or when converting from `dictionary` to `set`. There will be loss of data when type casting from a higher data type to a lower data type.

## Resources

- [Python documentation on Data Structures](https://docs.python.org/2/tutorial/datastructures.html).
- [Variable types on Tutorials Point](https://www.tutorialspoint.com/python/python_variable_types.htm).