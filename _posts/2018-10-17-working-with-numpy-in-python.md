---
layout: post
title:  "Working with NumPy in Python"
number: 76
date:   2018-10-17 0:00
categories: data
---
NumPy, or **Num**erical **Py**thon, is a library that makes it easy to do statistical and set operations on linear series and matrices in Python. It is orders of magnitude faster than Python lists, [which I covered in my notes on Python Data Types]({% post_url 2018-09-29-data-types-in-python %}). NumPy is used quite frequently in data analysis and scientific calculations.

We're going to go over installing NumPy, and then creating, reading, and sorting NumPy arrays. NumPy arrays are also called *ndarrays*, short for **n-dimensional arrays**.

## Installing NumPy
Installing the NumPy package is really simple using `pip`, and it can be installed just like you would install any other package.

```bash
pip install numpy
```

With the NumPy package installed, just import it in your Python file.

```python
import numpy as np
```

Importing `numpy` as `np` is standard convention, but instead of using `np` you can use any other alias that you want.

## Why use NumPy? Because it is orders of maginitude faster than Python lists.
NumPy is orders of magnitude faster than normal Python lists when it comes to handling a large number of values. To see exactly how fast it is, let's first measure the time it takes for `min()` and `max()` operations on a normal Python list.

Let's first create a Python list with 999,999,999 items.

```bash
>>> my_list = range(1, 1000000000)
>>> len(my_list)
999999999
```

Now let's measure the time for a finding the minimum value in this list.

```bash
>>> start = time.time()
>>> min(my_list)
1
>>> print('Time elapsed in milliseconds: ' + str((time.time() - start) * 1000))
Time elapsed in milliseconds: 27007.00879096985
```

That took about 27,007 milliseconds, or about **27 seconds**. That's a long time. Let's now try to find the time for finding the maximum value.

```bash
>>> start = time.time()
>>> max(my_list)
999999999
>>> print('Time elapsed in milliseconds: ' + str((time.time() - start) * 1000))
Time elapsed in milliseconds: 28111.071348190308
```

That took about 28,111 milliseconds, which is about **28 seconds**.

Now let's try to find the time to find the minimum and maximum value using NumPy.

```bash
>>> my_list = np.arange(1, 1000000000)
>>> len(my_list)
999999999
>>> start = time.time()
>>> my_list.min()
1
>>> print('Time elapsed in milliseconds: ' + str((time.time() - start) * 1000))
Time elapsed in milliseconds: 1151.1778831481934
>>>
>>> start = time.time()
>>> my_list.max()
999999999
>>> print('Time elapsed in milliseconds: ' + str((time.time() - start) * 1000))
Time elapsed in milliseconds: 1114.8970127105713
```

That took about 1151 milliseconds for finding the minimum value, and 1114 milliseconds for finding the maximum value. These are around **1 second**.

As you can see, using NumPy reduces the time to find the minimum and maximum of a list of around a billion values **from around 28 seconds to 1 second**. This is the power of NumPy.

## Creating ndarrays using Python lists
There are several ways to create a ndarray in NumPy.

We can create a ndarray by using a list of elements.

```bash
>>> my_ndarray = np.array([1, 2, 3, 4, 5])
>>> print(my_ndarray)
[1 2 3 4 5]
```

With the above ndarray defined, let's check out a few things. First, the type of the variable defined above is `numpy.ndarray`. This is the type of all NumPy ndarrays. 

```bash
>>> type(my_ndarray)
<class 'numpy.ndarray'>
```

Another thing to note here would be *shape*. The shape of a ndarray is the length of each dimension of the ndarray. As you can see, the shape of `my_ndarray` is `(5,)`. This means that `my_ndarray` contains one dimension with 5 elements.

```bash
>>> np.shape(my_ndarray)
(5,)
```

The nunmber of dimensions in the array is called its *rank*. So the above ndarray has a rank of 1.

Let's define another ndarray `my_ndarray2` as a multi-dimensional ndarray. What will its shape be then? See below.

```bash
>>> my_ndarray2 = np.array([(1, 2, 3), (4, 5, 6)])
>>> np.shape(my_ndarray2)
(2, 3)
```

This is a rank 2 ndarray. Another attribute to check is the `dtype`, which is the data type. Checking the `dtype` for our ndarray gives us the following:

```bash
>>> my_ndarray.dtype
dtype('int64')
```

`int64` means that our ndarray is made up of 64-bit integers. NumPy cannot create a ndarray of mixed types, and must contain only one type of elements. If we define a ndarray containing a mix of element types, NumPy will automatically typecast all the elements to the highest element type available that can contain all the elements.

For example, creating a mix of `int`s and `float`s will create a `float64` ndarray.

```bash
>>> my_ndarray2 = np.array([1, 2.0, 3])
>>> print(my_ndarray2)
[1. 2. 3.]
>>> my_ndarray2.dtype
dtype('float64')
```

Also, setting one of the elements as `string` will create string ndarray of `dtype` equal to `<U21`, meaning our ndarray contains unicode strings.

```bash
>>> my_ndarray2 = np.array([1, '2', 3])
>>> print(my_ndarray2)
['1' '2' '3']
>>> my_ndarray2.dtype
dtype('<U21')
```

The `size` attribute will show the total number of elements that are present in our ndarray.

```bash
>>> my_ndarray = np.array([1, 2, 3, 4, 5])
>>> my_ndarray.size
5
```

## Creating ndarrays using NumPy methods
There are several NumPy methods available for creating ndarrays in case you don't want to create them directly using a list.

`np.zeros()` can be used to create a ndarray full of zeroes. It takes a shape as a parameter, which is a list containing the number of rows and columns. It can also take an optional `dtype` parameter which is the data type of the ndarray.

```bash
>>> my_ndarray = np.zeros([2,3], dtype=int)
>>> print(my_ndarray)
[[0 0 0]
 [0 0 0]]
```

`np.ones()` can be used to create a ndarray full of ones.

```bash
>>> my_ndarray = np.ones([2,3], dtype=int)
>>> print(my_ndarray)
[[1 1 1]
 [1 1 1]]
```

`np.full()` can be used to fill a ndarray with a specific value.

```bash
>>> my_ndarray = np.full([2,3], 10, dtype=int)
>>> print(my_ndarray)
[[10 10 10]
 [10 10 10]]
```

`np.eye()` can be used to create an identity matrix/ndarray, which is a square matrix with ones all along the main diagnal. A square matrix is a matrix with the same number of rows and columns.

```bash
>>> my_ndarray = np.eye(3, dtype=int)
>>> print(my_ndarray)
[[1 0 0]
 [0 1 0]
 [0 0 1]]
```

`np.diag()` can be used to create a matrix with the specified values along the diagnal, and zeroes in the rest of the matrix.

```bash
>>> my_ndarray = np.diag([10, 20, 30, 40, 50])
>>> print(my_ndarray)
[[10  0  0  0  0]
 [ 0 20  0  0  0]
 [ 0  0 30  0  0]
 [ 0  0  0 40  0]
 [ 0  0  0  0 50]]
```

`np.arange()` can be used to create a ndarray with a specific range of values. It is used by specifying a start and end (exclusive) range of integers and a step size.

```bash
>>> my_ndarray = np.arange(1, 20, 3)
>>> print(my_ndarray)
[ 1  4  7 10 13 16 19]
```

## Reading ndarrays
The values of a ndarray can be read using indexing, slicing, or boolean indexing.

### Reading ndarrays using indexing
In indexing, we can read the values using the integer indices of the elements of the ndarray, much like we would read a Python list. Just like Python lists, the indices start from zero.

For example, in the ndarray defined as below:

```bash
>>> my_ndarray = np.arange(1, 20, 3)
```

The fourth value will be `my_ndarray[3]`, or `10`. The last value will be `my_ndarray[-1]`, or `19`.

```bash
>>> my_ndarray = np.arange(1, 20, 3)
>>> print(my_ndarray[0])
1
>>> print(my_ndarray[3])
10
>>> print(my_ndarray[-1])
19
>>> print(my_ndarray[5])
16
>>> print(my_ndarray[6])
19
```

### Reading ndarrays using slicing
We can also use slicing to read chunks of the ndarray. Slicing works be specifying a start index and an end index using a colon (`:`) operator. Python will then fetch the slice of the ndarray between that start and end index.

```bash
>>> print(my_ndarray[:])
[ 1  4  7 10 13 16 19]
>>> print(my_ndarray[2:4])
[ 7 10]
>>> print(my_ndarray[5:6])
[16]
>>> print(my_ndarray[6:7])
[19]
>>> print(my_ndarray[:-1])
[ 1  4  7 10 13 16]
>>> print(my_ndarray[-1:])
[19]
```

Slicing creates a reference, or view, of a ndarray. This means that modifying the values in a slice will also change the values of the original ndarray.

For example:

```bash
>>> my_ndarray[-1:] = 100
>>> print(my_ndarray)
[  1   4   7  10  13  16 100]
```

For slicing ndarrays with rank more than 1, the `[row-start-index:row-end-index, column-start-index:column-end-index]` syntax can be used.

```bash
>>> my_ndarray2 = np.array([(1, 2, 3), (4, 5, 6)])
>>> print(my_ndarray2)
[[1 2 3]
 [4 5 6]]
>>> print(my_ndarray2[0:2,1:3])
[[2 3]
 [5 6]]
```

### Reading ndarrays using boolean indexing
Another way to read ndarrays is using boolean indexing. In this method, we specify a filtering condition within square brackets, and a section of the ndarray that matches that criteria is returned.

For example, to get all the values in a ndarray greater than 5, we might specify a boolean indexing operation as `my_ndarray[my_ndarray > 5]`. This operation will return a ndarray that contains all values greater than 5.

```bash
>>> my_ndarray = np.array([1, 2, 3, 4, 5, 6, 7, 8, 9, 10])
>>> my_ndarray2 = my_ndarray[my_ndarray > 5]
>>> print(my_ndarray2)
[ 6  7  8  9 10]
```

For example, to get all the even values in a ndarray, we might use a boolean indexing operation as follows:

```bash
>>> my_ndarray2 = my_ndarray[my_ndarray % 2 == 0]
>>> print(my_ndarray2)
[ 2  4  6  8 10]
```

And to get all the odd values, we might use this:

```bash
>>> my_ndarray2 = my_ndarray[my_ndarray % 2 == 1]
>>> print(my_ndarray2)
[1 3 5 7 9]
```

## Vector and scalar arithmetic with ndarrays
NumPy ndarrays allow vector and scalar arithmetic operations. In vector arithmetic, an element-wise arithmetic operation is performed between to ndarrays. In scalar arithmetic, an arithmetic operation is performed between a ndarray and a constant scalar value.

Consider the two ndarrays below.

```bash
>>> my_ndarray = np.array([1, 2, 3, 4, 5])
>>> my_ndarray2 = np.array([6, 7, 8, 9, 10])
```

If we add the above two ndarrays, it would produce a new ndarray where each element of the two ndarrays would be added. For example, the first element of the resultant ndarray would be the result of adding the first elements of the original ndarrays, and so on.

```bash
>>> print(my_ndarray2 + my_ndarray)
[ 7  9 11 13 15]
```

Here, `7` is the sum of `1` and `6`, which are the first two elements of the ndarrays we've added together. Similarly, `15` is the sum of `5` and `10`, which are the last elements.

Consider the following arithmetic operations:

```bash
>>> print(my_ndarray2 - my_ndarray)
[5 5 5 5 5]
>>>
>>> print(my_ndarray2 * my_ndarray)
[ 6 14 24 36 50]
>>>
>>> print(my_ndarray2 / my_ndarray)
[6.         3.5        2.66666667 2.25       2.        ]
```

Adding a scalar value to a ndarray has a similar effect: the scalar value is added to all the elements of the ndarray. This is called *broadcasting*.

```bash
>>> print(my_ndarray + 10)
[11 12 13 14 15]
>>>
>>> print(my_ndarray - 10)
[-9 -8 -7 -6 -5]
>>>
>>> print(my_ndarray * 10)
[10 20 30 40 50]
>>>
>>> print(my_ndarray / 10)
[0.1 0.2 0.3 0.4 0.5]
```

## Sorting ndarrays
There are two ways available to sort ndarrays: we can sort them in-place or out-of-place. In-place sorting sorts and modifies the original ndarray, and out-of-place sorting will return the sorted ndarray but not modify the original one. Let's try out both examples.

```bash
>>> my_ndarray = np.array([3, 1, 2, 5, 4])
>>> my_ndarray.sort()
>>> print(my_ndarray)
[1 2 3 4 5]
```

As you can see, the `sort()` method sorts the ndarray in-place and modifies the original array.

There is another method called `np.sort()` which sorts the array out of place.

```bash
>>> my_ndarray = np.array([3, 1, 2, 5, 4])
>>> print(np.sort(my_ndarray))
[1 2 3 4 5]
>>> print(my_ndarray)
[3 1 2 5 4]
```

As you can see, the `np.sort()` method returns a sorted ndarray but does not modify it.

## Conclusion
We've covered quite a bit about NumPy and ndarrays. We talked about creating ndarrays, the different ways of reading them, basic vector and scalar arithmetic, and sorting. There is a lot more to explore with NumPy, including set operations like `union()` and `intersection()`, statistical operations like `min()` and `max()`, etc. I've mentioned some links in the resources section below which you can use to read more about these operations.

I hope the examples I demonstrated above were useful. Have fun exploring NumPy :)

## Resources

- [NumPy website](http://www.numpy.org/).
- [NumPy on Wikipedia](https://en.wikipedia.org/wiki/NumPy).
- [NumPy tutorial on TutorialsPoints](https://www.tutorialspoint.com/numpy/).