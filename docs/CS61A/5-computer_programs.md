# Computer Programs

Functions can be manipulated as data using higher-order **functions**

Data can be endowed with behavior using Message passing & object system.

Organizing large programs

- functional abstraction
- data abstraction
- class inheritance
- generic functions

## Modular Design

**Separation of Concerns**

A design principle: Isolate different parts of a program that address different concerns a modular component can be developed and tested independently.

## Exceptions

- A built-in mechanism in a programming language to declare and respond to exceptional conditions
- Python **raises** an exception whenever an error occurs
- Exceptions can be handled by the program, preventing the interpreter from halting
- Unhandled exceptions will cause Python to halt execution and print a stack trace

**Exceptions are objects!**

- They have classes with constructors.
- They enable non-local continuation of control
- If f calls g and g calls h, exceptions can shift control from h to f without waiting for g to return.
- (Exception handling tends to be slow.)

**Raising Exceptions**

- Assert statements raise an exception of type AssertionError: `assert <expression>, <string>`
  - Assertions are designed to be used liberally. They can be ignored to increase efficiency by running Python with the "-O" flag; "O" stands for optimized: `python3 -O`, then you will see

```python
>>> __debug__
False
>>> assert False, 'Error'
```

- Raise statements `raise <expression>`
  - `<expression>` must evaluate to a subclass of BaseException or an instance of one
  - Exceptions are constructed like any other object.
    - TypeError -- A function was passed the wrong number/type of argument
    - NameError -- A name wasn't found
    - KeyError -- A key wasn't found in a dictionary 
    - RecursionError -- Too many recursive calls

```python
>>> raise Exception('An error occurred')
Traceback (most recent call last):
  File "<stdin>", line 1, in <module>
Exception: an error occurred
```

The interpreter will print a ***stack backtrace***, which is a structured block of text that describes the nested set of active function calls in the branch of execution in which the exception was raised.

**Handling Exceptions**

Try statements handle exceptions

```python
try:
 <try suite>
except <exception class> as <name>:
 <except suite>
...
```

**Execution rule**

- The `<try suite>` is executed first
- If, during the course of executing the `<try suite>`, an exception is raised that is not handled otherwise, and
- If the class of the exception inherits from `<exception class>`, then
- The `<except suite>` is executed, with `<name>` bound to the exception

```python
>>> try:
...     x = 1/0
... except ZeroDivisionError as e:
...     print('handling a', type(e))
...     x = 0
... 
handling a <class 'ZeroDivisionError'>
>>> x
0
```

More examples

```python
def invert(x):
    """Return 1/x

    >>> invert(2)
    Never printed if x is 0
    0.5
    """
    result = 1/x  # Raises a ZeroDivisionError if x is 0
    print('Never printed if x is 0')
    return result
```

Handle the exception

```python
def invert_safe(x):
    """Return 1/x, or the string 'divison by zero' if x is 0.

    >>> invert_safe(2)
    Never printed if x is 0
    0.5
    >>> invert_safe(0)
    'division by zero'
    """
    try:
        return invert(x)
    except ZeroDivisionError as e:
        print('handled', e)
        return str(e)
```

output

```python
>>> invert_safe(0)
handled division by zero
'division by zero'
```

why do we get "division by zero"?

```python
>>> 1/0
Traceback (most recent call last):
  File "<stdin>", line 1, in <module>
ZeroDivisionError: division by zero
```

**Multiple try statements**: Control jumps to the except suite of the most recent try statement that handles that type of exception

```python
try:
    ... this line always gets executed ..
    ... maybe this one too ...
except:
    ...
finaly:
    ... this will always run at the end ...
    ... usually for realeasing resources ...
```

## Scheme (programming language)

Scheme is a dialect of Lisp, the second-oldest programming language that is still widely used today.

**Fundamentals**

- Scheme programs consist of expressions, which can be
  - Primitive expressions: 2, 3.3, true, +, quotient, ...
  - Combinations: (quotient 10 2), (not true), ...
- Numbers are self-evaluating; symbols are bound to values
- Call expressions include an operator and 0 or more operands in parentheses

**Special Forms**

A combination that is not a call expression is a special form:

- **if** expression: `(if <predicate> <consequent> <alternative>)`
- **and** and **or**: `(and <e1> ... <en>)`, `(or <e1> ... <en>)`
- Binding symbols: `(define <symbol> <expression>)`
- New procedures: `(define (<symbol> <formal parameters>) <body>)`
- The `cond` special form that behaves like `if-elif-else` statements in Python
- The `begin` special form combines multiple expressions into one expression
- The `let` special form binds symbols to values **temporarily**; just for one expression

**lambda expression**

Lambda expressions evaluate to anonymous procedures`(lambda (<formal-parameters>) <body>)`

**Scheme Lists**

like Python Linked List. In the late 1950s, computer scientists used confusing names

- `cons`: Two-argument procedure that creates a linked list
- `car`: Procedure that returns the first element of a list
- `cdr`: Procedure that returns the rest of a list
- `nil`: The empty list 

**Symbolic Programming**

- Quotation is used to refer to symbols directly in Lisp
- Quotation can also be applied to combinations to form lists

**Quasiquotation**

## Programming Languages

**Machine languages**: statements are interpreted by the hardware itself

- A fixed set of instructions invoke operations implemented by the circuitry of the central processing unit (CPU)
- Operations refer to specific hardware memory addresses; no abstraction mechanisms

**High-level languages**: statements & expressions are interpreted by another program or compiled (translated) into another language

- Provide means of abstraction such as naming, function definition, and objects
- Abstract away system details to be independent of hardware and operating system

**Metalinguistic Abstraction**

A powerful form of abstraction is to define a new language that is tailored to a particular type of application or problem domain

- **Type of application**: Erlang was designed for concurrent programs. It has built-in elements for expressing concurrent communication. It is used, for example, to implement chat servers with many simultaneous connections
- **Problem domain**: The MediaWiki mark-up language was designed for generating static web pages. It has built-in elements for text formatting and cross-page linking. It is used, for example, to create Wikipedia pages

A programming language has

- **Syntax**: The legal statements and expressions in the language 
- **Semantics**: The execution/evaluation rule for those statements and expressions

To create a new programming language, you either need a:

- **Specification**: A document describe the precise syntax and semantics of the language
- **Canonical Implementation**: An interpreter or compiler for the language

## Parsing

A Parser takes text and returns an expression.

*Text* -- **Lexical Analysis** -- *Tokens* -- **Syntactic Analysis** -- Expression

- **Lexical Analysis**
  - Iterative process
  - Checks for malformed tokens
  - Determines types of tokens
  - Processes one line at a time
- **Syntactic Analysis**
  - Tree-recursive process
  - Balances parentheses
  - Returns tree structure
  - Processes multiple lines

**Syntactic Analysis**

- Syntactic analysis identifies the hierarchical structure of an expression, which may be nested.

- Each call to scheme_read consumes the input tokens for exactly one expression.
- **Base case**: symbols and numbers
- **Recursive call**: scheme_read sub-expressions and combine them

Scheme expressions are represented as Scheme lists.

***Homoiconic*** means source code is data.

## Evaluation

**The Eval Function**

The eval function computes the value of an expression, which is always a number.

It is a generic function that dispatches on the type of the expression (primitive or call).

- **Primitive**: a number evaluates... to itself
- **Call**: a call expression evaluates... to its argument values combined by an operator

```python
def calc_eval(exp):
    if type(exp) in (int, float):
        return exp
    elif isinstance(exp, Pair):
        arguments = exp.second.map(calc_eval)
        return calc_apply(exp.first, arguments)
    else:
        raise TypeError

def calc_apply(operator, args):
    if operator == '+':
        return reduce(add, args, 0)
    elif operator == '-':
        ...
    elif operator == '*':
        ...
    elif operator == '/':
        ...
    else:
        raise TypeError
```



## Interactive Interpreters

**Read-Eval-Print Loop**

The user interface for many programming languages is an interactive interpreter

1. Print a prompt
2. Read text input from the user
3. Parse the text input into an expression
4. Evaluate the expression
5. If any errors occur, report those errors, otherwise
6. Print the value of the expression and repeat

**Raising Exceptions**

Exceptions are raised within lexical analysis, syntactic analysis, eval, and apply.

- **Lexical analysis**: The token 2.3.4 raises ValueError("invalid numeral")
- **Syntactic analysis**: An extra ) raises SyntaxError("unexpected token")
- **Eval**: An empty combination raises TypeError("() is not a number or call expression")
- **Apply**: No arguments to - raises TypeError("- requires at least 1 argument")

**Handling Exceptions**

An interactive interpreter prints information about each error.

A well-designed interactive interpreter should not halt completely on an error, so that the user has an opportunity to try again in the current environment.

## Interpreters

**The Structure of an Interpreter**

- **Eval**
  - Base cases
    - Primitive values (numbers)
    - Look up values bound to symbols
  - Recursive calls
    - Eval(operator, operands) of call expressions • Apply(procedure, arguments)
    - Eval(sub-expressions) of special forms
  - Requires an environment for symbol lookup
- **Apply**
  - Base cases
    - Built-in primitive procedures
  - Recursive calls
    - Eval(body) of user-defined procedures
  - Creates a new environment each time a user-defined procedure is applied

**Logical Forms**

- if
- and / or
- cond

**Quotation**

The quote special form evaluates to the quoted expression, which is not evaluated

**Lambda Expressions**

Lambda expressions evaluate to user-defined procedures

**Define**

Define binds a symbol to a value in the first frame of the current environment.

1. 
Evaluate the `<expression>`
2. Bind `<name>` to its value in the current frame

## Declarative Programming

**Database Management Systems**

- Database management systems (DBMS) are important, heavily used, and interesting!

- A table is a collection of records, which are rows that have a value for each column
- The Structured Query Language (SQL) is perhaps the most widely used declarative programming language

**Declarative vs Imperative**

- **Declarative Languages**
  - such as SQL & Prolog
  - A "program" is a description of the desired result
  - The interpreter figures out how to generate the result
- **Imperative Languages**
  - such as Python & Scheme
  - A "program" is a description of computational processes
  - The interpreter carries out execution/evaluation rules

## Binary Numbers

2-levels are reliable for fluctuations (imagining how to distinguish water level)

**Negative Numbers -- Two's Complement**

- start with an unsigned 4-bit binary number where leftmost bit is 0
  - 0110 = 6
- complement your binary number (flip bits)
  - 1001
- add one to your binary number
  - 1010 = -6

solve the problem of summation of negative and positive numbers in base 2

n-bit signed binary numbers -2^(n-1) ... 2^(n-1) -1

**Fraction Numbers**

± mantissa x base ^ (± exponent)

**Boolean Logic -- Building Gates**

- AND (sequential)
- OR (parallel)
- NOT (resistor)

A **circuit** is a collection of logical gates that transforms a set of binary inputs into a set of
binary outputs.

# Examples

## Restaurant

```python
def search(query, ranking=lambda r: -r.stars):
    results = [r for r in Restaurant.all if query in r.name]
    return sorted(results, key=ranking)


def num_shared_reviewers(restaurant, other):
    # return fast_overlap(restaurant.reviewers, other.reviewers)
    return len([r for r in restaurant.reviewers if r in other.reviewers])


class Restaurant:
    """A restaurant."""
    all = []
    
    def __init__(self, name, stars, reviewers):
        self.name = name
        self.stars = stars
        self.reviewers = reviewers
        Restaurant.all.append(self)

    def similar(self, k, similarity=num_shared_reviewers):
        "Return the K most similar restaurants to SELF, using SIMILARITY for comparison."
        others = list(Restaurant.all)
        others.remove(self)
        return sorted(others, key=lambda r: -similarity(self, r))[:k]

    def __repr__(self):
        return '<' + self.name + '>'
```

load data

```python
import json

def load_reviews(reviews_file):
    reviewers_by_restaurant = {}
    for line in open(reviews_file):
        r = json.loads(line)
        business_id = r['business_id']
        if business_id not in reviewers_by_restaurant:
            reviewers_by_restaurant[business_id] = []
        reviewers_by_restaurant[business_id].append(r['user_id'])
    return reviewers_by_restaurant

def load_restaurants(reviewers_by_restaurant, restaurants_file):
    for line in open(restaurants_file):
        b = json.loads(line)
        reviewers = reviewers_by_restaurant.get(b['business_id'], [])
        Restaurant(b['name'], b['stars'], sorted(reviewers))


load_restaurants(load_reviews('reviews.json'), 'restaurants.json')
```

improve speed

```python
def fast_overlap(s, t):
    """Return the overlap between sorted S and sorted T.

    >>> fast_overlap([2, 3, 5, 6, 7], [1, 4, 5, 6, 7, 8])
    3
    """
    count, i, j = 0, 0, 0
    while i < len(s) and j < len(t):
        if s[i] == t[j]:
            count, i, j = count + 1, i + 1, j + 1
        elif s[i] < t[j]:
            i += 1
        else:
            j += 1
    return count
```

## SQL - Python

```python
import sqlite3

# SQL Intro

db = sqlite3.Connection("nums.db")
db.execute("CREATE TABLE nums AS SELECT 2 as n UNION SELECT 3;")
db.execute("INSERT INTO nums VALUES (?), (?), (?);", range(4, 7))
print(db.execute("SELECT * FROM nums;").fetchall())
db.commit()
```

`db.commit()` will create `nums.db`

Next time, execute `sqlite3 n.db` from command line

## SQL Injection

```python
import readline
import sqlite3

db = sqlite3.Connection(":memory:")
db.execute("CREATE TABLE Students(name);")
db.execute("INSERT INTO Students VALUES ('John');")

def add_name(name):
    cmd = "INSERT INTO Students VALUES ('" + name + "');"
    print("Executing:", cmd)
    db.executescript(cmd)
    print("Students:", db.execute("select * from Students").fetchall())

def add_name_safe(name):
    db.execute("INSERT INTO Students VALUES (?)", [name])
    print("Students:", db.execute("select * from Students").fetchall())

add_name_safe("Jack")
add_name_safe("Jill")
add_name_safe("Robert'); DROP TABLE Students; --");
```

## BlackJack

```python
import random
import readline
import sqlite3

points = {'A': 1, 'J': 10, 'Q': 10, 'K':10}
points.update({n: n for n in range(2, 11)})

def hand_score(hand):
    """Total score for a hand."""
    total = sum([points[card] for card in hand])
    if total <= 11 and 'A' in hand:
        return total + 10
    return total

db = sqlite3.Connection('cards.db')
sql = db.execute
sql('DROP TABLE IF EXISTS cards')
sql('CREATE TABLE cards(card, place);')

def play(card, place):
    """Play a card so that the player can see it."""
    sql('INSERT INTO cards VALUES (?, ?)', (card, place))
    db.commit()

def score(who):
    """Compute the hand score for the player or dealer."""
    cards = sql('SELECT * from cards where place = ?;', [who])
    return hand_score([card for card, place in cards.fetchall()])

def bust(who):
    """Check if the player or dealer went bust."""
    return score(who) > 21

player, dealer = "Player", "Dealer"

def play_hand(deck):
    """Play a hand of Blackjack."""
    play(deck.pop(), player)
    play(deck.pop(), dealer)
    play(deck.pop(), player)
    hidden = deck.pop()

    while 'y' in input("Hit? ").lower():
        play(deck.pop(), player)
        if bust(player):
            print(player, "went bust!")
            return

    play(hidden, dealer)

    while score(dealer) < 17:
        play(deck.pop(), dealer)
        if bust(dealer):
            print(dealer, "went bust!")
            return

    print(player, score(player), "and", dealer, score(dealer))

deck = list(points.keys()) * 4
random.shuffle(deck)
while len(deck) > 10:
    print('\nDealing...')
    play_hand(deck)
    sql('UPDATE cards SET place="Discard";')
```

