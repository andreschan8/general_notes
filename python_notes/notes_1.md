# Python Notes - Notes 1

## 1.1. Inner workings of for loops

We want to understand the way for loops internally work in python. According to [1], they work using iterators and we can mimic their functioning through the following block of code:
```python
def my_for(iterable, func):
    iterator = iter(iterable)
    done_looping = False
    while not done_looping:
        try:
            item = next(iterator)
        except StopIteration:
            done_looping = True
        else:
            func(iterable, item)
```
If we pass in an array and a custom function (the content of the for loop) we get the same functionality we'd get with a regular for loop:
```python
arr = ['a','b','c','d','e','f','g','h','i','j','k','l','m','n']

def my_function(iterable, item):
    print(item,end=' ')

my_for(arr, my_function)
```
Output:
```
a b c d e f g h i j k l m n
```
An important aspect regarding for loops is their change in behaviour when the iterable suffers changes during the iteration. For example, if we remove the first element of the iterable and print it we'd expect to get the exact same output we just did. That is, if our code looks like this
```python
arr = ['a','b','c','d','e','f','g','h','i','j','k','l','m','n']

for char in arr:
    print(char,end=' ')
    arr.pop(0)
```
we'd expect something like this:
```
a b c d e f g h i j k l m n
```
But instead we get this:
```
a c e g i k m
```
This same result has been obtained using our `my_for()` function, so we should be on the right track. Now, we need to know how iterators work internally. According to [2], a classic iterator yields the original data (as opposed to other kinds of iterators, which can transform the original data or generate new data) and thus is what we are using here. RealPython provides the code for a classic iterator as
```python
class SequenceIterator:
    def __init__(self, sequence):
        self._sequence = sequence
        self._index = 0

    def __iter__(self):
        return self

    def __next__(self):
        if self._index < len(self._sequence):
            item = self._sequence[self._index]
            self._index += 1
            return item
        else:
            raise StopIteration
```
First, note that in the snippet `self._sequence = sequence` we are creating a new reference for the element stored in the `sequence` variable. It's the same as creating and initializaing a variable, say `x=4`, and then creating a new one in terms of the first, that is, `y=x`. On the other hand, a Python iterator is created when we pass in an iterable (an array, for example) to the `iter()` built-in function. This returns the iterator (as we can see from the `__iter__()` method.) Therefore, we could get the following behaviour:
```python
>>>arr = ['a','b','c','d']
>>>iterator = iter(arr)
>>>print(next(iterator))
'a'
>>>arr[1] = 6
>>>print(next(iterator))
6
```
Here we are modifying the sequence parameter of the iterator without explicitly modifying `iterator`. 

Now we're ready to analyze the loop in which the first element is removed in each iteration. For easier reference we've placed the corresponding code below.

```python
def my_for(iterable, func):
    iterator = iter(iterable)
    done_looping = False
    while not done_looping:
        try:
            item = next(iterator)
        except StopIteration:
            done_looping = True
        else:
            func(iterable, item)

#Driver code
arr = ['a','b','c','d','e','f','g','h','i','j','k','l','m','n']

def my_function(iterable, item):
    print(item,end=' ')
    iterable.pop(0)

my_for(arr, my_function)
```
When we first call `my_for()` the loop starts and `item` is 'a'. Then we print it and remove it from `arr`, but `iterator._index` is 1 according to the definition of the `SequenceIterator` class (CAREFUL: apparently, Python's `iter()` function has other names for these variables, but for simplicity we are assuming they are the same as those from a `SequenceIterator` instance. Trying to print `iterator._index` would throw an error!) 

|iterator._index|item|arr[0]|
|---------------|----|------|
|0              |'a' |'a'   |
|1              |'c' |'b'   |
|2              |'e' |'c'   |

## References
[1] https://www.geeksforgeeks.org/understanding-for-loop-in-python/
[2] https://realpython.com/python-iterators-iterables/
















