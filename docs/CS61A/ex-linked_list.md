# Linked List

A linked list is either empty or a first value and the rest of the linked list

- A linked list is a pair
- The first (zeroth) element is an attribute value
- The rest of the elements are stored in a linked list
- `Link.empty` - a class attribute represents an empty linked list

## Linked List Class

Linked list class: attributes are passed to `__init__`

```python
class Link:
    empty = () # tuple: some zero-length sequence
    
    def __init__(self, first, rest=empty):
        assert rest is Link.empty or isinstance(rest, Link)
        self.first = first
        self.rest = rest
    
    def __repr__(self):
        if self.rest:
            rest_repr = ', ' + repr(self.rest)
        else:
            rest_repr = ''
        return 'Link(' + repr(self.first) + rest_repr + ')'
    
    def __str__(self):
        string = '<'
        while self.rest is not Link.empty:
            string += str(self.first) + ' '
            self = self.rest
        return string + str(self.first) + '>'
    
    
    def __getitem__(self, i):
        if i == 0:
            return self.first
        else:
            return self.rest[i-1]
    
    def __len__(self):
        return 1 + len(self.rest)
```

## Extend Link

```python
def extend_link(s, t):
    """
    >>> extend_link(s, s)
    Link(3, Link(4, Link(5, Link(3, Link(4, Link(5))))))
    """
    if s is Link.empty:
        return t
    else:
        return Link(s.first, extend(s.rest, t))
```

## Range Link

```python
def range_link(start, end):
    """Return a Link containing consecutive integers from start to end.

    >>> range_link(3, 6)
    Link(3, Link(4, Link(5)))
    """
    if start >= end:
        return Link.empty
    else:
        return Link(start, range_link(start + 1, end))
```

## Map Link

```python
def map_link(f, s):
    """Return a Link that contains f(x) for each x in Link s.

    >>> map_link(square, range_link(3, 6))
    Link(9, Link(16, Link(25)))
    """
    if s is Link.empty:
        return s
    else:
        return Link(f(s.first), map_link(f, s.rest))
```

## Filter Link

```python
def filter_link(f, s):
    """Return a Link that contains only the elements x of Link s for which f(x)
    is a true value.

    >>> filter_link(odd, range_link(3, 6))
    Link(3, Link(5))
    """
    if s is Link.empty:
        return s
    filtered_rest = filter_link(f, s.rest)
    if f(s.first):
        return Link(s.first, filtered_rest)
    else:
        return filtered_rest
```

or

```python
def filter_link(lnk, fn):
    """
    >>> link = Link(1, Link(2, Link(3)))
    >>> g = filter_link(link, lambda x: x % 2 == 0)
    >>> next(g)
    2
    >>> next(g)
    StopIteration
    >>> list(filter_link(link, lambda x: x % 2 != 0))
    [1, 3]
    """
    while lnk is not Link.empty:
        if f(link.first):
            yield lnk.first
        lnk = lnk.rest
```

## Join Link

```python
def join_link(s, separator):
    """
    >>> join_link(s, ", ")
    '3, 4, 5'
    """
    if s is Link.empty:
        return ""
    elif s.rest is Link.empty:
        return str(s.first)
    else:
        return str(s.first) + separator + join_link(s.rest, separator)
```

## Add to an Ordered List

```python
def add(s, v):
    """
    Add v to an ordered list s with no repeats, returning modified s.
    If v is already in s, then don't modify s, but still return it
    >>> s = Link(1, Link(3, Link(5)))
    >>> add(s, 0)
    Link(0, Link(1, Link(3, Link(5))))
    >>> add(s, 3)
    Link(0, Link(1, Link(3, Link(5))))
    >>> add(s, 4)
    Link(0, Link(1, Link(3, Link(4, Link(5)))))
    >>> add(s, 6)
    Link(0, Link(1, Link(3, Link(4, Link(5, Link(6))))
    """
    if v < s.first:
        s.first, s.rest = v, Link(s.first, s.rest)
    elif v > s.first and empty(s.rest):
        s.rest = Link(v)
    elif v > s.first:
        add(s.rest, v)
    return s
```

## Sum Nums

```python
def sum_nums(lnk):
    """
    >>> a = Link(1, Link(6, Link(7)))
    >>> sum_nums(a)
    14
    """
    if lnk is Link.empty:
        return 0
    return lnk.first + sum_nums(lnk.rest)
```

## Multiply Link

```python
def multiply_lnks(lst_of_lnks):
    """
    >>> a = Link(2, Link(3, Link(5)))
    >>> b = Link(6, Link(4, Link(2)))
    >>> c = Link(4, Link(1, Link(0, Link(2))))
    >>> p = multiply_lnks([a, b, c])
    >>> p.first
    48
    >>> p.rest.first
    12
    >>> p.rest.rest.rest is Link.empty
    True
    """
    product = 1
    for lnk in lst_of_lnks:
        if lnk is Link.empty:
            return Link.empty
        product *= lnk.first
    lst_of_lnks_rest = [lnk.rest for lnk in lst_of_lnks]
    return Link(product, multiply_lnks(lst_of_lnks_rest))
```

## Convert to List

```python
def convert_link(link):
    """Takes a linked list and returns a Python list with the same elements.

    >>> link = Link(1, Link(2, Link(3, Link(4))))
    >>> convert_link(link)
    [1, 2, 3, 4]
    >>> convert_link(Link.empty)
    []
    """
    output_list = []
    while link != Link.empty:
        output_list.append(link.first)
        link = link.rest
    return output_list
```

recursive

```python
def convert_link(link):
    """Takes a linked list and returns a Python list with the same elements.

    >>> link = Link(1, Link(2, Link(3, Link(4))))
    >>> convert_link(link)
    [1, 2, 3, 4]
    >>> convert_link(Link.empty)
    []
    """
    if link == Link.empty:
      return []
    else:
      return [link.first] + convert_link(link.rest)
```

## Flip Two

```python
def flip_two(lnk):
    """
    >>> one_lnk = Link(1)
    >>> flip_two(one_lnk)
    >>> one_lnk
    Link(1)
    >>> lnk = Link(1, Link(2, Link(3, Link(4, Link(5)))))
    >>> flip_two(lnk)
    >>> lnk
    Link(2, Link(1, Link(4, Link(3, Link(5)))))
    """
    if lnk is Link.empty:
        return
    elif lnk.rest is Link.empty:
        return
    else:
        tmp_first = lnk.first
        tmp_rest = lnk.rest.first
        lnk.first = tmp_rest
        lnk.rest = Link(tmp_first, lnk.rest.rest)
        lnk.rest = Link(tmp_first, lnk.rest.rest)
        flip_two(lnk.rest.rest)
```

## Every Other Element (Q)

```python
def every_other(s):
    """Returns a new linked list that contains all the even-indiced elements of the original linked list
    (using 0-based indexing).
    """
    if s is Link.empty:
        return Link.empty
    elif s.rest is Link.empty:
        return s
    return Link(s.first, every_other(s.rest.rest))
```

in-place mutation?

```python
def every_other(s):
    """Mutates a linked list so that all the odd-indiced elements are removed
    (using 0-based indexing).

    >>> s = Link(1, Link(2, Link(3, Link(4))))
    >>> every_other(s)
    >>> s
    Link(1, Link(3))
    >>> odd_length = Link(5, Link(3, Link(1)))
    >>> every_other(odd_length)
    >>> odd_length
    Link(5, Link(1))
    >>> singleton = Link(4)
    >>> every_other(singleton)
    >>> singleton
    Link(4)
    """
    "*** YOUR CODE HERE ***"
```

## store digits

```python
def store_digits(n):
    """Stores the digits of a positive number n in a linked list.

    >>> s = store_digits(1)
    >>> s
    Link(1)
    >>> store_digits(2345)
    Link(2, Link(3, Link(4, Link(5))))
    >>> store_digits(876)
    Link(8, Link(7, Link(6)))
    >>> # a check for restricted functions
    >>> import inspect, re
    >>> cleaned = re.sub(r"#.*\\n", '', re.sub(r'"{3}[\s\S]*?"{3}', '', inspect.getsource(store_digits)))
    >>> print("Do not use str or reversed!") if any([r in cleaned for r in ["str", "reversed"]]) else None
    """
    l = Link.empty
    while n > 0:
        l = Link(n % 10, l)
        n = n // 10
```

## Sorted

```python
def ordered(s, key=lambda x: x):
    """Is Link s ordered?

    >>> ordered(Link(1, Link(3, Link(4))))
    True
    >>> ordered(Link(1, Link(4, Link(3))))
    False
    >>> ordered(Link(1, Link(-3, Link(4))))
    False
    >>> ordered(Link(1, Link(-3, Link(4))), key=abs)
    True
    >>> ordered(Link(-4, Link(-1, Link(3))))
    True
    >>> ordered(Link(-4, Link(-1, Link(3))), key=abs)
    False
    """
    if s is Link.empty or s.rest is Link.empty:
        return True
    elif key(s.first) > key(s.rest.first):
        return False
    else:
        return ordered(s.rest)
```

## Merge

```python
def merge(s, t):
    """Return a sorted Link containing the elements of sorted s & t.

    >>> a = Link(1, Link(5))
    >>> b = Link(1, Link(4))
    >>> merge(a, b)
    Link(1, Link(1, Link(4, Link(5))))
    >>> a
    Link(1, Link(5))
    >>> b
    Link(1, Link(4))
    """
    if s is Link.empty:
        return t
    elif t is Link.empty:
        return s
    elif s.first <= t.first:
        return Link(s.first, merge(s.rest, t))
    else:
        return Link(t.first, merge(s, t.rest))
```

in-place

```python
def merge_in_place(s, t):
    """Return a sorted Link containing the elements of sorted s & t.

    >>> a = Link(1, Link(5))
    >>> b = Link(1, Link(4))
    >>> merge_in_place(a, b)
    Link(1, Link(1, Link(4, Link(5))))
    >>> a
    Link(1, Link(1, Link(4, Link(5))))
    >>> b
    Link(1, Link(4, Link(5)))
    """
    if s is Link.empty:
        return t
    elif t is Link.empty:
        return s
    elif s.first <= t.first:
        s.rest = merge_in_place(s.rest, t)
        return s
    else:
        t.rest = merge_in_place(s, t.rest)
        return t
```

## Partition (TBD)

```python

```

## Has Cycle

```python
def has_cycle(link):
    """Return whether link contains a cycle.

    >>> s = Link(1, Link(2, Link(3)))
    >>> s.rest.rest.rest = s
    >>> has_cycle(s)
    True
    >>> t = Link(1, Link(2, Link(3)))
    >>> has_cycle(t)
    False
    >>> u = Link(2, Link(2, Link(2)))
    >>> has_cycle(u)
    False
    """
    links = []
    while link is not Link.empty:
        if link in links:
            return True
        links.append(link)
        link = link.rest
    return False
```

constant space

```python
def has_cycle_constant(link):
    """Return whether link contains a cycle.
    (with constant space)

    >>> s = Link(1, Link(2, Link(3)))
    >>> s.rest.rest.rest = s
    >>> has_cycle_constant(s)
    True
    >>> t = Link(1, Link(2, Link(3)))
    >>> has_cycle_constant(t)
    False
    """
    if link is Link.empty:
        return False
    slow, fast = link, link.rest
    while fast is not Link.empty:
        if fast.rest == Link.empty:
            return False
        elif fast is slow or fast.rest is slow:
            return True
        else:
            slow, fast = slow.rest, fast.rest.rest
    return False
```

## Deep Linked List Length

A linked list that contains one or more linked lists as elements is called a *deep* linked list. Write a function `deep_len` that takes in a (possibly deep) linked list and returns the *deep length* of that linked list. The deep length of a linked list is the total number of non-link elements in the list, as well as the total number of elements contained in all contained lists.

```python
def deep_len(lnk):
    """ Returns the deep length of a possibly deep linked list.

    >>> deep_len(Link(1, Link(2, Link(3))))
    3
    >>> deep_len(Link(Link(1, Link(2)), Link(3, Link(4))))
    4
    >>> levels = Link(Link(Link(1, Link(2)), \
            Link(3)), Link(Link(4), Link(5)))
    >>> print(levels)
    <<<1 2> 3> <4> 5>
    >>> deep_len(levels)
    5
    """
    if lnk is Link.empty:
        return 0
    elif isinstance(lnk.first, Link):
        return deep_len(lnk.first) + deep_len(lnk.rest)
    else:
        return 1 + deep_len(lnk.rest)
```

## Make to String

```python
def make_to_string(front, mid, back, empty_repr):
    """ Returns a function that turns linked lists to strings.

    >>> kevins_to_string = make_to_string("[", "|-]-->", "", "[]")
    >>> jerrys_to_string = make_to_string("(", " . ", ")", "()")
    >>> lst = Link(1, Link(2, Link(3, Link(4))))
    >>> kevins_to_string(lst)
    '[1|-]-->[2|-]-->[3|-]-->[4|-]-->[]'
    >>> kevins_to_string(Link.empty)
    '[]'
    >>> jerrys_to_string(lst)
    '(1 . (2 . (3 . (4 . ()))))'
    >>> jerrys_to_string(Link.empty)
    '()'
    """
    def printer(lnk):
        if lnk is Link.empty:
            return empty_repr
        else:
            return front + str(lnk.first) + mid + printer(lnk.rest) + back
    return printer
```

