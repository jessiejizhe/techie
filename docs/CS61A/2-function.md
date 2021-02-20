# Functions

The special value **None** represents nothing in Python.

- **Pure Functions**

  - just return values
  - simpler to test
  - essential for writing *concurrent* programs, in which multiple call expressions may be evaluated simultaneously
  - Functions have some input (their arguments) and return some output (the result of applying them)

- **Non-pure Functions**
- have side effects (consequence of calling a function)
  
- In addition to returning a value, applying a non-pure function can generate *side effects*, which make some change to the state of the interpreter or computer. A common side effect is to generate additional output beyond the return value.

```python
>> print(print(1), print(2))
1
2
None None
```

```python
>>> two = print(2)
2
>>> print(two)
None
```

- define a function

```python
def <name>(<formal parameters>):
    return <return expression>
```

-  A description of the formal parameters of a function is called the function's signature.

- The body of a function is not executed until the function is called (not when it is defined).

- Good functions are

  - Each function should have exactly one job.
  - *Don't repeat yourself* is a central tenet of software engineering.
  - Functions should be defined generally.

- Conventions

  - Function names are lowercase, with words separated by underscores. Descriptive names are encouraged.
  - Function names typically evoke operations applied to arguments by the interpreter (e.g., `print`, `add`, `square`) or the name of the quantity that results (e.g., `max`, `abs`, `sum`).
  - Parameter names are lowercase, with words separated by underscores. Single-word names are preferred.
  - Parameter names should evoke the role of the parameter in the function, not just the kind of argument that is allowed.
  - Single letter parameter names are acceptable when their role is obvious, but avoid "l" (lowercase ell), "O" (capital oh), or "I" (capital i) to avoid confusion with numerals.

- Aspects of a function abstraction (3 core attributes)

  - The *domain* of a function is the set of arguments it can take.
  - The *range* of a function is the set of values it can return.
  - The *intent / behavior* of a function is the relationship it computes between inputs and output (as well as any side effects it might generate).

## Function Documentation

**docstring**

```python
def pressure(v, t, n):
    """Compute the pressure in pascals of an ideal gas.

    Applies the ideal gas law: http://en.wikipedia.org/wiki/Ideal_gas_law

    v -- volume of gas, in cubic meters
    t -- absolute temperature in degrees kelvin
    n -- particles of gas
    """
    k = 1.38e-23  # Boltzmann's constant
    return n * k * t / v
```

When you call `help` with the name of a function as an argument, you see its docstring (type `q` to quit Python help).

```python
help(pressure)
```

Comments in Python can be attached to the end of a line following the `#` symbol.

```bash
python3 -m doctest test.py
python3 -m doctest -v test.py
```

## Default Argument Values

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

In the `def` statement header, `=` does not perform assignment, but instead indicates a default value to use when the function is called.

By contrast, the assignment statement to `k` in the body of the function binds the name `k` to an approximation of Boltzmann's constant.

## Statements

- Expressions can also be executed as statements, in which case they are evaluated, but their value is discarded.
- Executing a pure function has no effect, but executing a non-pure function can cause effects as a consequence of function application.
- A statement is executed by the interpreter to perform an action.
- **Compound** Statements
  - The first header determines a statement’s type
  - def statements are compound statements
  - A suite is a sequence of statements
  - To “execute” a suite means to execute its sequence of statements, in order
- **Conditional** Statements
  - Boolean Contexts
- **iterations**
  - for
  - while

## Testing

`python test.py -v` and then???

`python -i test.py`

**Assertions**

```python
def fib(n):
  """Compute the nth Fibonacci number, for n >= 2."""
  pred, curr = 0, 1   # Fibonacci numbers 1 and 2
  k = 2               # Which Fib number is curr?
  while k < n:
      pred, curr = curr, pred + curr
      k = k + 1
  return curr
```

An `assert` statement has an expression in a boolean context, followed by a quoted line of text (single or double quotes are both fine, but be consistent) that will be displayed if the expression evaluates to a false value.

```python
assert fib(8) == 13, 'The 8th Fibonacci number should be 13'
```

When the expression being asserted evaluates to a true value, executing an assert statement has no effect. When it is a false value, `assert` causes an error that halts execution.

```python
def fib_test():
  assert fib(2) == 1, 'The 2nd Fibonacci number should be 1'
  assert fib(3) == 1, 'The 3rd Fibonacci number should be 1'
  assert fib(50) == 7778742049, 'Error at the 50th Fibonacci number'
```

A test function for `fib` should test several arguments, including extreme values of `n`.

**Doctests**

The first line of a docstring should contain a one-line description of the function, followed by a blank line. A detailed description of arguments and behavior may follow. In addition, the docstring may include a sample interactive session that calls the function.

```python
def sum_naturals(n):
  """Return the sum of the first n natural numbers.
  >>> sum_naturals(10)
  55
  >>> sum_naturals(100)
  5050
  """
  total, k = 0, 1
  while k <= n:
      total, k = total + k, k + 1
  return total
```

  example

```python
>>> from doctest import run_docstring_examples
>>> run_docstring_examples(sum_naturals, globals(), True)
Finding tests in NoName
Trying:
  sum_naturals(10)
Expecting:
  55
ok
Trying:
  sum_naturals(100)
Expecting:
  5050
ok
```

- Its first argument is the function to test.
- The second should always be the result of the expression `globals()`, a built-in function that returns the global environment.
- The third argument is `True` to indicate that we would like "verbose" output: a catalog of all tests run.

When the return value of a function does not match the expected result, the `run_docstring_examples` function will report this problem as a test failure.

```bash
python3 -m doctest <python_source_file>
```

## Higher-Order Functions

Functions that manipulate functions are called higher-order functions.

Benefits
- express general methods of computation
- remove repetition
- separate concerns among functions

**functions as arguments**

```python
def summation(n, term):
    total, k = 0, 1
    while k <= n:
        total, k = total + term(k), k + 1
    return total

def cube(x):
    return x*x*x

def sum_cubes(n):
    return summation(n, cube)

result = sum_cubes(3)
```

  1e6, a shorthand for 1 * 10^6 = 1000000

**functions as general methods**

```python
def improve(update, close, guess=1):
    while not close(guess):
        guess = update(guess)
    return guess

def golden_update(guess):
    return 1/guess + 1

def square_close_to_successor(guess):
    return approx_eq(guess * guess, guess + 1)

def approx_eq(x, y, tolerance=1e-15):
    return abs(x - y) < tolerance
```

**nested definitions**

This two-argument update function is incompatible with `improve` (it takes two arguments, not one), and it provides only a single update, while we really care about taking square roots by repeated updates. The solution to both of these issues is to place function definitions inside the body of other definitions.

```python
def sqrt(a):
      def sqrt_update(x):
          return average(x, a/x)
      def sqrt_close(x):
          return approx_eq(x * x, a)
      return improve(sqrt_update, sqrt_close)
```

**Lexical Scope**

This discipline of sharing names among nested definitions is called *lexical scoping*. Critically, the inner functions have access to the names in the environment where they are defined (not where they are called).

**functions as returned values**

Functions defined within other function bodies are bound to names in a local frame.

```python
def make_adder(n):
  """Return a function that takes one argument k and returns k + n.
  >>> add_three = make_adder(3)
  >>> add_three(4)
  7
  """
  def adder(k):
      return k + n
  return adder

# equivalently

>>> f = make_add(2000)
>>> f(13)
>>> 2013
```

## Currying

**Curry**: Transform a multi-argument function into a single-argument, higher-order function.

We can use higher-order functions to convert a function that takes multiple arguments into a chain of functions that each take a single argument. More specifically, given a function `f(x, y)`, we can define a function `g` such that `g(x)(y)` is equivalent to `f(x, y)`.

```python
>>> def curried_pow(x):
      def h(y):
          return pow(x, y)
      return h

>>> curried_pow(2)(3)
8

>>> def map_to_range(start, end, f):
      while start < end:
          print(f(start))
          start = start + 1

>>> map_to_range(0, 10, curried_pow(2))
1
2
4
8
16
32
64
128
256
512
```

**curry vs uncurry**

```python
>>> def curry2(f):
      """Return a curried version of the given two-argument function."""
      def g(x):
          def h(y):
              return f(x, y)
          return h
      return g

>>> def uncurry2(g):
      """Return a two-argument version of the given curried function."""
      def f(x, y):
          return g(x)(y)
      return f
```

results

```python
>>> pow_curried = curry2(pow)
>>> pow_curried(2)(5)
32

>>> uncurry2(pow_curried)(2, 5)
32
```

## Self-Reference

Returning a function using its own name

```python
def print_all(x):
    print(x)
    return print_all

print(1)(3)(5)
# 1 3 5

def print_sums(x):
    print(x)
    def next_sum(y):
        return print_sums(x+y)
    return next_sum

print_sums(1)(3)(5)
# 1 4 9
```

## Lambda Function

Create functions values on the fly, no intrinsic name

```python
>>> square = lambda x: x * x
>>> square(10)
100
>>> (lambda x: x * x)(3)
9
>>> (lambda f, x: f(x))(lambda y: y + 1, 10)
11
```

## Abstractions and naming

practical naming conventions
- n, k, i -- usually integers
- x, y, z -- usually real numbers
- f, g, h -- usually functions

## Function Decorators

Python provides **special syntax to apply higher-order functions** as part of executing a `def` statement, called a decorator. Perhaps the most common example is a `trace`.

The decorator symbol `@` may also be followed by a call expression. The expression following `@` is evaluated first (just as the name `trace` was evaluated above), the `def` statement second, and finally the result of evaluating the decorator expression is applied to the newly defined function, and the result is bound to the name in the `def` statement.

```python
def trace(fn):
    def wrapped(x):
        print('-> ', fn, '(', x, ')')
        return fn(x)
    return wrapped
```

Application

```python
>>> @trace
    def triple(x):
        return 3 * x

>>> triple(12)
->  <function triple at 0x102a39848> ( 12 )
36

# equivalently

>>> def triple(x):
        return 3 * x
>>> triple = trace(triple)
```

## Recursive Functions

A function is called *recursive* if the body of the function calls the function itself, either directly or indirectly.

Recursive functions start with conditional statements check for **base cases**; base cases are evaluated **without recursive calls**.

Recursive cases are evaluated **with recursive calls**; they simplify the original problem.

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

While we can unwind the recursion using our model of computation, it is often clearer to think about recursive calls as functional abstractions. That is, we should not care about how `fact(n-1)` is implemented in the body of `fact`; we should simply trust that it computes the factorial of `n-1`.

Treating a recursive call as a functional abstraction has been called a ***recursive leap of faith***.

## Mutual Recursion

When a recursive procedure is divided among two functions that call each other, the functions are said to be *mutually recursive*. As an example, consider the following definition of even and odd for non-negative integers.

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

Mutually recursive functions can be turned into a single recursive function by breaking the abstraction boundary between the two functions.

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

## Tree Recursion

A function with multiple recursive calls is said to be *tree recursive* because each call branches into multiple smaller calls, each of which branches into yet smaller calls, just as the branches of a tree become smaller but more numerous as they extend from the trunk.

**Fibonacci**

```python
def fib(n):
    if n == 1:
        return 0
    elif n == 2:
        return 1
    else:
        return fib(n-2) + fib(n-1)
```

**Counting Partitions**

```python
def count_partitions(n, m):
    """
    number of positive integer n using parts up to size m
    possibility 1: use at least one 4
    possibility 2: don't use any 4
    """
    if n == 0:
        return 1
    elif n < 0:
        return 0
    elif m == 0:
        return 0
    else:
        with_m = count_partitions(n-m, m)
        without_m = count_partitions(n, m-1)
        return with_m + without_m
```

