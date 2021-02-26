# Recursions

## sum(n)

built-in `sum()` for a list

```python
>>> sum
<built-in function sum>
>>> sum([3,4,5])
12
>>> 0 + 3 + 4 + 5
12
>>> sum([])
0
>>> [3] + [4, 5][3, 4, 5]
```

takes input n and returns the sum of the first n integers

sum(5) returns 1+2+3+4+5

```python
def sum_itr(n):
    i, total = 0, 0
    while i < n:
        i += 1
        total += i
    return total

def sum_itr(n):
    sum = 0
    for i in range(0, n+1):
        sum = sum + i
    return sum

def sum(n):
    if n == 0:
        return 0
    else:
        return n + sum(n-1)
```

## sum digits

```python
def sum_digits(y):
    """Sum all the digits of y.

    >>> sum_digits(10) # 1 + 0 = 1
    1
    >>> sum_digits(4224) # 4 + 2 + 2 + 4 = 12
    12
    >>> sum_digits(1234567890)
    45
    >>> a = sum_digits(123) # make sure that you are using return rather than print
    >>> a
    6
    """
    if y <= 0:
        return "need to input a nonnegative number"
    else:
        sum = 0
        while y > 0:
            sum = sum + y % 10
            y = y // 10
        return sum
```

recursively

```python
def sum_digits(n):
    """Return the sum of the digits of positive integer n."""
    if n < 10:
        return n
    else:
        all_but_last, last = n // 10, n % 10
        return sum_digits(all_but_last) + last
```

## sum of first n terms

```python
def summation(n, term):

    """Return the sum of the first n terms in the sequence defined by term.
    Implement using recursion!

    >>> summation(5, lambda x: x * x * x) # 1^3 + 2^3 + 3^3 + 4^3 + 5^3
    225
    >>> summation(9, lambda x: x + 1) # 2 + 3 + 4 + 5 + 6 + 7 + 8 + 9 + 10
    54
    >>> summation(5, lambda x: 2**x) # 2^1 + 2^2 + 2^3 + 2^4 + 2^5
    62
    """
    assert n >= 1
    if n == 1:
        return term(n)
    else:
        return term(n) + summation(n-1, term)
```

## cascade

```python
def cascade(n):
    if n < 10:
        print(n)
    else:
        print(n)
        cascade(n//10)
        print(n)
```

alternatively

```python
def cascade(n):
    print(n)
    if n > 10:
        cascade(n//10)
        print(n)
```

results

```python
>>> print(123)
123
12
1
12
123
```

## inverse cascade

hint

- `grow` prints out: 1, 12, 123
- `print` prints out 123
- `shrink` prints out 123, 12, 12

```python
def inverse_cascade(n):
    grow(n)
    print(n)
    shrink(n)

def f_then_g(f, g, n):
    if n:
        f(n)
        g(n)

grow = lambda n: f_then_g()
shrink = lambda n: f_then_g()
```

solution

```python
grow = lambda n: f_then_g(grow, print, n//10)
shrink = lambda n: f_then_g(print, shrink, n//10)
```

## hailstone

Douglas Hofstadter's Pulitzer-prize-winning book, *Gödel, Escher, Bach*, poses the following mathematical puzzle.

1. Pick a positive integer `n` as the start.
2. If `n` is even, divide it by 2.
3. If `n` is odd, multiply it by 3 and add 1.
4. Continue this process until `n` is 1.

```python
def hailstone(n):
    count = 1
    if n <= 0:
        print('pick a positive number')
        return 0
    else:
        print(n)
        while n != 1:
            if n % 2 == 0:
                n = n / 2
            else:
                n = n * 3 + 1
            print(n)
            count += 1
    return count
```

recursive

```python
def hailstone(n):
    print(n)
    if n == 1:
        return n
    elif n % 2 == 0:
        return 1 + hailstone(n // 2)
    else:
        return 1 + hailstone(3 * n + 1)
```

## merge digits

take s numbers with digits in decreasing order

returns a single number with all of the digits of the two in decreasing order

```python
def merge(n1, n2):
    if n1 == 0:
        return n2
    elif n2 == 0:
        return n1
    elif n1 % 10 < n2 % 10:
        return merge(n1 // 10, n2) * 10 + n1 % 10
    else:
        return merge(n1, n2 // 10) * 10 + n2 % 10
```

## missing digits

```python
def missing_digits(n):
    """Given a number a that is in sorted, increasing order,
    return the number of missing digits in n. A missing digit is
    a number between the first and last digit of a that is not in n.
    >>> missing_digits(1248) # 3, 5, 6, 7
    4
    >>> missing_digits(1122) # No missing numbers
    0
    >>> missing_digits(123456) # No missing numbers
    0
    >>> missing_digits(3558) # 4, 6, 7
    3
    >>> missing_digits(35578) # 4, 6
    2
    >>> missing_digits(12456) # 3
    1
    >>> missing_digits(16789) # 2, 3, 4, 5
    4
    >>> missing_digits(19) # 2, 3, 4, 5, 6, 7, 8
    7
    >>> missing_digits(4) # No missing numbers between 4 and 4
    0
    """
    def missing_digits(n):
    if n < 10:
        return 0
    else:
        last_digit = n % 10
        second_to_last_digit = n // 10 % 10
        if last_digit == second_to_last_digit:
            return missing_digits(n // 10)
        else:
            return (last_digit - second_to_last_digit - 1) + missing_digits(n // 10)

```

## skip add

```python
def skip_add(n):
    """ Takes a number n and returns n + n-2 + n-4 + n-6 + ... + 0.

    >>> skip_add(5)  # 5 + 3 + 1 + 0
    9
    >>> skip_add(10) # 10 + 8 + 6 + 4 + 2 + 0
    30
    """
    if n <= 0:
        return 0
    else:
        return n + skip_add(n-2)
```

## factorial

```python
def fact(n):
    if n == 0:
        return 1
    else:
        return n * fact(n-1)

def fact_iter(n):
    total, k = 1, 1
    while k <= n:
        total, k = total * k, k + 1
        return total
```

## prime number

while loop

```python
def is_prime(n):
    """
    >>> is_prime(10)
    False
    >>> is_prime(7)
    True
    """
    if n == 1:
        return False
    else:
        i = 2
        while i < n:
            if n % i == 0:
                return False
                break
            else:
                i += 1
                return True
```

for loop

```python
def is_prime(n):
    """
    >>> is_prime(10)
    False
    >>> is_prime(7)
    True
    """
    if n == 1:
        return False
    else:
        for i in range(2, n):
            if n % i == 0:
                return False
                break
            else:
                return True
```

recursive

```python
def is_prime(n):
    def prime_helper(i):
        if i == n:
            return True
        elif n % i == 0 or n == 1:
            return False
        else:
            return prime_helper(i+1)
    return prime_helper(2)  # i starts from 2
```

## pingpong

```python
def pingpong(n):
    """Return the nth element of the ping-pong sequence.

    >>> pingpong(8)
    8
    >>> pingpong(10)
    6
    >>> pingpong(15)
    1
    >>> pingpong(21)
    -1
    >>> pingpong(22)
    -2
    >>> pingpong(30)
    -2
    >>> pingpong(68)
    0
    >>> pingpong(69)
    -1
    >>> pingpong(80)
    0
    >>> pingpong(81)
    1
    >>> pingpong(82)
    0
    >>> pingpong(100)
    -6
    """
    def pingpong_helper(x, direction, base):
        if x == n:
            return base + direction
        elif x % 8 == 0 or num_eights(x)>0:
            return pingpong_helper(x+1, -direction, base + direction)
        else:
            return pingpong_helper(x+1, direction, base + direction)
    return pingpong_helper(1, 1, 0)

```

iterative

```python
def pingpong_itr(n):
    i, value, direction = 1, 1, 1
    while i < n:
        if i % 8 == 0 or num_eights(i)>0:
            direction = -direction
        value += direction
        i += 1
    return value
```

## count coins

```python
def next_largest_coin(coin):
    """Return the next coin. 
    >>> next_largest_coin(1)
    5
    >>> next_largest_coin(5)
    10
    >>> next_largest_coin(10)
    25
    >>> next_largest_coin(2) # Other values return None
    """
    if coin == 1:
        return 5
    elif coin == 5:
        return 10
    elif coin == 10:
        return 25


def count_coins(total):
    """Return the number of ways to make change for total using coins of value of 1, 5, 10, 25.
    >>> count_coins(15)
    6
    >>> count_coins(10)
    4
    >>> count_coins(20)
    9
    >>> count_coins(100) # How many ways to make change for a dollar?
    242
    """
    def count_coins(total):
        def coin_helper(total, coin):
            if total < 0:
                return 0
            elif total == 0:
                return 1
            elif coin is None:
                return 0
            else:
                cur_coin = coin_helper(total - coin, coin)
                next_coin = coin_helper(total, next_largest_coin(coin))
                return cur_coin + next_coin
        return coin_helper(total, 1)

# doesn't work for count_coins(100), why?
```

## count paths in grid

```python
def paths(m, n):
    """Return the number of paths from one corner of an
    M by N grid to the opposite corner.

    >>> paths(2, 2)
    2
    >>> paths(5, 7)
    210
    >>> paths(117, 1)
    1
    >>> paths(1, 157)
    1
    """
    if m == 1 and n == 1: # start
        return 1
    elif m == 1: # left edge
        return paths(m, n-1)
    elif n == 1: # bottom edge
        return paths(m-1, n)
    else:
        return paths(m, n-1) + paths(m-1, n)
```

## count stairs for n steps

take 1 or 2 steps each time

```python
# my sln
def count_stair_ways(n):
    if n == 1:
        return 1
    elif n == 2:
        return 2
    else:
        return count_stair_ways(n-1) + count_stair_ways(n-2)

# don't need else
def count_stair_ways(n):
    if n == 1:
        return 1
    elif n == 2:
        return 2
    return count_stair_ways(n-1) + count_stair_ways(n-2)
```

take up to k steps at a time

```python
def count_k(n, k):
    """
    >>> count_k(3, 3) # 3, 2 + 1, 1 + 2, 1 + 1 + 1
    4
    >>> count_k(4, 4)
    8
    >>> count_k(10, 3)
    274
    >>> count_k(300, 1) # Only one step at a time
    1
    """
    if n == 0:
        return 1
    elif n < 0:
        return 0
    else:
        # count_k(n-1, k) + count_k(n-2, k) + ... + count_k(n-k, k)
        total = 0
        i = 1
        while i <= k:
            total += count_k(n-i, k)
            i += 1
        return total
```

## max product of nonconsecutive elements

```python
def max_product(s):
    """Return the maximum product that can be formed using non-consecutive
    elements of s.
    >>> max_product([10,3,1,9,2]) # 10 * 9
    90
    >>> max_product([5,10,5,10,5]) # 5 * 5 * 5
    125
    >>> max_product([])
    1
    """
    if len(s) == 0:
        return 1
    elif len(s) == 1:
        return s[0]
    else:
        return max(max_product(s[1:]), s[0] * max_product(s[2:]))
```

## insert into all

```python
def insert_into_all(item, nested_list):
    """Assuming that nested_list is a list of lists, return a new list
    consisting of all the lists in nested_list, but with item added to
    the front of each.

    >>> nl = [[], [1, 2], [3]]
    >>> insert_into_all(0, nl)
    [[0], [0, 1, 2], [0, 3]]
    """
    return [[item] + i for i in nested_list]
```

## subsequence

```python
def subseqs(s):
    """
    >>> seqs = subseqs([1, 2, 3])
    >>> sorted(seqs)
    [[], [1], [1, 2], [1, 2, 3], [1, 3], [2], [2, 3], [3]]
    >>> subseqs([])
    [[]]
    """
    if len(s) == 0:
        return [[]]
    else:
        exclude_first = subseqs(s[1:])
        include_first = [[s[0]] + e for e in exclude_first]
        # include_first = insert_into_all([s[0]], exclude_first)
        return exclude_first + include_first
```

## increasing subsequence

```python
def inc_subseqs(s):
    """
    >>> seqs = inc_subseqs([1, 3, 2])
    >>> sorted(seqs)
    [[], [1], [1, 2], [1, 3], [2], [3]]
    >>> inc_subseqs([])
    [[]]
    >>> seqs2 = inc_subseqs([1, 1, 2])
    >>> sorted(seqs2)
    [[], [1], [1], [1, 1], [1, 1, 2], [1, 2], [1, 2], [2]]
    """
    def subseq_helper(s, prev):
        if not s:
            return [[]]
        elif s[0] < prev:
            return subseq_helper(s[1:], prev)
        else:
            include = subseq_helper(s[1:], s[0])
            exclude = subseq_helper(s[1:], prev)
            return insert_into_all(s[0], include) + exclude
    return subseq_helper(s, 0)
```

## max subsequence

```python
def max_subseq(n, t):
    """
    Return the maximum subsequence of length at most t that can be found in the given number n.
    For example, for n = 20125 and t = 3, we have that the subsequences are
        2
        0
        1
        2
        5
        20
        21
        22
        25
        01
        02
        05
        12
        15
        25
        201
        202
        205
        212
        215
        225
        012
        015
        025
        125
    and of these, the maxumum number is 225, so our answer is 225.

    >>> max_subseq(20125, 3)
    225
    >>> max_subseq(20125, 5)
    20125
    >>> max_subseq(20125, 6) # note that 20125 == 020125
    20125
    >>> max_subseq(12345, 3)
    345
    >>> max_subseq(12345, 0) # 0 is of length 0
    0
    >>> max_subseq(12345, 1)
    5
    """
    if t == 0 or n == 0:
        return 0
    last_digit = n % 10
    rest = n // 10
    keep_last_digit = max_subseq(rest, t-1) * 10 + last_digit
    drop_last_digit = max_subseq(rest, t)
    return max(keep_last_digit, drop_last_digit)
```

## add characters

```python
def add_chars(w1, w2):
    """
    Return a string containing the characters you need to add to w1 to get w2.

    You may assume that w1 is a subsequence of w2.

    >>> add_chars("owl", "howl")
    'h'
    >>> add_chars("want", "wanton")
    'on'
    >>> add_chars("rat", "radiate")
    'diae'
    >>> add_chars("a", "prepare")
    'prepre'
    >>> add_chars("resin", "recursion")
    'curo'
    >>> add_chars("fin", "effusion")
    'efuso'
    >>> add_chars("coy", "cacophony")
    'acphon'
    """
    if len(w1) == 0:
        return w2
    if w1[0] == w2[0]:
        return add_chars(w1[1:], w2[1:])
    else:
        return w2[0] + add_chars(w1, w2[1:])
```

## reverse string

```python
def reverse(s):
    if len(s) == 1:
        return s
    else:
        return reverse(s[1:]) + s[0]

def reverse(s):
    if len(s) == 0:
        return ''
    else:
        return reverse(s[1:]) + s[0]
```

## sequence iteration

```python
def mysum(L):
    if L == []:
        return 0
    else:
        return L[0] + mysum(L[1:])
```

iterations

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

## Pascal's Triangle

```
1
1 1
1 2 1
1 3 3 1
1 4 6 4 1
```

```python
def pascal(row, column):
    """Returns the value of the item in Pascal's Triangle 
    whose position is specified by row and column.
    >>> pascal(0, 0)
    1
    >>> pascal(0, 5)	# Empty entry; outside of Pascal's Triangle
    0
    >>> pascal(3, 2)	# Row 3 (1 3 3 1), Column 2
    3
    >>> pascal(4, 2)     # Row 4 (1 4 6 4 1), Column 2
    6
    """
    if column == 0:
    	return 1
    elif row == 0:
    	return 0
    else:
    	return pascal(row - 1, column) + pascal(row - 1, column - 1)
```

## Knapsack

```python
def knapsack(weights, values, c):
    """
    takes a list weights, list values and a capacity c
    return that max value
    >>> w = [2, 6, 3, 3]
    >>> v = [1, 5, 3, 3]
    >>> knapsack(w, v, 6)
    6
    """
    "*** YOUR CODE HERE ***"
    if len(weights) == 0:
        return 0
    else:
        first_weight, rest_weight = weights[0], weights[1:]
        first_value, rest_value = values[0], values[1:]
        with_first = first_value + knapsack(rest_weight, rest_value, c - first_weight)
        without_first = knapsack(rest_weight, rest_value, c)
        if first_weight <= c:
            return max(with_first, without_first)
        else:
            return without_first
```

# Mutual Recursion

## luhn sum - credit card validation

checksum = a multiple of 10

```python
def luhn_sum(n):
    if n < 10:
        return n
    else:
        all_but_last, last = n // 10, n % 10
        return luhn_sum_double(all_but_last) + last
 
def luhn_sum_double(n):
    all_but_last, last = n // 10, n % 10
    luhn_digit = sum_digits(2 * last)
    if n < 10:
        return n
    else:
        return luhn_sum(all_but_last) + luhn_digit
```

## even or odd

```python
def is_even(n):
    if n == 0:
        return True
    else:
        return is_odd(n-1)

def is_odd(n):
    if n == 0:
        return False
    else:
        return is_even(n-1)

result = is_even(4)
```

all-in-one

```python
def is_even(n):
    if n == 0:
        return True
    else:
        if (n-1) == 0:
            return False
        else:
            return is_even((n-1)-1)
```
