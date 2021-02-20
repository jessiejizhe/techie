# Function

## basic

```python
def signum(x):
    return 1 if x > 0 else -1 if x < 0 else 0
```

## series

```python
def series(x, N):
    if N == 1:
        return x
    else:
        return N * x**N + series(x, N-1)
```

## default value

```python
def pressure(v, t, n=6.022e23):
    """Compute the pressure in pascals of an ideal gas.

    v -- volume of gas, in cubic meters
    t -- absolute temperature in degrees kelvin
    n -- particles of gas (default: one mole)
    """
    k = 1.38e-23  # Boltzmann's constant
    return n * k * t / v
```

## Lambda Function

```python
>>> square = lambda x: x * x
>>> square(10)
100
>>> (lambda x: x * x)(3)
9
```

## Newton's Method

```python
def newton_update(f, df):
    def update(x):
        return x - f(x) / df(x)
    return update

def find_zero(f, df):
    def near_zero(x):
        return approx_eq(f(x), 0)
    return improve(newton_update(f, df), near_zero)

def square_root_newton(a):
    def f(x):
        return x * x - a
    def df(x):
        return 2 * x
    return find_zero(f, df)

def power(x, n):
  """Return x * x * x * ... * x for x repeated n times."""
  product, k = 1, 0
  while k < n:
      product, k = product * x, k + 1
    return product

def nth_root_of_a(n, a):
    def f(x):
        return power(x, n) - a
    def df(x):
        return n * power(x, n-1)
    return find_zero(f, df)
```

## anonymous function

```python
from operator import sub, mul

def make_anonymous_factorial():
    """Return the value of an expression that computes factorial.

    >>> make_anonymous_factorial()(5)
    120
    >>> from construct_check import check
    >>> # ban any assignments or recursion
    >>> check(HW_SOURCE_FILE, 'make_anonymous_factorial', ['Assign', 'AugAssign', 'FunctionDef', 'Recursion'])
    True
    """
    return lambda x: (lambda f: f(f, x))(lambda f, n: 1 if n == 1 else mul(n, f(f, sub(n, 1))))
```

# Higher-Order Function

## function repeater

take in a one-argument function f and an integer x

return another function which takes in one argument, another integer.

This function returns the result of applying f to x this number of times.

```python
def make_func_repeater(f, x):
    def repeat(i):
        if i == 0:
            return i
        else:
            return f(repeat(i-1))
    return repeat
```

## cycle functions

- `n = 0`, return `x`
- `n = 1`, apply `f1` to `x`, or return `f1(x)`
- `n = 2`, apply `f1` to `x` and then `f2` to the result of that, or return `f2(f1(x))`
- `n = 3`, apply `f1` to `x`, `f2` to the result of applying `f1`, and then `f3` to the result of applying `f2`, or `f3(f2(f1(x)))`
- `n = 4`, start the cycle again applying `f1`, then `f2`, then `f3`, then `f1` again, or `f1(f3(f2(f1(x))))`
- And so forth.

```python
def cycle(f1, f2, f3):
    """Returns a function that is itself a higher-order function.

    >>> def add1(x):
    ...     return x + 1
    >>> def times2(x):
    ...     return x * 2
    >>> def add3(x):
    ...     return x + 3
    >>> my_cycle = cycle(add1, times2, add3)
    >>> identity = my_cycle(0)
    >>> identity(5)
    5
    >>> add_one_then_double = my_cycle(2)
    >>> add_one_then_double(1)
    4
    >>> do_all_functions = my_cycle(3)
    >>> do_all_functions(2)
    9
    >>> do_more_than_a_cycle = my_cycle(4)
    >>> do_more_than_a_cycle(2)
    10
    >>> do_two_cycles = my_cycle(6)
    >>> do_two_cycles(1)
    19
    """
    def cycle_n_times(n):
        def calculate(x):
            i = 0
            while i < n:
                if i % 3 == 0:
                    x = f1(x)
                elif i % 3 == 1:
                    x = f2(x)
                elif i % 3 == 2:
                    x = f3(x)
                i += 1
            return x
        return calculate
    return cycle_n_times
```

# Divide and Conquer

## Tower of Hanoi

```python
def move_disk(disk_number, from_peg, to_peg):
    print("Move disk " + str(dis_number) + "from peg " + from_peg + "to peg " + str(to_peg) + '.')

def solve_hanoi(n, start_peg, end_peg):
    if n == 1:
        move_disk(n, start_peg, end_peg)
    else:
        spare_peg = 6 - start_peg - end_peg
    	solve_hanoi(n-1, start_peg, spare_peg)
        move_disk(n, start_peg, end_peg)
        solve_hanoi(n-1, spare_peg, end_peg)
```

walkthrough

```python
hanoi(3,1,2)
	hanoi(2,1,3)
    	hanoi(1,1,2)
        move(2,1,3)
        solve(1,2,3)
    move_disk(3,1,2)
    hanoi(2,3,2)
    	hanoi(1,3,1)
        move(2,3,2)
        hanoi(1,1,2)
```

disc move - exponential growth

```
1  1
2  3
3  7
4  15
...
n  solve (n-1) twice + 1
```

