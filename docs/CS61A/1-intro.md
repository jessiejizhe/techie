# Intro

## Libraries

An import statement that loads functionality for accessing data on the Internet. 
In particular, it makes available a function called `urlopen`, which can access the content at a uniform resource locator (URL), a location of something on the Internet.

```python
from urllib.request import urlopen
```
Functions encapsulate logic that manipulates data.

```python
shakespeare = urlopen('http://composingprograms.com/shakespeare.txt')

>>> shakespeare
<http.client.HTTPResponse object at 0x0000013C74319908>
```

## Objects

A `set` is a type of object, one that supports set operations like computing intersections and membership. An object seamlessly bundles together data and the logic that manipulates that data, in a way that manages the complexity of both.

all unique words that appear in Shakespeare's plays

```python
words = set(shakespeare.read().decode().split())
```

## Interpreters

Evaluating compound expressions requires a precise procedure that interprets code in a predictable way. A program that implements such a procedure, evaluating compound expressions, is called an interpreter.

```python
>>> {w for w in words if len(w) == 6 and w[::-1] in words}
{'redder', 'drawer', 'reward', 'diaper', 'repaid'}
```

Functions are objects, objects are functions, and interpreters are instances of both.

## Errors

Guiding principles of **Debugging**

- Test incrementally
- Isolate errors
- Check your assumptions
- Consult others

## Elements of Programming

Every powerful language has three such mechanisms:

- **primitive expressions and statements**, which represent the simplest building blocks that the language provides,
- **means of combination**, by which compound elements are built from simpler ones, and
- **means of abstraction**, by which compound elements can be named and manipulated as units.

## Expression

An expression describes a computation and evaluates to a value

Primitive expressions: 2 (number or numeral), add (name), 'hello' (string)

All expressions can use function call notation.

```python
add(2, 3)
```

Operator(Operand, Operand)

Operators and operands are also expressions.

Evaluation procedure for call expressions:

1. Evaluate the operator and then the *operand subexpressions*
2. Apply the function that is the value of the operator to the arguments that are the values of the operands

## Names & Environment

Most important lessons

- An environment is a sequence of frames.
- A name evaluates to the value bound to that name in the earliest frame of the current environment in which that name is found.

The `=` symbol is called the *assignment* operator.

Assignment is our simplest means of *abstraction*.

Assign multiple values to multiple names in a single statement

```python
area, circumference = pi * radius * radius, 2 * pi * radius
```

Environment diagrams visualize the interpreter’s process.

The possibility of binding names to values and later retrieving those values by name means that the interpreter must maintain some sort of memory that keeps track of the names, values, and bindings. This memory is called an ***environment***.

## Operators

- truediv

```python
>>> 5 / 4
1.25
>>> 8 / 4
2.0
```

- floordiv

```python
>>> 5 // 4
1
>>> -5 // 4
-2
```

# Examples

```python
from math import sqrt
sqrt(169)
```

## Objects
Note: Download from http://composingprograms.com/shakespeare.txt
```python
shakes = open('shakespeare.txt')
text = shakes.read().split()
len(text)
text[:25]
text.count('thou')
text.count(',')
```

## Sets
```python
words = set(text)
len(words)
max(words)
max(words, key=len)
```

## Reversals
```python
'DRAW'[::-1]
{w for w in words if w == w[::-1] and len(w)>4}
{w for w in words if w[::-1] in words and len(w) == 4}
{w for w in words if w[::-1] in words and len(w) > 6}
```

## logical expressions

```python
>>> True and 13
13
>>> False or 0
0
>>> not 10
False
>>> not None
True
```

````python
>>> True and 1 / 0 and False
Traceback (most recent call last):
  File "<stdin>", line 1, in <module>
ZeroDivisionError: division by zero
>>> True or 1/0 or False
True
>>> True and 0
0
>>> False or 1
1
>>> 1 and 3 and 6 and 10 and 15
15
>>> -1 and 1>0
True
>>> 0 or False or 2 or 1/0
2
````

```python
>>> (1+1) and 1
1
>>> 1/0 or True
Traceback (most recent call last):
  File "<stdin>", line 1, in <module>
ZeroDivisionError: division by zero
```
