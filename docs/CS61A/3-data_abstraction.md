# Data Abstraction

**Containers**

Built-in operators for testing whether an element appears in a compound value.

```python
>>> digits = [1, 8, 2, 8]
>>> 1 in digits
True
>>> 8 in digits
True
>>> 5 not in digits
True
>>> not(5 in digits)
True
```

## Identity Operators

- **Identity**
  - \<exp0\> **is** \<exp1\>
  - evaluates to True if both \<exp0\> and \<exp1\> evaluate to the same object

- **Equality**
  - \<exp0\> **==** \<exp1\>
  - evaluates to True if both \<exp0\> and \<exp1\> evaluate to equal values
- Identical objects are always equal values

## Native Data Types

Every value in Python has a `class` that determines what type of value it is. Values that share a class also share behavior. 

Native data types have the following properties:

- There are expressions that evaluate to values of native types, called ***literals***.
- There are built-in functions and operators to manipulate values of native types.

Python includes three native numeric types: integers (`int`), real numbers (`float`), and complex numbers (`complex`).

Integer literals (sequences of adjacent numerals) evaluate to `int` values, and mathematical operators manipulate these values.

## Data Abstraction

**Lecture Slide**

We need to guarantee that constructor and selector functions work together to specify the right behavior.

Data abstraction uses selectors and constructors to define behavior.

If behavior conditions are met, then the representation is valid.

You can recognize data abstraction by its behavior.

**rational data abstraction implemented as functions**

**constructor** is a higher order function

```python
def rational(n, d):
    def select(name):
        if name == 'n':
            return n
        elif name == 'd':
            return d
    return select
```

**selector** calls the object itself

```python
def numer(x):
    return x('n')
def denom(x):
    return x('d')
```

**The Closure Property of Data Types**

- A method for combining data values satisfies the closure property if:
  - The result of combination can itself be combined using the same method
- Closure is powerful because it permits us to create hierarchical structures 
- Hierarchical structures are made up of parts, which themselves are made up of parts, and so on

**Box-and-Pointer Notation in Environment Diagrams**

Lists are represented as a row of index-labeled adjacent boxes, one per element.

Each box either contains a primitive value or points to a compound value.

**From Textbook**

The general technique of isolating the parts of a program that deal with how data are represented from the parts that deal with how data are manipulated is a powerful design methodology called ***data abstraction***. 

Data abstraction makes programs much easier to design, maintain, and modify.

In general, the underlying idea of data abstraction is to identify a basic set of operations in terms of which all manipulations of values of some kind will be expressed, and then to use only those operations in manipulating the data

A powerful strategy for designing programs: ***wishful thinking***.

**Compound Data Structure: List**

0-indexed in Python: the index represents how far an element is offset from the beginning of the list.

```python
pair = [10, 20]
x,y = pair

>>> pair[0]
10
>>> from operator import getitem
>>> getitem(pair, 0)
10
```

We don't actually need the `list` type to create pairs.

```python
def pair(x, y):
    """Return a function that represents a pair."""
    def get(index):
        if index == 0:
            return x
        elif index == 1:
            return y
    return get

def select(p, i):
    """Return the element at index i of pair p."""
    return p(i)
```

```python
>>> p = pair(20, 14)
>>> select(p, 0)
20
>>> select(p, 1)
14
```

## Sequences

A sequence is an ordered collection of values.

Common behavior of many kinds of sequences

- **Length.** A sequence has a finite length. An empty sequence has length 0.
- A sequence has an element corresponding to any non-negative integer index less than its length, starting at 0 for the first element.

list

```python
>>> digits = [1, 8, 2, 8]
>>> len(digits)
4
>>> digits[3]
8
```

**append**

```python
>>> [2, 7] + digits * 2
[2, 7, 1, 8, 2, 8, 1, 8, 2, 8]
```

list in list

```python
>>> pairs = [[10, 20], [30, 40]]
>>> pairs[1]
[30, 40]
>>> pairs[1][0]
30
```

## Sequence Iteration

```python
# index
def count(s, value):
    """Count the number of occurrences of value in sequence s."""
    total, index = 0, 0
    while index < len(s):
        if s[index] == value:
            total = total + 1
        index = index + 1
    return total

# element
def count(s, value):
    """Count the number of occurrences of value in sequence s."""
    total = 0
    for elem in s:
        if elem == value:
            total = total + 1
    return total
```
recursively

```python
def mysum(L):
    if L == []:
        return 0
    else:
        return L[0] + mysum(L[1:])
```

## Sequence Unpacking

```python
>>> pairs = [[1,2], [2,2], [3,2], [4,4]]
>>> for x, y in pairs:
        if x == y:
            same_count = same_count + 1
>>> same_count
2
```

**range**

```python
>>> list(range(-2, 2))
[-2, -1, 0, 1]

>>> list(range(5, 8))
[5, 6, 7]

>>> list(range(4))
[0, 1, 2, 3]
```

**list comprehensions**

```python
>>> odds = [1, 3, 5, 7, 9]

>>> [x+1 for x in odds]
[2, 4, 6, 8, 10]

>>> [x for x in odds if 25 % x == 0]
[1, 5]
```

## Sequence Aggregation

Several built-in functions take iterable arguments and aggregate them into a value.

- `sum`: Return the sum of a 'start' value (default: 0) plus an iterable of numbers.
- `max`: With a single iterable argument, return its largest item. With two or more arguments, return the largest argument.
- `all`: Return True if bool(x) is True for all values x in the iterable. If the iterable is empty, return True.

```python
def divisors(n):
    return [1] + [x for x in range(2, n) if n % x == 0]
```

```python
def width(area, height):
    assert area % height == 0
    return area // height

def perimeter(width, height):
    return 2 * width + 2 * height

def minimum_perimeter(area):
    heights = divisors(area)
    perimeters = [perimeter(width(area, h), h) for h in heights]
    return min(perimeters)

>>> [minimum_perimeter(n) for n in range(1, 10)]
[4, 6, 8, 8, 12, 10, 16, 12, 12]
```

## Higher-Order Function

```python
def apply_to_all(map_fn, s):
    return [map_fn(x) for x in s]

def keep_if(filter_fn, s):
    return [x for x in s if filter_fn(x)]

def reduce(reduce_fn, s, initial):
    reduced = initial
    for x in s:
        reduced = reduce_fn(reduced, x)
    return reduced
```

i.e.

```python
>>> reduce(mul, [2, 4, 8], 1)
64
```

find perfect numbers

```python
>>> def divisors_of(n):
        divides_n = lambda x: n % x == 0
        return [1] + keep_if(divides_n, range(2, n))
>>> divisors_of(12)
[1, 2, 3, 4, 6]

>>> from operator import add
>>> def sum_of_divisors(n):
        return reduce(add, divisors_of(n), 0)
>>> def perfect(n):
        return sum_of_divisors(n) == n

>>> keep_if(perfect, range(1, 1000))
[1, 6, 28, 496]
```

## Conventional Names

The more common name for `apply_to_all` is `map` .

The more common name for `keep_if` is `filter`.

The `reduce` function is built into the `functools` module of the Python standard library. In this version, the `initial` argument is optional.

```python
>>> apply_to_all = lambda map_fn, s: list(map(map_fn, s))
>>> keep_if = lambda filter_fn, s: list(filter(filter_fn, s))

>>> from functools import reduce
>>> from operator import mul
>>> def product(s):
        return reduce(mul, s)

>>> product([1, 2, 3, 4, 5])
120
```

## Sequence Abstraction

**membership**

```python
>>> digits
[1, 8, 2, 8]
>>> 2 in digits
True
>>> 1828 not in digits
True
```

**slicing**

```python
>>> digits[0:2]
[1, 8]
>>> digits[1:]
[8, 2, 8]
>>> digits[::-1]
[8, 2, 8, 1]

>>> digits[0:len(digits):2]
[1, 2]

>>> [digits[i] for i in range(1,3)]
[8, 2]
```

## Strings

**assert -- used for debugging**

```python
x = "hello"

#if condition returns True, then nothing happens:
assert x == "hello"

#if condition returns False, AssertionError is raised:
assert x == "goodbye", "x should be hello"
```

**membership**

```python
>>> 'here' in "Where's Waldo?"
True
```

**multiline literals**

`\n` = line feed starts a new line

```python
>>> """The Zen of Python
claims, Readability counts.
Read more: import this."""
'The Zen of Python\nclaims, "Readability counts."\nRead more: import this.'
```

**string coercion**

```python
>>> str(2) + ' is an element of ' + str(digits)
'2 is an element of [1, 8, 2, 8]'
```

## Dictionaries

construct a dictionary from tuples

```python
>>> dict([(3, 9), (4, 16), (5, 25)])
{3: 9, 4: 16, 5: 25}
```

example

```python
numerals = {'I': 1, 'V': 5, 'X': 10}

>>> numerals['X']
10
```

**key, value, pair**

```python
>>> numerals.keys()
dict_keys(['I', 'V', 'X'])

>>> numerals.values()
dict_values([1, 5, 10])

>>> numerals.items()
dict_items([('I', 1), ('V', 5), ('X', 10)])
```

**if keys in dictionary**

```python
>>> 'X' in numerals
True
>>> 'X-ray' in numerals
False
>>> numerals.get('X', 0)
10
>>> numerals.get('X-ray', 0)
0
```

**dictionary comprehension**

```python
>>> squares = {x:x*x for x in range(10)}
>>> squares
{0: 0, 1: 1, 2: 4, 3: 9, 4: 16, 5: 25, 6: 36, 7: 49, 8: 64, 9: 81}
>>> squares[7]
49
```

limitations

- dictionaries are **unordered** collections of key-value pairs
- restrictions
  - two keys cannot be equal
  - a **key cannot be** a list or a dictionary (or any **mutable type**)

**dictionary object**

```python
>>> numerals['X'].pop('X')
>>> numerals.get('X')

>>> numerals.get('A', 0)
0
>>> numerals.get('V', 0)
5
```

## Trees

**Recursive description (wooden trees)**

- A **tree** has a root **label** and a list of **branches**
- Each branch is a **tree**
- A **tree** with **zero branches** is called a **leaf**
- A **tree** starts at the **root**

**Relative description (family trees)**

- Each location in a tree is called a **node**
- Each **node** has a **label** that can be any value (labels are usually referred as  locations)
- One node can be the **parent/child** of another (ancestor, descendant, sibling .etc)
- The top node is the **root node**

**Implementation**

- A tree has a root label and a list of branches
- Each branch is a tree

Constructor

```python
def tree(label, branches=[]):
    for branch in branches:
        assert is_tree(branch) # check: each branch is a tree
    return [label] + branches
```

Selectors

```python
def label(tree):
    return tree[0]

def branches(tree):
    return tree[1:]
```

check tree or leaf

```python
def is_tree(tree):
    if type(tree) != list or len(tree) < 1:
        return False
    for branch in branches (tree):
        if not is_tree(branch):
            return False
    return True

def is_leaf(tree):
    return not branches(tree) # check branches are empty
```

example

```python
>>> tree(1)
[1]
>>> is_leaf(tree(1))
True

>>> t = tree(1, [tree(5, [tree(7)]), tree(6)])
>>> t
[1, [5, [7]], [6]]
>>> label(t)
1
>>> branches(t)
[[5, [7]], [6]]
>>> branches(t)[0]
[5, [7]]
>>> is_tree(branches(t)[0])
True
>>> label(branches(t)[0])
5
```

a tree with no branches

```python
>>> leaf = tree(4, [])  # same as tree(4)
>>> is_tree(leaf)
True
>>> branches(leaf)
[]
```

## Strings

```python
>>> s = 'Hello'
>>> dir(s)
['__add__', '__class__', '__contains__', '__delattr__', '__dir__', '__doc__', '__eq__', '__format__', '__ge__', '__getattribute__', '__getitem__', '__getnewargs__', '__gt__', '__hash__', '__init__', '__init_subclass__', '__iter__', '__le__', '__len__', '__lt__', '__mod__', '__mul__', '__ne__', '__new__', '__reduce__', '__reduce_ex__', '__repr__', '__rmod__', '__rmul__', '__setattr__', '__sizeof__', '__str__', '__subclasshook__', 'capitalize', 'casefold', 'center', 'count', 'encode', 'endswith', 'expandtabs', 'find', 'format', 'format_map', 'index', 'isalnum', 'isalpha', 'isascii', 'isdecimal', 'isdigit', 'isidentifier', 'islower', 'isnumeric', 'isprintable', 'isspace', 'istitle', 'isupper', 'join', 'ljust', 'lower', 'lstrip', 'maketrans', 'partition', 'replace', 'rfind', 'rindex', 'rjust', 'rpartition', 'rsplit', 'rstrip', 'split', 'splitlines', 'startswith', 'strip', 'swapcase', 'title', 'translate', 'upper', 'zfill']
```

**ASCII -- American Standard Code for Information Interchange**

- Layout was chosen to support sorting by character code
- Rows indexed 2-5 are a useful 6-bit (64 element) subset
- Control characters were designed for transmission

```python
>>> ord('A')
65
>>> hex(ord('A'))
'0x41'
# row 4, column 1 in ASCII table
```

get a sound (alert) from the computer -- `\a` is "Bell" in ASCII

```python
>>> print('\a')

```

**Unicode Standard**

as of 2021

- 137,994 characters in Unicode 12.1
- 150 scripts (organized)
- Enumeration of character properties, such as case
- Supports bidirectional display order
- A canonical name for every character

```python
>>> print('\u0420\u043e\u0441\u0441\u0438\u044f')
Россия

>>> from unicodedata import name, lookup
>>> name('A')
'LATIN CAPITAL LETTER A'
>>> lookup('WHITE SMILING FACE')
'☺'
>>> lookup('WHITE SMILING FACE').encode()
b'\xe2\x98\xba'
```

## Mutable Objects

- All names that refer to the same object are affected by a mutation
- Only objects of mutable types can change: **lists** & **dictionaries**

**Mutation Can Happen Within a Function Call**

A function can change the value of any object in its scope.

## Mutation

**Sameness and Change**

- As long as we never modify objects, a compound object is just the totality of its pieces
- A rational number is just its numerator and denominator
- This view is no longer valid in the presence of change
- A compound data object has an "identity" in addition to the pieces of which it is composed
- A list is still "the same" list even if we change its contents
- Conversely, we could have two lists that happen to have the same contents, but are different

**Mutable Default Arguments are Dangerous**

A default argument value is part of a function value, not generated by a call

## Lists

```python
>>> suits = ['coin', 'string', 'myriad']  # A list literal
>>> original_suits = suits

>>> suits.pop()             # Removes and returns the final element
'myriad'
>>> suits.remove('string')  # Removes the first element that equals the argument
>>> suits.append('cup')              # Add an element to the end
>>> suits.extend(['sword', 'club'])  # Add all elements of a list to the end
>>> suits[2] = 'spade'  # Replace an element
>>> suits[0:2] = ['heart', 'diamond']  # Replace a slice

>>> suits
['heart', 'diamond', 'spade', 'club']
>>> original_suits
['heart', 'diamond', 'spade', 'club']
```

**Lists in Lists in Lists**

```python
>>> t = [1, 2, 3]
>>> t[1:3] = [t]
>>> [t]
[[1, [...]]]
>>> t.extend(t)
>>> t
[1, [...], 1, [...]]

>>> t = [1]
>>> t.append(t)
>>> t
[1, [...]]
# it's actually [1, [1, [1, [...]]]]
# in printing, it seems forever
# in storage, it is not
```

another example

```python
>>> t = [[1, 2], [3, 4]]
>>> t[0].append(t[1:2])
>>> t
[[1, 2, [[3, 4]]], [3, 4]]
```

**slicing -- inserting vs replacing -- can change list length**

`s[0:0]` -- inserting

```python
>>> s1 = [1, 2, 3]
>>> t = [4, [5], 6]
>>> s1[0:0] = t
>>> s1
[4, [5], 6, 1, 2, 3]
```

`s[0:1]` -- replacing

```python
>>> s2 = [1, 2, 3]
>>> s2[0:1] = t
>>> s2
[4, [5], 6, 2, 3]
```

**element replacing -- doesn't change list length**

```python
>>> s3 = [1, 2, 3]
>>> t = [4, [5], 6]
>>> s3[0] = t
>>> s3
[[4, [5], 6], 2, 3]
```

**make a copy**

```python
s = [1, 2, 3]
t = list(s)
```

`append` vs `extend`

```python
>>> l = [1, 2, 3]
>>> l.append(4)
>>> l
[1, 2, 3, 4]
>>> l.append([5,6])
>>> l
[1, 2, 3, 4, [5, 6]]
>>> l.extend([7,8])
>>> l
[1, 2, 3, 4, [5, 6], 7, 8]
>>> l.extend(9)
!ERROR!
```

`+` creates a new list; `append()` modifies on the original list

**list mutations**

- `append(el)`: Adds el to the end of the list, and returns None
- `extend(lst)`: Extends the list by concatenating it with lst, and returns None
- `insert(i, el)`: Insert el at index i (does not replace element but adds a new one), and returns None
- `remove(el)`: Removes the first occurrence of el in list, otherwise errors, and returns None
- `pop(i)`: Removes and returns the element at index i

## Tuples

Immutable sequences

```python
>>> (3, 4, 5, 6)
(3, 4, 5, 6)
>>> 3, 4, 5, 6
(3, 4, 5, 6)
>>> ()
()
>>> tuple()
()
>>> tuple([3, 4, 5, 6])
(3, 4, 5, 6)
```

operations

```python
>>> (3, 4) + (5, 6)
(3, 4, 5, 6)
>>> 5 in (3, 4, 5, 6)
True
```

tuples can be used as keys in dictionary, but list cannot

```python
>>> {(1, 2): 3}
{(1, 2): 3}

>>> {[1, 2]: 3}
!ERROR!
```

An immutable sequence may still change if it contains a mutable value as an element

```python
>>> s = ([1, 2], 3)
>>> s[0][0] = 4
>>> s
([4, 2], 3)

>>> s = ([1, 2], 3)
>>> s[0] = 4
!ERROR!
```



## Linked Lists

- A linked list is either empty or a first value and the rest of the linked list
- A common representation of a sequence constructed from nested pairs is called a ***linked list***.

```python
four = [1, [2, [3, [4, 'empty']]]]
```

recursive structure

```python
empty = 'empty'

def is_link(s):
    """s is a linked list if it is empty or a (first, rest) pair."""
    return s == empty or (len(s) == 2 and is_link(s[1]))

def link(first, rest):
    """Construct a linked list from its first element and the rest."""
    assert is_link(rest), "rest must be a linked list."
    return [first, rest]

def first(s):
    """Return the first element of a linked list s."""
    assert is_link(s), "first only applies to linked lists."
    assert s != empty, "empty linked list has no first element."
    return s[0]

def rest(s):
    """Return the rest of the elements of a linked list s."""
    assert is_link(s), "rest only applies to linked lists."
    assert s != empty, "empty linked list has no rest."
    return s[1]
```

 `link` is a constructor and `first` and `rest` are selectors for an abstract data representation of linked lists. The behavior condition for a linked list is that, like a pair, its constructor and selectors are inverse functions.

```python
>>> four = link(1, link(2, link(3, link(4, empty))))
>>> first(four)
1
>>> rest(four)
[2, [3, [4, 'empty']]]
```

linked list satisfies sequence abstraction

```python
def len_link(s):
    """Return the length of linked list s."""
    length = 0
    while s != empty:
        s, length = rest(s), length + 1
    return length

def getitem_link(s, i):
    """Return the element at index i of linked list s."""
    while i > 0:
        s, i = rest(s), i - 1
    return first(s)
```

recursively

```python
def len_link_recursive(s):
    """Return the length of a linked list s."""
    if s == empty:
        return 0
    return 1 + len_link_recursive(rest(s))

def getitem_link_recursive(s, i):
    """Return the element at index i of linked list s."""
    if i == 0:
        return first(s)
    return getitem_link_recursive(rest(s), i - 1)
```

transform list

```python
>>> def extend_link(s, t):
        """Return a list with the elements of s followed by those of t."""
        assert is_link(s) and is_link(t)
        if s == empty:
            return t
        else:
            return link(first(s), extend_link(rest(s), t))

>>> extend_link(four, four)
[1, [2, [3, [4, [1, [2, [3, [4, 'empty']]]]]]]]

>>> def apply_to_all_link(f, s):
        """Apply f to each element of s."""
        assert is_link(s)
        if s == empty:
            return s
        else:
            return link(f(first(s)), apply_to_all_link(f, rest(s)))

>>> apply_to_all_link(lambda x: x*x, four)
[1, [4, [9, [16, 'empty']]]]

>>> def keep_if_link(f, s):
        """Return a list with elements of s for which f(e) is true."""
        assert is_link(s)
        if s == empty:
            return s
        else:
            kept = keep_if_link(f, rest(s))
            if f(first(s)):
                return link(first(s), kept)
            else:
                return kept

>>> keep_if_link(lambda x: x%2 == 0, four)
[2, [4, 'empty']]

```

combine list

```python
>>> def join_link(s, separator):
        """Return a string of all elements in s separated by separator."""
        if s == empty:
            return ""
        elif rest(s) == empty:
            return str(first(s))
        else:
            return str(first(s)) + separator + join_link(rest(s), separator)

>>> join_link(four, ", ")
'1, 2, 3, 4'
```

count partitions

```python
def partitions(n, m):
        """Return a linked list of partitions of n using parts of up to m.
        Each partition is represented as a linked list.
        """
        if n == 0:
            return link(empty, empty) # A list containing the empty partition
        elif n < 0 or m == 0:
            return empty
        else:
            using_m = partitions(n-m, m)
            with_m = apply_to_all_link(lambda s: link(m, s), using_m)
            without_m = partitions(n, m-1)
            return extend_link(with_m, without_m)

def print_partitions(n, m):
        lists = partitions(n, m)
        strings = apply_to_all_link(lambda s: join_link(s, " + "), lists)
        print(join_link(strings, "\n"))

print_partitions(6, 4)
4 + 2
4 + 1 + 1
3 + 3
3 + 2 + 1
3 + 1 + 1 + 1
2 + 2 + 2
2 + 2 + 1 + 1
2 + 1 + 1 + 1 + 1
1 + 1 + 1 + 1 + 1 + 1
```

## Nonlocal Statements

**Effect**

- Future assignments to that name change its pre-existing binding in the **first non-local frame** of the current environment in which that name is bound.

**Python Particulars**

- Python pre-computes which frame contains each name before executing the body of a function.

- Within the body of a function, all instances of a name must refer to the same frame.

Lists, dictionaries, functions have **local state**.

`nonlocal` statement

```python
def make_withdraw(balance):
    """Return a withdraw function that draws down balance with each call."""
    def withdraw(amount):
        nonlocal balance                 # Declare the name "balance" nonlocal
        if amount > balance:
            return 'Insufficient funds'
        balance = balance - amount       # Re-bind the existing balance name
        return balance
    return withdraw
```

Only after a `nonlocal` statement can a function *change* the binding of names in these frames.

By introducing `nonlocal` statements, we have created a dual role for assignment statements. Either they change local bindings, or they change nonlocal bindings.

**Benefit**

Non-local assignment has given us the ability to maintain some state that is local to a function, inaccessible to the rest of the program.

**Cost**

It matters whether the instances are bound to the same function or different instances of that function.

## Mutable Functions

How to create mutable functions?

- Using non-local statements

```python
def make_withdraw(balance):
    """Return a withdraw function with a starting balance."""
    def withdraw(amount):
        nonlocal balance
        if amount > balance:
            return 'Insufficient funds'
        balance = balance - amount
        return balance
    return withdraw
```

- Mutable values can be changed without a nonlocal statement

```python
def make_withdraw_list(balance):
    b = [balance]
    def withdraw(amount):
        if amount > b[0]:
            return 'Insufficient funds'
        b[0] = b[0] - amount
        return b[0]
    return withdraw
```

**Referential Transparency**

- Expressions are referentially transparent if substituting an expression with its value does not change the meaning of a program.
- Mutation operations violate the condition of referential transparency because they do more than just return a value; **they change the environment**.

## Iterators

An ***iterator*** is an object that provides sequential access to values, one by one.

- An **iterable** value is any value that can be passed to `iter` to produce an iterator.
- An **iterator** is returned from `iter` and can be passed to `next`; all iterators are mutable.

The way that Python signals that there are no more values available is to raise a `StopIteration` exception when `next` is called. This exception can be handled using a `try` statement.

```python
>>> try:
        next(iterator)
    except StopIteration:
        print('No more values')
No more values
```

A **container** can provide an iterator that provides access to its elements in order.

**Built-in functions**

- `iter(iterable)`: Return an iterator over the elements of an iterable value
- `next(iterator)`: Return the next element in an iterator

## Dictionary Iteration

A dictionary, its keys, its values, and its items are all iterable values.

- The order of items in a dictionary is the order in which they were added (Python 3.6+)
- Historically, items appeared in an arbitrary order (Python 3.5 and earlier)

```python
>>> d = {'one': 1, 'two': 2, 'three': 3}
>>> d['zero'] = 0
```

iterate over keys

```python
>>> k = iter(d.keys()) # or iter(d)
>>> next(k)
'one'
>>> next(k)
'two'
>>> next(k)
'three'
>>> next(k)
'zero'
```

iterate over values

```python
v = iter(d.values())
>>> v = iter(d.values())
>>> next(v)
1
>>> next(v)
2
>>> next(v)
3
>>> next(v)
0
```

iterate over key-value pairs

```python
>>> i  = iter(d.items())
>>> next(i)
('one', 1)
>>> next(i)
('two', 2)
>>> next(i)
('three', 3)
>>> next(i)
('zero', 0)
```

Once the dictionary's size/shape/structure is changed, the iterator is invalid.

**For Statements**

```python
>>> r = range(3,6)
>>> for i in r:
...     print(i)
...
3
4
5

>>> ri = iter(r)
>>> next(ri)
3
>>> for i in ri:
...     print(i)
...
4
5
```

## Built-in Iterator Functions

Many built-in Python sequence operations return iterators that compute results lazily.

- `map(func, iterable)`: Iterate over func(x) for x in iterable
- `filter(func, iterable)`: Iterate over x in iterable if func(x)
- `zip(first_iter, second_iter)`: Iterate over co-indexed (x, y) pairs
- `reversed(sequence)`: Iterate over x in a sequence in reverse order

To view the contents of an iterator, place the resulting elements into a container

- `list(iterable)`: Create a list containing all x in iterable
- `tuple(iterable)`: Create a tuple containing all x in iterable
- `sorted(iterable)`: Create a sorted list containing all x in iterable

**Sequence**

```python
>>> bcd = ['b', 'c', 'd']
>>> [x.upper() for x in bcd]
['B', 'C', 'D']
```

**Iterator**

```python
>>> map(lambda x: x.upper(), bcd)
<map object at 0x00000244BFC9E608>
>>> m = map(lambda x: x.upper(), bcd)
>>> next(m)
'B'
>>> next(m)
'C'
>>> next(m)
'D'
```

compute results lazily

```python
>>> def double(x):
...     print('**', x, '=>', 2*x, '**')
...     return 2*x
...
>>> m = map(double, range(3,7))
>>> f = lambda y: y >= 10
>>> t = filter(f, m)
>>> next(t)
** 3 => 6 **
** 4 => 8 **
** 5 => 10 **
10
>>> next(t)
** 6 => 12 **
12
>>> list(t)
[]
```

exhaustive results when a list is called

```python
>>> list(filter(f, map(double, range(3,7))))
** 3 => 6 **
** 4 => 8 **
** 5 => 10 **
** 6 => 12 **
[10, 12]
```

comparing `zip(,)` with `items()`

```python
>>> d = {'a': 1, 'b': 2}

>>> items = iter(d.items())
>>> next(items)
('a', 1)
>>> next(items)
('b', 2)

>>> zip_items = zip(d.keys(), d.values())
>>> next(zip_items)
('a', 1)
>>> next(zip_items)
('b', 2)
```

## Generators

- A **generator function** is a function that **yields** values instead of **returning** them.
- A normal function **returns** once; a **generator function** can **yield** multiple times.
- A **generator** is an iterator created automatically by calling a **generator function**.
- When a **generator function** is called, it returns a **generator** that iterates over its yields

```python
>>> def plus_minus(x):
...     yield x
...     yield -x
...
>>> t = plus_minus(3)
>>> next(t)
3
>>> next(t)
-3
```

**Generators can Yield from Iterators**

A yield from statement yields all values from an iterator or iterable (Python 3.3).

`yield` vs `return`

While a return statement closes the current frame after the function exits, a yield statement causes the frame to be saved until the next time next is called, which allows the generator to automatically keep track of the iteration state.

`yield` vs `yield from`

**example 1**

```python
def countdown(k):
    """Count down to zero.
    >>> list(countdown(5))
    [5, 4, 3, 2, 1]
    """
    if k > 0:
        yield k
        yield from countdown(k-1)
```

otherwise, it will be

```python
def countdown(k):
    """Count down to zero.
    >>> list(countdown(5))
    [5, 4, 3, 2, 1]
    """
    if k > 0:
        yield k
        for x in countdown(k-1):
            yield x
```

**example 2**

```python
def substrings(s):
    """Yield all substrings of s.
    >>> list(substrings('tops'))
    ['t', 'to', 'top', 'tops', 'o', 'op', 'ops', 'p', 'ps', 's']
    """
    if s:
        yield from prefixes(s)
        yield from substrings(s[1:])
```

**example 3**

```python
def prefixes(s):
    """
    Yield all prefixes of s.
    >>> result = prefixes('dogs')
    >>> list(result)
    ['d', 'do', 'dog', 'dogs']
    """
    if s:
        yield from prefixes(s[:-1])
        yield s
```

`yield from`, a shortcut, can be replaced by a `for` statement.

```python
def prefixes(s):
    """
    Yield all prefixes of s.
    >>> result = prefixes('dogs')
    >>> list(result)
    ['d', 'do', 'dog', 'dogs']
    """
    if s:
        for x in prefixes(s[:-1]):
            yield x
        yield s
```

changing the order of `yield s` will get

```python
def prefixes2(s):
    """
    >>> result = prefixes2('dogs')
    >>> list(result)
    ['dogs', 'dog', 'do', 'd']
    """
    if s:
        for x in prefixes(s[:-1]):
            yield x
        yield s
```

