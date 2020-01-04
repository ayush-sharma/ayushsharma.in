---
layout: post
title:  "Algorithms in Python: Exchange Sorts"
number: 78
date:   2019-03-02 0:00
categories: algorithms
---
Today I want to talk about implementing exchange sorting algorithms in Python. We'll look at some of the most popular exchange sorts, such as ***Bubble sort***, ***Cocktail shaker sort***, ***Odd-even sort***, ***Gnome sort***, ***Quicksort***, and ***Bogosort***.

An exchange sort algorithm is one which compares adjacent elements and moves them to their correct position by swapping them based on a less-than rule. For example, while sorting to ascending order, we might swap if the element on the left is greater than the element on the right. Iteratively following this process gives us a final list where the input elements are sorted in ascending order.

Let's go over some of these exchange sorting algorithms.

## Bubble Sort
Bubble sort, or ***sinking sort***, is a simple sorting algorithm that repeatedly iterates through the list of elements from left to right, and swaps them if the left element is greater than the one on the right. In each pass, the greatest element “sinks down” to the right, or the end, of the list, and the lowest element “bubbles up” to the left, or the beginning, of the list.

#### Algorithm
To understand the basic algorithm, let’s say we want to sort the list `[1, 5, 3, 4, 9]`.

In each pass, the element on the left will be compared with the element on the right, and swapped if the one on the left is greater. The sorting will happen as follows (elements in brackets are being compared):

```
Pass 1:
[(1, 5), 3, 4, 9] -> [1, 5, 3, 4, 9] No swap, since 1 < 5.
[1, (5, 3), 4, 9] -> [1, 3, 5, 4, 9] Swap, since 5 > 3.
[1, 3, (5, 4), 9] -> [1, 3, 4, 5, 9] Swap, since 5 > 4.
[1, 3, 4, (5, 9)] -> [1, 3, 4, 5, 9] No swap, since 5 < 9.

Pass 2:
[1, 3, 4, 5, 9] -> [1, 3, 4, 5, 9] No swap, since all elements are in their correct positions.
```

Note that this algorithm needs an entire extra pass at the end to know that the list is sorted.

#### Python Implementation
In Python, the code would look like this:

```python
def bubbleSort(items):

    swapped = True
    pass_count = 1
    while(swapped):

        print('Pass ' + str(pass_count))

        swapped = False

        for i in range(0, len(items) - 1):

            if items[i] > items[i + 1]:

                print('Swapping '
                      + str(items[i])
                      + ' and '
                      + str(items[i+1]))

                items[i], items[i + 1] = items[i+1], items[i]
                swapped = True

        print('After pass '
              + str(pass_count)
              + ', items are: '
              + str(items))

        pass_count += 1

    return items
```

The above code can be run as follows:

```python
items = [2, 3, 4, 5, 1]
items = bubbleSort(items, 0, 4)
print(items)
```

Once we run the code above, we should see the following output:

```bash
Pass 1
Swapping 5 and 1
After pass 1, items are: [2, 3, 4, 1, 5]
Pass 2
Swapping 4 and 1
After pass 2, items are: [2, 3, 1, 4, 5]
Pass 3
Swapping 3 and 1
After pass 3, items are: [2, 1, 3, 4, 5]
Pass 4
Swapping 2 and 1
After pass 4, items are: [1, 2, 3, 4, 5]
Pass 5
After pass 5, items are: [1, 2, 3, 4, 5]
[1, 2, 3, 4, 5]
```

#### Performance
- Worst-case: ***O(n<sup>2</sup>)***
- Best-case: ***O(n)***
- Average-case: ***O(n<sup>2</sup>)***

#### Additional Notes
While using Bubble sort, some elements can move faster (called rabbits) than others (called turtles). For example, the largest element can take part in successive swaps in each pass, and therefore move to the end very quickly (a rabbit), but a smaller element can move to the left only once in each pass and is therefore slower (a turtle).

Bubble sort is not a practical sorting algorithm, and is not recommended for use. It has one of the worst performances of the known sorting algorithms, and it has even been debated that it no longer be taught in computer science curriculums altogether.

## Cocktail shaker sort
The Cocktail shaker sort, also called ***bi-directional bubble sort***, builds on the Bubble sort algorithm above by moving turtles a bit faster to the left. It differs in that in every pass, it not only moves the largest element to the right, but also moves the smallest element to the left. It does this by running two loops, one from the start of the list to the end for rabbits, and another from the end of the list to the start for turtles. So each pass in cocktail sort is equivalent to two passes in bubble sort.

#### Algorithm
As an example, let’s sort `[2, 3, 4, 5, 1]` using Cocktail sort.

```
Pass 1:
[(2, 3), 4, (5, 1)] -> [2, 3, 4, 1, 5] No swap on the left, but swap on the right since 5 > 1.
[2, (3, (4), 1), 5] -> [2, 3, 1, 4, 5] No swap on the left, but swap on the right since 4 > 1.
[2, (3, (1), 4), 5] -> [2, 1, 3, 4, 5] Swap on the left since 3 > 1, but no swap on the right since 1 < 4.
[(2, 1), 3, (4, 5)] -> [1, 2, 3, 4, 5] Swap on the left since 2 > 1, but no swap on the right since 4 < 5.

Pass 2:
[1, 2, 3, 4, 5] -> [1, 2, 3, 4, 5] No swap, since list is sorted.
```

Note that this algorithm needs an entire extra pass at the end to know that the list is sorted.

### Python Implementation
In Python, the code would look like this:

```python
def cocktailShakerBubbleSort(items):

    swapped = True
    pass_count = 1
    while(swapped):

        print('Pass ' + str(pass_count))

        swapped = False

        for i in range(0, len(items) - 1):

            if items[i] > items[i + 1]:

                print('Swapping '
                      + str(items[i])
                      + ' and '
                      + str(items[i+1]))

                items[i], items[i +
                                1] = items[i+1], items[i]
                swapped = True

            if items[len(items) - i - 2] > items[len(items) - i - 1]:

                print('Swapping '
                      + str(items[len(items) - i - 2])
                      + ' and '
                      + str(items[len(items) - i - 1]))

                items[len(items) - i - 2], items[len(items) - i -
                                                 1] = items[len(items) - i - 1], items[len(items) - i - 2]
                swapped = True

        print('After pass '
              + str(pass_count)
              + ', items are: '
              + str(items))

        pass_count += 1

    return items
```

The above code can be run as follows:

```python
items = [2, 3, 4, 5, 1]
items = cocktailShakerBubbleSort(items)
print(items)
```

Once we run the code above, we should see the following output:

```bash
Pass 1
Swapping 5 and 1
Swapping 4 and 1
Swapping 3 and 1
Swapping 2 and 1
After pass 1, items are: [1, 2, 3, 4, 5]
Pass 2
After pass 2, items are: [1, 2, 3, 4, 5]
[1, 2, 3, 4, 5]
```

#### Performance
- Worst-case: ***O(n<sup>2</sup>)***
- Best-case: ***O(n)***
- Average-case: ***O(n<sup>2</sup>)***

#### Additional Notes
While Cocktail shaker sort is better than bubble sort in that it moves turtles quicker and requires fewer passes, in terms of performance it does not provide a significant improvement to recommend it over other, better sorting algorithms.

## Odd-Even Sort
Odd-event sort, or ***brick sort***, is another comparison sorting algorithm. It works by iterating through the list, and first comparing all the odd-indexed pairs, and swapping them if the one on the left is greater than the one of the right, and then repeating the process for the even-indexed pairs. This process is repeated with multiple passes until the list is sorted.

#### Algorithm
As an example, let’s apply the algorithm for sorting the list `[2, 3, 4, 5, 1]`.

```
Pass 1:
[(2, 3), 4, 5, 1] -> [2, 3, 4, 5, 1] No swap, since 2 < 3.
[2, 3, (4, 5), 1] -> [2, 3, 4, 5, 1] No swap, since 4 < 5.
[2, (3, 4), 5, 1] -> [2, 3, 4, 5, 1] No swap, since 3 < 4.
[2, 3, 4, (5, 1)] -> [2, 3, 4, 1, 5] Swap, since 5 > 1.

Pass 2:
[(2, 3), 4, 1, 5] -> [2, 3, 4, 1, 5] No swap, since 2 < 3.
[2, 3, (4, 1), 5] -> [2, 3, 1, 4, 5] Swap, since 4 > 1.
[2, (3, 1), 4, 5] -> [2, 1, 3, 4, 5] Swap, since 3 > 1.
[2, 1, 3, (4, 5)] -> [2, 1, 3, 4, 5] No swap, since 4 < 5.

Pass 3:
[(2, 1), 3, 4, 5] -> [1, 2, 3, 4, 5] Swap, since 2 > 1.
[1, 2, (3, 4), 5] -> [1, 2, 3, 4, 5] No swap, since 3 < 4.
[1, (2, 3), 4, 5] -> [1, 2, 3, 4, 5] No swap, since 2 < 3.
[1, 2, 3, (4, 5)] -> [1, 2, 3, 4, 5] No swap, since 4 < 5.

Pass 4:
[(1, 2), 3, 4, 5] -> [1, 2, 3, 4, 5] No swap, since 1 < 2.
[1, 2, (3, 4), 5] -> [1, 2, 3, 4, 5] No swap, since 3 < 4.
[1, (2, 3), 4, 5] -> [1, 2, 3, 4, 5] No swap, since 2 < 3.
[1, 2, 3, (4, 5)] -> [1, 2, 3, 4, 5] No swap, since 4 < 5.
```

Note that this algorithm needs an entire extra pass at the end to know that the list is sorted.

#### Python Implementation
In Python, the code would look like this:

```python
def oddEvenSort(items):

    swapped = True
    pass_count = 1
    while(swapped):

        print('Pass ' + str(pass_count))

        swapped = False

        for i in range(0, len(items) - 1, 2):

            print('Comparing ' + str(items[i]) + ' and ' + str(items[i + 1]))

            if items[i] > items[i + 1]:

                print('Swapping '
                      + str(items[i])
                      + ' and '
                      + str(items[i+1]))

                items[i], items[i +
                                1] = items[i+1], items[i]
                swapped = True

        for i in range(1, len(items) - 1, 2):

            print('Comparing ' + str(items[i]) + ' and ' + str(items[i + 1]))

            if items[i] > items[i + 1]:

                print('Swapping '
                      + str(items[i])
                      + ' and '
                      + str(items[i+1]))

                items[i], items[i +
                                1] = items[i+1], items[i]
                swapped = True

        print('After pass '
              + str(pass_count)
              + ', items are: '
              + str(items))

        pass_count += 1

    return items
```

The above code can be run as follows:

```python
items = [2, 3, 4, 5, 1]
items = oddEvenSort(items)
print(items)
```

Once we run the code above, we should see the following output:

```bash
Pass 1
Comparing 2 and 3
Comparing 4 and 5
Comparing 3 and 4
Comparing 5 and 1
Swapping 5 and 1
After pass 1, items are: [2, 3, 4, 1, 5]
Pass 2
Comparing 2 and 3
Comparing 4 and 1
Swapping 4 and 1
Comparing 3 and 1
Swapping 3 and 1
Comparing 4 and 5
After pass 2, items are: [2, 1, 3, 4, 5]
Pass 3
Comparing 2 and 1
Swapping 2 and 1
Comparing 3 and 4
Comparing 2 and 3
Comparing 4 and 5
After pass 3, items are: [1, 2, 3, 4, 5]
Pass 4
Comparing 1 and 2
Comparing 3 and 4
Comparing 2 and 3
Comparing 4 and 5
After pass 4, items are: [1, 2, 3, 4, 5]
[1, 2, 3, 4, 5]
```

#### Performance
- Worst-case: ***O(n<sup>2</sup>)***
- Best-case: ***O(n)***

## Gnome Sort
Gnome sort is another simple comparison-based sorting algorithm that works by comparing and swapping elements to move them to the correct positions. An important distinction is this: once a swap is made, it looks at the previous element to check if a new out-of-order pair has been created, and if so, moves back one position to sort that pair. If not, it moves forward to the next pair. This approach ensures that no nested loops are required, as the algorithm will move forwards and backwards and swap pairs as required, even ones that are now out of order due to a new swap.

#### Algorithm
As an example, let’s apply the algorithm for sorting the list `[2, 3, 4, 5, 1]`.

```
[(2), 3, 4, 5, 1] -> [2, 3, 4, 5, 1] No swap since we’re at the beginning. Moving forward.
[(2, 3), 4, 5, 1] -> [2, 3, 4, 5, 1] No swap, since 2 < 3. Moving forward.
[2, (3, 4), 5, 1] -> [2, 3, 4, 5, 1] No swap, since 3 < 4. Moving forward.
[2, 3, (4, 5), 1] -> [2, 3, 4, 5, 1] No swap, since 4 < 5. Moving forward.
[2, 3, 4, (5, 1)] -> [2, 3, 4, 1, 5] Swap, since 5 > 1. Moving backward.
[2, 3, (4, 1), 5] -> [2, 3, 1, 4, 5] Swap, since 4 > 1. Moving backward.
[2, (3, 1), 4, 5] -> [2, 1, 3, 4, 5] Swap, since 3 > 1. Moving backward.
[(2, 1), 3, 4, 5] -> [1, 2, 3, 4, 5] Swap, since 2 > 1. Moving backward.
[(2), 1, 3, 4, 5] -> [2, 1, 3, 4, 5] No swap since we’re at the beginning. Moving forward.
[(1, 2), 3, 4, 5] -> [1, 2, 3, 4, 5] No swap, since 1 < 2. Moving forward.
[1, (2, 3), 4, 5] -> [1, 2, 3, 4, 5] No swap, since 2 < 3. Moving forward.
[1, 2, (3, 4), 5] -> [1, 2, 3, 4, 5] No swap, since 3 < 4. Moving forward.
[1, 2, 3, (4, 5)] -> [1, 2, 3, 4, 5] No swap, since 4 < 5. Moving forward.
```

As you can see, Gnome sort can implement the same comparison-based sorting as bubble sort, without the need for nested loops.

#### Python Implementation
In Python, the code would look like this:

```python
def gnomeSort(items):

    pos = 0
    pass_count = 1
    while(pos < len(items)):

        print('Pass ' + str(pass_count) + ', position is: ' + str(pos))

        if pos == 0 or items[pos] >= items[pos - 1]:

            print('Moving forward')
            pos += 1

        else:

            print('Swapping '
                      + str(items[pos])
                      + ' and '
                      + str(items[pos - 1])
                      + ' and moving back')

            items[pos], items[pos - 1] = items[pos - 1], items[pos]

            pos -= 1

        print('After pass '
              + str(pass_count)
              + ', items are: '
              + str(items))

        pass_count += 1

    return items
```

The above code can be run as follows:

```python
items = [2, 3, 4, 5, 1]
items = gnomeSort(items)
print(items)
```

Once we run the code above, we should see the following output:

```bash
Pass 1, position is: 0
Moving forward
After pass 1, items are: [2, 3, 4, 5, 1]
Pass 2, position is: 1
Moving forward
After pass 2, items are: [2, 3, 4, 5, 1]
Pass 3, position is: 2
Moving forward
After pass 3, items are: [2, 3, 4, 5, 1]
Pass 4, position is: 3
Moving forward
After pass 4, items are: [2, 3, 4, 5, 1]
Pass 5, position is: 4
Swapping 1 and 5 and moving back
After pass 5, items are: [2, 3, 4, 1, 5]
Pass 6, position is: 3
Swapping 1 and 4 and moving back
After pass 6, items are: [2, 3, 1, 4, 5]
Pass 7, position is: 2
Swapping 1 and 3 and moving back
After pass 7, items are: [2, 1, 3, 4, 5]
Pass 8, position is: 1
Swapping 1 and 2 and moving back
After pass 8, items are: [1, 2, 3, 4, 5]
Pass 9, position is: 0
Moving forward
After pass 9, items are: [1, 2, 3, 4, 5]
Pass 10, position is: 1
Moving forward
After pass 10, items are: [1, 2, 3, 4, 5]
Pass 11, position is: 2
Moving forward
After pass 11, items are: [1, 2, 3, 4, 5]
Pass 12, position is: 3
Moving forward
After pass 12, items are: [1, 2, 3, 4, 5]
Pass 13, position is: 4
Moving forward
After pass 13, items are: [1, 2, 3, 4, 5]
[1, 2, 3, 4, 5]
```

#### Performance
- Worst-case: ***O(n<sup>2</sup>)***
- Best-case: ***&Theta;(n)***
- Average-case: ***O(n<sup>2</sup>)***

## Quick Sort
Quicksort is an effecient sorting algorithm with a average performance of `O(nlogn)`. Unlike its competitor Merge sort, it can sort a list in-place without the need to create an extra copy of the list. It is a divide-and-conquer algorithm that works as follows:

1. Pick a element called a pivot from the input list.
2. `Divide` and partition the input list such that all elements less than the pivot are before it, and all elements larger than the pivot are after it.
3. `Conquer` the list by recursively applying the `Divide` operation.

#### Algorithm
To implement the above divide-and-conque strategy, we follow the following procedure:

1. Pick a pivot from the list.
2. Start two counters `i` and `j`. `i` will start from the beginning, and find the next element that is greater than the pivot. `j` will start from the end, and find the next element that is less than the pivot. Once found, swap both elements.
3. Continue the process until `i` crosses `j`. At this point, `i` will be the sorted position of the pivot. So swap the element at `i` with the current pivot, and return `i` for the next round.
4. Recursively call the above function with the new `i` found above, separating the list into elements before the new `i` and elements after the new `i`.

#### Python Implementation
In Python, the code would look like this:

```python
def quickSort(items, start, end):

    if start < end:
        
        p = quickSortPartition(items, start, end)
        quickSort(items, start, p - 1)
        quickSort(items, p + 1, end)

    return items

def quickSortPartition(items, start, end):

    print('Partition with list = ' + str(items) + ', start = ' + str(start) + ' and end = ' + str(end))

    pivot = items[end]
    i = start
    j = end

    print('Pivot is ' + str(pivot))

    while i < j:

        while items[i] <= pivot:

            i += 1

        while items[j] > pivot:

            j -= 1

        print('i is ' + str(i) + ' and j is ' + str(j))

        if i < j:

            print('Swapping ' + str(items[i]) + ' and ' + str(items[j]))

            items[i], items[j] = items[j], items[i]

        else:

            print('No swapping.')

    print('Swapping pivot from ' + str(end) + ' to ' + str(i))
    items[end], items[i] = items[i], items[end]

    print('New pivot is ' + str(i))

    return i
```

The above code can be run as follows:

```python
items = [2, 3, 4, 5, 1]
items = quickSort(items, 0, 4)
print(items)
```

Once we run the code above, we should see the following output:

```bash
Partition with list = [2, 3, 4, 5, 1], start = 0 and end = 4
Pivot is 1
i is 0 and j is 4
Swapping 2 and 1
i is 1 and j is 0
No swapping.
Swapping pivot from 4 to 1
New pivot is 1
Partition with list = [1, 2, 4, 5, 3], start = 2 and end = 4
Pivot is 3
i is 2 and j is 4
Swapping 4 and 3
i is 3 and j is 2
No swapping.
Swapping pivot from 4 to 3
New pivot is 3
[1, 2, 3, 4, 5]
```

#### Performance
- Worst-case: ***O(n<sup>2</sup>)***
- Best-case: ***O(nlogn)***
- Average-case: ***O(nlogn)***

#### Additional Notes
The performance of Quick sort can vary dramatically with the choice of pivot. There are multiple options, such as picking the first element, the last element, a median, etc. I recommend that you review the Wikipedia page in the resources section to get a better understanding of the choice of pivots.

## BogoSort
BogoSort, also called ***stupid sort***, ***monkey sort***, or ***shotgun sort***, works by randomly shuffling the input list and then checking if the list is sorted, and if not, reshuffling the list and checking again. It’s one of those sorting algorithms that make you wonder why they even exist, and has no real-world application whatsoever, but I mention it because I’d really like to know if you manage to get away with implementing this in a production environment. At your own risk, please :)

#### Algorithm
Step 1: Check if list is sorted using a different sorting algorithm.
Step 2: If sorted, exit. If not, randomly shuffle input list.
Step 3: Repeat.

#### Python Implementation
In Python, the code would look like this:

```python
def bogoSort(items):

    pass_count = 1

    while items != sorted(items):

        random.shuffle(items)

        pass_count += 1

    print('After pass '
          + str(pass_count)
          + ', items are: '
          + str(items))

    return items
```

The above code can be run as follows:

```python
items = [2, 3, 4, 5, 1]
items = bogoSort(items)
print(items)
```

Once we run the code above, we should see the following output:

```bash
After pass 95, items are: [1, 2, 3, 4, 5]
[1, 2, 3, 4, 5]
```

#### Performance
- Worst-case: Either runs ***indefinitely***, or ***O((n+1)!)***.
- Best-case: ***O(n)***
- Average-case: ***O((n+1)!)***

## Resources
- [Bubble sort on Wikipedia](https://en.wikipedia.org/wiki/Bubble_sort)
- [Cocktail shaker sort on Wikipedia](https://en.wikipedia.org/wiki/Cocktail_shaker_sort)
- [Odd-even sort on Wikipedia](https://en.wikipedia.org/wiki/Odd%E2%80%93even_sort)
- [Quick sort on Wikipedia](https://en.wikipedia.org/wiki/Quicksort)
- [Gnome sort on Wikipedia](https://en.wikipedia.org/wiki/Gnome_sort)
- [Bogosort on Wikipedia](https://en.wikipedia.org/wiki/Bogosort)
