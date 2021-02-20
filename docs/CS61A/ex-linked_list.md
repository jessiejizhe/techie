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

## Partition (TBD)


