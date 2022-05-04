# Sequence

## list basics

append

```python
>>> [2, 7] + digits * 2
[2, 7, 1, 8, 2, 8, 1, 8, 2, 8]
```

make a copy

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

## list comprehension

```python
>>> odds = [1, 3, 5, 7, 9]

>>> [x+1 for x in odds]
[2, 4, 6, 8, 10]

>>> [x for x in odds if 25 % x == 0]
[1, 5]
```

## even weighted

```python
def even_weighted(s):
    """
    >>> x = [1, 2, 3, 4, 5, 6]
    >>> even_weighted(x)
    [0, 6, 20]
    """
    return [s[i] * i for i in range(len(s)) if i % 2 == 0]
```

## closest in the list

the number in the list, whose square is closest to 24

```python
>>> min([3, 2, 5, 6], key=lambda x: abs(x*x - 24))
5
```

call another function

```python
>>> def f(x, y):
...     return abs(x * x - y)
...
>>> min([3, 2, 5, 6], key=lambda x: f(x, 24))
5
```

messy way

```python
>>> f = lambda x: abs(x*x - 24)
>>> [f(x) for x in [3,2,5,6]]
[15, 20, 1, 12]

>>> [[f(x),x] for x in [3,2,5,6]]
[[15, 3], [20, 2], [1, 5], [12, 6]]

>>> min([[f(x),x] for x in [3,2,5,6]])
[1, 5]
>>> min([[f(x),x] for x in [3,2,5,6]])[1]
5
```

## couple list

```python
def couple(s, t):
    """Return a list of two-element lists in which the i-th element is [s[i], t[i]]."""
    assert len(s) == len(t)
    return [[s[i], t[i]] for i in range(len(s))]
```

## add matrices

```python
def add_matrices(x, y):
    """
    >>> matrix1 = [[1, 3],
    ...            [2, 0]]
    >>> matrix2 = [[-3, 0],
    ...            [1, 2]]
    >>> add_matrices(matrix1, matrix2)
    [[-2, 3], [3, 2]]
    """
    return [[x[i][j] + y[i][j] for j in range(len(x[0]))]
                               for i in range(len(x))]
```

## riffle deck

```python
def riffle(deck):
    """Produces a single, perfect riffle shuffle of DECK, consisting of
    DECK[0], DECK[M], DECK[1], DECK[M+1], ... where M is position of the
    second half of the deck.  Assume that len(DECK) is even.
    >>> riffle([3, 4, 5, 6])
    [3, 5, 4, 6]
    >>> riffle(range(20))
    [0, 10, 1, 11, 2, 12, 3, 13, 4, 14, 5, 15, 6, 16, 7, 17, 8, 18, 9, 19]
    """
    return [ deck[(i % 2) * len(deck)//2 + i // 2] for i in range(len(deck)) ]
```

## if this not that

```python
def if_this_not_that(i_list, this):
    """Define a function which takes a list of integers `i_list` and an integer
    `this`. For each element in `i_list`, print the element if it is larger
    than `this`; otherwise, print the word "that".

    >>> original_list = [1, 2, 3, 4, 5]
    >>> if_this_not_that(original_list, 3)
    that
    that
    that
    4
    5
    """
    [print(i_list[i]) if i_list[i] > this else print('that') for i in range(len(i_list))]
```

## insert item after element

```python
def insert_items(lst, entry, elem):
    """
    >>> test_lst = [1, 5, 8, 5, 2, 3]
    >>> new_lst = insert_items(test_lst, 5, 7)
    >>> new_lst
    [1, 5, 7, 8, 5, 7, 2, 3]
    >>> large_lst = [1, 4, 8]
    >>> large_lst2 = insert_items(large_lst, 4, 4)
    >>> large_lst2
    [1, 4, 4, 8]
    >>> large_lst3 = insert_items(large_lst2, 4, 6)
    >>> large_lst3
    [1, 4, 6, 4, 6, 8]
    >>> large_lst3 is large_lst
    True
    """
    "*** YOUR CODE HERE ***"
    # for i in range(len(lst)):
    #     if lst[i] == entry:
    #         lst.insert(i+1, elem)
    # return lst
    # does not satisfy large_lst2 = insert_items(large_lst, 4, 4)
    index_lst = []
    for i in range(len(lst)):
        if lst[i] == entry:
            index_lst.append(i)
    for i1, i2 in zip(index_lst, range(len(index_lst))):
        lst.insert(i1+i2+1, elem)
    return lst
```

## countdown

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

## prefix of string

`yield` vs `yield from`

```python
def prefixes(s):
    """
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
    >>> result = prefixes('dogs')
    >>> list(result)
    ['d', 'do', 'dog', 'dogs']
    """
    if s:
        for x in prefixes(s[:-1]):
            yield x
        yield s
```

changing the order of `yield s`

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

## merge (TBD)

```python

```

## flatten (TBD)

```python

```

## permutation

```python
def permutations(seq):
    """Generates all permutations of the given sequence. Each permutation is a
    list of the elements in SEQ in a different order. The permutations may be
    yielded in any order.
    >>> perms = permutations([100])
    >>> type(perms)
    <class 'generator'>
    >>> next(perms)
    [100]
    >>> try: #this piece of code prints "No more permutations!" if calling next would cause an error
    ...     next(perms)
    ... except StopIteration:
    ...     print('No more permutations!')
    No more permutations!
    >>> sorted(permutations([1, 2, 3])) # Returns a sorted list containing elements of the generator
    [[1, 2, 3], [1, 3, 2], [2, 1, 3], [2, 3, 1], [3, 1, 2], [3, 2, 1]]
    >>> sorted(permutations((10, 20, 30)))
    [[10, 20, 30], [10, 30, 20], [20, 10, 30], [20, 30, 10], [30, 10, 20], [30, 20, 10]]
    >>> sorted(permutations("ab"))
    [['a', 'b'], ['b', 'a']]
    """
    if not seq:
        yield []
    else:
        for perm in permutations([x for x in seq if x != seq[0]]):
            for k in range(len(perm) + 1):
                yield perm[:k] + [seq[0]] + perm[k:]

```

or

```python
def permutations(s):
    """
    yield permutations of list s
    >>> list(permutations([1, 2, 3]))
    [[1, 2, 3], [1, 3, 2], [2, 1, 3], [2, 3, 1], [3, 1, 2], [3, 2, 1]]
    """
    if len(s) == 0:
        yield []
    for i in range(len(s)):
        start = s[i] # 2
        rest = [s[j] for j in range(len(s)) if j != i] # [1, 3]
        for rest_permutation in permutations(rest):
            yield [start] + rest_permutation # [2, 1, 3] or [2, 3, 1]
```

or 

```python
def permutations(s):
    """
    yield permutations of list s
    >>> list(permutations([1, 2, 3]))
    [[1, 2, 3], [2, 1, 3], [2, 3, 1], [1, 3, 2], [3, 1, 2], [3, 2, 1]]
    """
    if len(s) == 0:
        yield []
    else:
        first = s[0] # 1
        rest = s[1:] # [2, 3]
        for rest_permutation in permutations(rest):
            for i in range(len(s)):
                result = list(rest_permutation)
                result.insert(i, first)
                yield result
```

modification to work with string

```python
def permutations(s):
    """
    yield permutations of list s
    >>> list(permutations('abc'))
    ['abc', 'bac', 'bca', 'acb', 'cab', 'cba']
    """
    if len(s) == 0:
        yield ''
    else:
        first = s[0]
        rest = s[1:]
        for rest_permutations in permutations(rest):
            for i in range(len(s)):
                yield rest_permutations[:i] + first + rest_permutations[i:]
```

## remainder generator

```python
def naturals():
    """A generator function that yields the infinite sequence of natural
    numbers, starting at 1.

    >>> m = naturals()
    >>> type(m)
    <class 'generator'>
    >>> [next(m) for _ in range(10)]
    [1, 2, 3, 4, 5, 6, 7, 8, 9, 10]
    """
    i = 1
    while True:
        yield i
        i += 1
```

```python
def remainders_generator(m):
    """
    Yields m generators. The ith yielded generator yields natural numbers whose
    remainder is i when divided by m.

    >>> import types
    >>> [isinstance(gen, types.GeneratorType) for gen in remainders_generator(5)]
    [True, True, True, True, True]
    >>> remainders_four = remainders_generator(4)
    >>> for i in range(4):
    ...     print("First 3 natural numbers with remainder {0} when divided by 4:".format(i))
    ...     gen = next(remainders_four)
    ...     for _ in range(3):
    ...         print(next(gen))
    First 3 natural numbers with remainder 0 when divided by 4:
    4
    8
    12
    First 3 natural numbers with remainder 1 when divided by 4:
    1
    5
    9
    First 3 natural numbers with remainder 2 when divided by 4:
    2
    6
    10
    First 3 natural numbers with remainder 3 when divided by 4:
    3
    7
    11
    """
    def generator(n):
        while True:
            yield n
            n += m
    for n in naturals():
        yield generator(n)
```

## filter, map on range

```python
square, odd = lambda x: x * x, lambda x: x % 2 == 1
list(map(square, filter(odd, range(1,6))))
# [1, 9, 25]
```

## scale iterable

```python
def scale(it, multiplier):
    """Yield elements of the iterable it scaled by a number multiplier.

    >>> m = scale([1, 5, 2], 5)
    >>> type(m)
    <class 'generator'>
    >>> list(m)
    [5, 25, 10]

    >>> m = scale(naturals(), 2)
    >>> [next(m) for _ in range(5)]
    [2, 4, 6, 8, 10]
    """
    yield from map(lambda x: x * multiplier, it)
```

## min abs indices

```python
def min_abs_indices(s):
    """
    Indices of all elements in list that equal to the smallest absolute value.

    >>> min_abs_indices([-4, -3, -2, 3, 2, 4])
    [2, 4]
    >>> min_abs_indices([1, 2, 3, 4, 5])
    [0]
    """
    min_abs = min(map(abs, s))
    return [i for i in range(len(s)) if abs(s[i]) == min_abs]
```

## largest adjacent sum

```python
def largest_adj_sum(s):
    """
    Largest sum of the two adjacent elements in a list s.
    >>> largest_adj_sum([-4, -3, -2, -3, 2, 4])
    6
    >>> largest_adj_sum([-4, 3, -2, -3, 2, -4])
    1
    """
    return max([s[i] + s[i+1] for i in range(len(s)-1)])

```

zip

```python
def largest_adj_sum(s):
    """
    Largest sum of the two adjacent elements in a list s.
    >>> largest_adj_sum([-4, -3, -2, -3, 2, 4])
    6
    >>> largest_adj_sum([-4, 3, -2, -3, 2, -4])
    1
    """
    return max([a + b for a, b in zip(s[:-1], s[1:])])
```

## all have an equal

```python
def all_have_an_equal(s):
    """
    Does every element equal to some other element in s?

    >>> all_have_an_equal([-4, -3, -2, 3, 2, 4])
    False
    >>> all_have_an_equal([4, 3, 2, 3, 2, 4])
    True
    """
    return all([s[i] in s[:i] + s[i+i:] for i in range(len(s))])
```

alternatively

```python
def all_have_an_equal(s):
    """
    Does every element equal to some other element in s?

    >>> all_have_an_equal([-4, -3, -2, 3, 2, 4])
    False
    >>> all_have_an_equal([4, 3, 2, 3, 2, 4])
    True
    """
    return min([sum(1 for y in s if y == x) for x in s]) > 1
```

alternatively

```python
def all_have_an_equal(s):
    """
    Does every element equal to some other element in s?

    >>> all_have_an_equal([-4, -3, -2, 3, 2, 4])
    False
    >>> all_have_an_equal([4, 3, 2, 3, 2, 4])
    True
    """
    return min([s.count(x) for x in s]) > 1
```

## trade equal prefix (TBD)

```python

```

## shuffle cards

```python
def card(n):
    """Return the playing card numeral as a string for a positive n <= 13."""
    assert type(n) == int and n > 0 and n <= 13, "Bad card n"
    specials = {1: 'A', 11: 'J', 12: 'Q', 13: 'K'}
    return specials.get(n, str(n))

def shuffle(cards):
    """Return a shuffled list that interleaves the two halves of cards.

    >>> shuffle(range(6))
    [0, 3, 1, 4, 2, 5]
    >>> suits = ['♡', '♢', '♤', '♧']
    >>> cards = [card(n) + suit for n in range(1,14) for suit in suits]
    >>> cards[:12]
    ['A♡', 'A♢', 'A♤', 'A♧', '2♡', '2♢', '2♤', '2♧', '3♡', '3♢', '3♤', '3♧']
    >>> cards[26:30]
    ['7♤', '7♧', '8♡', '8♢']
    >>> shuffle(cards)[:12]
    ['A♡', '7♤', 'A♢', '7♧', 'A♤', '8♡', 'A♧', '8♢', '2♡', '8♤', '2♢', '8♧']
    >>> shuffle(shuffle(cards))[:12]
    ['A♡', '4♢', '7♤', '10♧', 'A♢', '4♤', '7♧', 'J♡', 'A♤', '4♧', '8♡', 'J♢']
    >>> cards[:12]  # Should not be changed
    ['A♡', 'A♢', 'A♤', 'A♧', '2♡', '2♢', '2♤', '2♧', '3♡', '3♢', '3♤', '3♧']
    """
    assert len(cards) % 2 == 0, 'len(cards) must be even'
    half = len(cards) // 2
    shuffled = []
    for i in range(half):
        shuffled += [cards[i], cards[half + i]]
    return shuffled
```

# Dictionary

## dict comprehension

```python
>>> squares = {x:x*x for x in range(10)}
>>> squares
{0: 0, 1: 1, 2: 4, 3: 9, 4: 16, 5: 25, 6: 36, 7: 49, 8: 64, 9: 81}
>>> squares[7]
49
```

## digit dict

```python
def digit_dict(s):
    """
    Map each digit d to the lists of elements in s that end with d.

    >>> digit_dict([5, 8, 13, 21, 34, 55, 89])
    {1: [21], [3]: [14],}
    """
    last_digits = [x % 10 for x in s]
    return {d: [x for x in s if x % 10 == d] for d in range(len(s)) if d in last_digits}
```

my solution

```python
def digit_dict(s):
    """
    Map each digit d to the lists of elements in s that end with d.
    """
    digit_dict = {}
    for d in s:
        last_digit = d % 10
        if last_digit not in digit_dict:
            digit_dict[last_digit] = [d]
        else:
            digit_dict[last_digit].append(d)
    return digit_dict
```

## successor table

```python
def build_successors_table(tokens):
    """Return a dictionary: keys are words; values are lists of successors.

    >>> text = ['We', 'came', 'to', 'investigate', ',', 'catch', 'bad', 'guys', 'and', 'to', 'eat', 'pie', '.']
    >>> table = build_successors_table(text)
    >>> sorted(table)
    [',', '.', 'We', 'and', 'bad', 'came', 'catch', 'eat', 'guys', 'investigate', 'pie', 'to']
    >>> table['to']
    ['investigate', 'eat']
    >>> table['pie']
    ['.']
    >>> table['.']
    ['We']
    """
    table = {}
    prev = '.'
    for word in tokens:
        if prev not in table:
            table[prev] = []
        table[prev] += [word]
        prev = word
    return table
```

## construct sentence

```python
def construct_sent(word, table):
    """Prints a random sentence starting with word, sampling from
    table.

    >>> table = {'Wow': ['!'], 'Sentences': ['are'], 'are': ['cool'], 'cool': ['.']}
    >>> construct_sent('Wow', table)
    'Wow!'
    >>> construct_sent('Sentences', table)
    'Sentences are cool.'
    """
    import random
    result = ''
    while word not in ['.', '!', '?']:
        result = result + ' ' + word
        word = random.choice(table[word])
    return result.strip() + word
```

## decrypt alanturing

```python
def decrypt(s , d):
    """ List all possible decoded strings of s.
    >>> codes = {
        'alan': 'spooky',
        'al': 'drink',
        'antu': 'your',
        'turing': 'ghosts',
        'tur': 'scary',
        'ing': 'skeletons',
        'ring': 'ovaltine'
        }
    >>> decrypt('alanturing', codes)
    ['drink your ovaltine', 'spooky ghosts', 'spooky scary skeletons']
    """
    if s == '':
        return []
    messeges = []
    if s in d:
        messeges.append(d[s])
    for k in range(1, len(s)+1):
        first, suffix = s[:k], s[k:]
        if first in d:
            for rest in decrypt(suffix, d):
                messeges.append(d[first] + ' ' + rest)
    return messeges
```



