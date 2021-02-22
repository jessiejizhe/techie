# Object-Oriented Programming

- A method for organizing modular programs
  - Abstraction barriers
  - Bundling together information and related behavior
- A metaphor for computation using **distributed state**
  - each object has its own local state
  - each object also know how to change its own local state, based on method calls
  - method calls are messages passed between objects
  - several objects may all be instances of a common type
  - different types may relate to each other
- Specialized syntax and vocabulary to support this metaphor

## Classes and Objects

- A **class** combines (and abstracts) data and functions
- An **object** is an instantiation of a class
- example
  - String is a built-in class, append is a function
  - Int is a built-in class, + is a function

```python
myball = Ball(10.0, 15.0, 0.0, -5.0)
```

## Classes

- A class serves as a template for its instances

```python
class <name>:
    <suite>
```

- A class statement creates a new `class` and \<name\> binds that class to  in the first frame of the current environment
- Assignment & def statements in \<suite\>  create attributes of the class (not names in frames)
- When a class is called
  - A new instance of that class is created
  - The `__init__` method of the class is called with the new object as its first argument (named `self`), along with any additional arguments provided in the call expression

```python
class Account:
    """An account has a balance and a holder.
    All accounts share a common interest rate.

    >>> a = Account('John')
    >>> a.holder
    'John'
    >>> a.deposit(100)
    100
    >>> a.withdraw(90)
    10
    >>> a.withdraw(90)
    'Insufficient funds'
    >>> a.balance
    10
    >>> a.interest
    0.02
    >>> Account.interest = 0.04
    >>> a.interest
    0.04
    """

    interest = 0.02  # A class attribute

    def __init__(self, account_holder):
        self.holder = account_holder
        self.balance = 0

    def deposit(self, amount):
        """Add amount to balance."""
        self.balance = self.balance + amount
        return self.balance

    def withdraw(self, amount):
        """Subtract amount from balance if funds are available."""
        if amount > self.balance:
            return 'Insufficient funds'
        self.balance = self.balance - amount
        return self.balance
```

`__init__` is called a **constructor**

**Constructor**

- allocate memory for a Ball object
- initialize the Ball object with values
- return address of the Ball object
- similar to a list

## Objects

- **Objects** represent information
- They consist of **data** and **behavior**, bundled together to create abstractions
- Objects can represent things, but also properties, interactions, & processes
- A type of object is called a **class**; classes are first-class values in Python
- Object-oriented programming:
  - A metaphor for organizing large programs
  - Special syntax that can improve the composition of programs (i.e. `.` expression)
- In Python, every value is an object 
  - All **objects** have **attributes** (named values that are part of the object)
    - use `.` to designated an attribute of an object
  - A lot of data manipulation happens through object **methods** (function-valued attributes)
    - can be accessed through `.` expression
    - `date(2021,1,1).strftime('%Y-%M-%d')`
    - `date(2021,1,1).strftime('%A %B %d')`
  - Functions do one thing; objects do many related things

## Instance

`help(isinstance)`: Return whether an object is an instance of a class or of a subclass thereof.

```python
>>> n = 123
>>> isinstance(n, int)
True
```

## Methods and Functions

Python distinguishes between:

- Functions, which we have been creating since the beginning of the course
- Bound methods, which couple together a function and the object on which that method will be invoked
  - Object + Function = Bound Method

```python
>>> type(Account.deposit)
<class 'function'>
>>> type(tom_account.deposit)
<class 'method'>
```

## Methods

Methods are functions defined in the suite of a class statement

Invoking methods

- All invoked methods have access to the object via the self parameter, and so they can all access and manipulate the object's state
- Dot notation automatically supplies the first argument to a method

**Static Method**

```python
class Math:
    @staticmethod
    def square(x):
        return x*x
```

The decorator `@staticmethod` allows`square` to be a regular function, but happen to be grouped in `Math` class.

## Dot Expressions

- Objects receive messages via dot notation
- Dot notation accesses attributes of the instance or its class
  `<expression> . <name>`
- The `<expression>` can be any valid Python expression
- The `<name>` must be a simple name
- Evaluates to the value of the attribute looked up by `<name>` in the object
  that is the value of the `<expression>`

## Attributes

- Data stored within either an instance or a class
- Attributes can be accessed using
  - dot notation
  - built-in function `getattr`

Objects are ***iterable*** (an interface) if they have an `__iter__` method that returns an *iterator*.

## Class Attributes

Class attributes are "shared" across all instances of a class because they are attributes of the class, not the instance.

```python
class Account:
	interest = 0.02 # A class attribute

    def __init__(self, account_holder):
		self.balance = 0
		self.holder = account_holder

# Additional methods would be defined here
```

## Python Object System

- Functions are objects.
- Bound methods are also objects: a function that has its first parameter "self" already bound to an instance.
- Dot expression evaluate to bound methods for class attributes that are functions `<instance>.<method_name>`

## Inheritance

Inheritance is a method for relating classes together.

```python
class <name>(<base class>):
    <suite>
```

Conceptually, the new subclass "shares" attributes with its base class.

Using inheritance, we implement a subclass by specifying its differences from the base class.

```python
class Account:
    """An account has a balance and a holder.

    >>> a = Account('John')
    >>> a.holder
    'John'
    >>> a.deposit(100)
    100
    >>> a.withdraw(90)
    10
    >>> a.withdraw(90)
    'Insufficient funds'
    >>> a.balance
    10
    >>> a.interest
    0.02
    """

    interest = 0.02  # A class attribute

    def __init__(self, account_holder):
        self.holder = account_holder
        self.balance = 0

    def deposit(self, amount):
        """Add amount to balance."""
        self.balance = self.balance + amount
        return self.balance

    def withdraw(self, amount):
        """Subtract amount from balance if funds are available."""
        if amount > self.balance:
            return 'Insufficient funds'
        self.balance = self.balance - amount
        return self.balance


class CheckingAccount(Account):
    """A bank account that charges for withdrawals.

    >>> ch = CheckingAccount('Jack')
    >>> ch.balance = 20
    >>> ch.withdraw(5)
    14
    >>> ch.interest
    0.01
    """

    withdraw_fee = 1
    interest = 0.01

    def withdraw(self, amount):
        return Account.withdraw(self, amount + self.withdraw_fee)
        # Alternatively:
        return super().withdraw(amount + self.withdraw_fee)

class SavingsAccount(Account):
    """A bank account that charges for deposits."""

    deposit_fee = 2

    def deposit(self, amount):
        return Account.deposit(self, amount - self.deposit_fee)
```

The `super()` function in Python makes class inheritance more manageable and extensible. The function returns a temporary object that allows reference to a parent class.

## Multiple Inheritance

```python
class AsSeenOnTVAccount(CheckingAccount, SavingsAccount):
    """A bank account that charges for everything."""

    def __init__(self, account_holder):
        self.holder = account_holder
        self.balance = 1  # A free dollar!
```

results

```python
>>> such_a_deal = AsSeenOnTVAccount("John")
>>> such_a_deal.balance
1
>>> such_a_deal.deposit(20)
19
```

## Inheritance and Composition

Inheritance is best for representing **is-a** relationships.

- E.g., a checking account is a specific type of account
- So, CheckingAccount inherits from Account

Composition is best for representing **has-a** relationships

- E.g., a bank has a collection of bank accounts it manages
- So, A bank has a list of accounts as an attribute

**composition example**

```python
class Bank:
    """A bank has accounts and pays interest.

    >>> bank = Bank()
    >>> john = bank.open_account('John', 10)
    >>> jack = bank.open_account('Jack', 5, CheckingAccount)
    >>> jack.interest
    0.01
    >>> john.interest = 0.06
    >>> bank.pay_interest()
    >>> john.balance
    10.6
    >>> jack.balance
    5.05
    """
    def __init__(self):
        self.accounts = []

    def open_account(self, holder, amount, account_type=Account):
        """Open an account_type for holder and deposit amount."""
        account = account_type(holder)
        account.deposit(amount)
        self.accounts.append(account)
        return account

    def pay_interest(self):
        """Pay interest to all accounts."""
        for account in self.accounts:
            account.deposit(account.balance * account.interest)
```

## Object Oriented Design

- Designing for inheritance -- don't repeat yourself.
- Attributes that have been overridden are still accessible via class objects.

## Representation

**String Representations**

In Python, all objects produce **two** string representations:

- The `str` is legible to humans
- The `repr` is legible to the Python interpreter

The `str` and `repr` strings are often the same, but not always.

**`str` String for an Object**

The result of calling str on the value of an expression is what Python prints using the print function.

```python
>>> from fractions import Fraction
>>> half = Fraction(1, 2)
>>> repr(half)
'Fraction(1, 2)'
>>> str(half)
'1/2'
>>> print(half)
1/2
```

**`repr` String for an Object**

The `repr` function returns a Python expression (a string) that evaluates to an equal object.

`repr(object) -> string` 

Return the canonical string representation of the object. For most object types, `eval(repr(object)) == object`.

```python
>>> 12e6
12000000.0
>>> print(repr(12e6))
12000000.0
```

Some objects do not have a simple Python-readable string

```python
>>> repr(min)
'<built-in function min>'
```

**eval and repr**

```python
>>> s = 'Hello, World'
>>> s
'Hello, World'
>>> print(repr(s))
'Hello, World'
>>> print(s)
Hello, World
>>> print(str(s))
Hello, World
>>> repr(s)
"'Hello, World'"

>>> eval(repr(s))
'Hello, World'
```

**`@property` decorator** allows functions to be called without call expression syntax (parentheses following an expression).

```python
>>> from math import atan2
>>> class ComplexRI(Complex):
        def __init__(self, real, imag):
            self.real = real
            self.imag = imag
        @property
        def magnitude(self):
            return (self.real ** 2 + self.imag ** 2) ** 0.5
        @property
        def angle(self):
            return atan2(self.imag, self.real)
        def __repr__(self):
            return 'ComplexRI({0:g}, {1:g})'.format(self.real, self.imag)
```

usage

```python
>>> ri = ComplexRI(5, 12)
>>> ri.real
5
>>> ri.magnitude
13.0
>>> ri.real = 9
>>> ri.real
9
>>> ri.magnitude
15.0
```

## Polymorphic Functions

A function that applies to many (poly) different forms (morph) of data.

`str` and `repr` are both polymorphic; they apply to any object.

`repr` invokes a zero-argument method `__repr__` on its argument.

```python
>>> half.__repr__()
'Fraction(1, 2)'
```

`str` invokes a zero-argument method `__str__` on its argument

```python
>>> half.__str__()
'1/2'
```

**Implementation of `repr` and `str`**

The behavior of `repr` is slightly more complicated than invoking `__repr__` on its argument.

- An instance attribute called `__repr__` is ignored! Only class attributes are found.

```python
def repr(x):
    return type(x).__repr__(x)
```

The behavior of `str` is also complicated.

- An instance attribute called `__str__` is ignored.
- If no ` __str__` attribute is found, uses `repr` string.
- `str` is a class, not a function.

```python
def str(x):
    t = type(x)
    if hasattr(t, '__str__'):
        return t.__str__(x)
    else:
        return repr(x)
```

**Example**

```python
class Bear:
    """A Bear."""
    def __init__(self):
        self.__repr__ = lambda: 'oski'
        self.__str__ = lambda: 'this bear'
    def __repr__(self):
        return 'Bear()'
    def __str__(self):
        return 'a bear'
```

compare

```python
>>> oski = Bear()
>>> print(oski)
a bear
>>> print(str(oski))
a bear
>>> print(repr(oski))
Bear()
>>> print(oski.__str__())
this bear
>>> print(oski.__repr__())
oski
```

## Interfaces

**Message passing**: Objects interact by looking up attributes on each other (passing messages).

The attribute look-up rules allow different data types to respond to the same message

A **shared message** (attribute name) that elicits similar behavior from different object classes is a powerful method of abstraction.

An **interface** is **a set of shared messages**, along with a specification of what they mean.

## Special Method Names in Python

Certain names are special because they have built-in behavior.

These names always start and end with two underscores

- `__init__`: Method invoked automatically when an object is constructed.
- `__repr__`: Method invoked to display an object as a Python expression.
- `__add__`: Method invoked to add one object to another.
  - `__radd__ = __add__`
- `__bool__`: Method invoked to convert an object to True or False.
  - `Account.__bool__ = lambda self: self.balance != 0`
- `__float__`: Method invoked to convert an object to a float (real number).

## Efficiency (Q)

- **Constant Time**
  - Increasing n doesn't affect time
- **Logarithmic Time**
  - Doubling the input increases the time by a constant C
  - 1024x the input increases the time by only 10 times C
  - i.e. `exp_fast`
- **Linear Time**
  - Doubling the input doubles the time
  - 1024x the input takes 1024x as much time
  - i.e. `exp`
- **Quadratic Time**
  - Functions that process all pairs of values in a sequence of length n take quadratic time
  - i.e. `overlap`
- **Exponential Time**
  - Tree-recursive functions can take exponential time
  - i.e. recursive `fib`

How many calculations to do Fib Tree?

```python
def fib(n):
    if n == 0 or n == 1:
        return n
    else:
        return fib(n-2) + fib(n-1)

def count(f):
    def counted(n):
        counted.call_count += 1
        return f(n)
    counted.call_count = 0
    return counted
```

**Memoization**

  Idea: remember the results that have been computed before.

```python
def memo(f):
    cache = {}
    def memoized(n):
        if n not in cache:
            cache[n] = f(n)
        return cache[n]
    return memoized
```

**Exponentiation**

```python
def exp(b, n):
    if n == 0:
        return 1
    else:
        return b * exp(b, n-1)

def exp_fast(b, n):
    if n == 0:
        return 1
    elif n % 2 == 0:
        return square(exp_fast(b, n//2))
    else:
        return b * exp_fast(b, n-1)

def square(x):
    return x * x
```

## Consumption of Space

- Which environment frames do we need to keep during evaluation?
- At any moment there is a set of active environments
- Values and frames in active environments consume memory
- Memory that is used for other values and frames can be recycled
- **Active environments**
  - Environments for any function calls currently being evaluated
  - Parent environments of functions named in active environments

# Object Examples

## Type Dispatching

`isinstance()`

```python
>>> isinstance(123, int)
True
>>> isinstance(123, str)
False
>>> isinstance('123', str)
True
```

`isnumeric()`

```python
>>> '1234'.isnumeric()
True
>>> 'xyz'.isnumeric()
False
>>> '2.0'.isnumeric()
False
```

## Date Object

```python
>>> from datetime import date, timedelta

>>> today = date(2021,2,14)
>>> today.year
2021
>>> today.strftime('%A, %B, %d')
'Sunday, February, 14'

>>> today +  timedelta(days=90)
datetime.date(2021, 5, 15)

>>> dir(today)
['__add__', '__class__', '__delattr__', '__dir__', '__doc__', '__eq__', '__format__', '__ge__', '__getattribute__', '__gt__', '__hash__', '__init__', '__init_subclass__', '__le__', '__lt__', '__ne__', '__new__', '__radd__', '__reduce__', '__reduce_ex__', '__repr__', '__rsub__', '__setattr__', '__sizeof__', '__str__', '__sub__', '__subclasshook__', 'ctime', 'day', 'fromisoformat', 'fromordinal', 'fromtimestamp', 'isocalendar', 'isoformat', 'isoweekday', 'max', 'min', 'month', 'replace', 'resolution', 'strftime', 'timetuple', 'today', 'toordinal', 'weekday', 'year']
```

## String Object

```python
>>> 'rOBERT dE nIRO'.swapcase()
'Robert De Niro'
>>> 'eyes'.upper().endswith('YES')
True

>>> dir('x')
['__add__', '__class__', '__contains__', '__delattr__', '__dir__', '__doc__', '__eq__', '__format__', '__ge__', '__getattribute__', '__getitem__', '__getnewargs__', '__gt__', '__hash__', '__init__', '__init_subclass__', '__iter__', '__le__', '__len__', '__lt__', '__mod__', '__mul__', '__ne__', '__new__', '__reduce__', '__reduce_ex__', '__repr__', '__rmod__', '__rmul__', '__setattr__', '__sizeof__', '__str__', '__subclasshook__', 'capitalize', 'casefold', 'center', 'count', 'encode', 'endswith', 'expandtabs', 'find', 'format', 'format_map', 'index', 'isalnum', 'isalpha', 'isascii', 'isdecimal', 'isdigit', 'isidentifier', 'islower', 'isnumeric', 'isprintable', 'isspace', 'istitle', 'isupper', 'join', 'ljust', 'lower', 'lstrip', 'maketrans', 'partition', 'replace', 'rfind', 'rindex', 'rjust', 'rpartition', 'rsplit', 'rstrip', 'split', 'splitlines', 'startswith', 'strip', 'swapcase', 'title', 'translate', 'upper', 'zfill']
```

## Sequence/List? Object

assign

```python
>>> chinese = ['coin', 'string', 'myriad']  # A list literal
>>> suits = chinese                         # Two names refer to the same list
```

remove

```python
>>> suits.pop()             # Remove and return the final element
'myriad'
>>> suits.remove('string')  # Remove the first element that equals the argument
```

add

```python
>>> suits.append('cup')              # Add an element to the end
>>> suits.extend(['sword', 'club'])  # Add all elements of a sequence to the end
```

replace

```python
>>> suits[2] = 'spade' 
>>> suits[0:2] = ['heart', 'diamond']  # Replace a slice
```

check

```python
>>> suits
['heart', 'diamond', 'spade', 'club']
>>> suits is ['heart', 'diamond', 'spade', 'club']       # check for identity
False
>>> suits == ['heart', 'diamond', 'spade', 'club']       # check for equality
True
```

properties

```python
>>> dir(suits)
['__add__', '__class__', '__contains__', '__delattr__', '__delitem__', '__dir__', '__doc__', '__eq__', '__format__', '__ge__', '__getattribute__', '__getitem__', '__gt__', '__hash__', '__iadd__', '__imul__', '__init__', '__init_subclass__', '__iter__', '__le__', '__len__', '__lt__', '__mul__', '__ne__', '__new__', '__reduce__', '__reduce_ex__', '__repr__', '__reversed__', '__rmul__', '__setattr__', '__setitem__', '__sizeof__', '__str__', '__subclasshook__', 'append', 'clear', 'copy', 'count', 'extend', 'index', 'insert', 'pop', 'remove', 'reverse', 'sort']
```

## Tuple Object

Built-in ***tuple*** is an immutable sequence, with finite length.

```python
>>> code = ("up", "up", "down", "down") + ("left", "right") * 2
>>> len(code)
8
>>> code[3]
'down'
>>> code.count("down")
2
>>> code.index("left")
4
```

properties

```python
>>> dir(code)
['__add__', '__class__', '__contains__', '__delattr__', '__dir__', '__doc__', '__eq__', '__format__', '__ge__', '__getattribute__', '__getitem__', '__getnewargs__', '__gt__', '__hash__', '__init__', '__init_subclass__', '__iter__', '__le__', '__len__', '__lt__', '__mul__', '__ne__', '__new__', '__reduce__', '__reduce_ex__', '__repr__', '__rmul__', '__setattr__', '__sizeof__', '__str__', '__subclasshook__','count', 'index']
```

## Dictionary Object

Dictionaries are Python's built-in data type for storing and manipulating correspondence relationships.

```python
>>> numerals = {'I': 1.0, 'V': 5, 'X': 10}
>>> numerals['I'] = 1

>>> numerals.keys()
dict_keys(['I', 'V', 'X'])
>>> numerals.values()
dict_values([1, 5, 10])
>>> numerals.items()
dict_items([('I', 1), ('V', 5), ('X', 10)])

>>> numerals.get('A', 0)
0
>>> numerals.get('V', 0)
5
```

loop over

```python
>>> for k,v in numerals.items():
...     print(k,v)
...
I 1
V 5
X 10
```

construct a dictionary

```python
>>> dict([(3, 9), (4, 16), (5, 25)])
{3: 9, 4: 16, 5: 25}

>>> {x: x*x for x in range(3,6)}
{3: 9, 4: 16, 5: 25}
```

properties

```python
>>> dir(numerals)
['__class__', '__contains__', '__delattr__', '__delitem__', '__dir__', '__doc__', '__eq__', '__format__', '__ge__', '__getattribute__', '__getitem__', '__gt__', '__hash__', '__init__', '__init_subclass__', '__iter__', '__le__', '__len__', '__lt__', '__ne__', '__new__', '__reduce__', '__reduce_ex__', '__repr__', '__setattr__', '__setitem__', '__sizeof__', '__str__', '__subclasshook__', 'clear', 'copy', 'fromkeys', 'get', 'items', 'keys', 'pop', 'popitem', 'setdefault', 'update', 'values']
```

zip

```python
>>> numbers = [1, 2, 3]
>>> letters = ['a', 'b', 'c']
>>> zipped = zip(numbers, letters)
>>> type(zipped)
<class 'zip'>
>>> dict(zipped)
{1: 'a', 2: 'b', 3: 'c'}
```

implementation by function

```python
def dictionary():
    """Return a functional implementation of a dictionary."""
    records = []
    def getitem(key):
        matches = [r for r in records if r[0] == key]
        if len(matches) == 1:
            key, value = matches[0]
            return value
    def setitem(key, value):
        nonlocal records
        non_matches = [r for r in records if r[0] != key]
        records = non_matches + [[key, value]]
    def dispatch(message, key=None, value=None):
        if message == 'getitem':
            return getitem(key)
        elif message == 'setitem':
            setitem(key, value)
    return dispatch

>>> d = dictionary()
>>> d('setitem', 3, 9)
>>> d('setitem', 4, 16)
>>> d('getitem', 3)
9
>>> d('getitem', 4)
16
```

# Class Examples

## MinList

```python
class MinList:
    """A list that can only pop the smallest element """
    
    def __init__(self):
        self.items = []
        self.size = 0
    
    def append(self, item):
        """Appends an item to the MinList
        >>> m = MinList()
        >>> m.append(4)
        >>> m.append(2)
        >>> m.size
        2
        """
        self.items.append(item)
        self.size += 1
    
    def pop(self):
        """ Removes and returns the smallest item from the MinList
        >>> m = MinList()
        >>> m.append(4)
        >>> m.append(1)
        >>> m.append(5)
        >>> m.pop()
        1
        >>> m.size
        2
        """
        min_out = min(self.items)
        self.items = [i for i in self.items if i != min_out]
        self.size -= 1
        return min_out
```

## Email

```python
class Email:
    """Every email object has 3 instance attributes: the
    message, the sender name, and the recipient name.
    """
    
    def __init__(self, msg, sender_name, recipient_name):
        self.msg = msg
        self.sender_name = sender_name
        self.recipient_name = recipient_name


class Server:
    """Each Server has an instance attribute clients, which
    is a dictionary that associates client names with
    client objects.
    """
    
    def __init__(self):
        self.clients = {}
    
    def send(self, email):
        """Take an email and put it in the inbox of the client
        it is addressed to.
        """
        client = self.clients[email.recipient_name]
        client.receive(email)

    def register_client(self, client, client_name):
        """Takes a client object and client_name and adds them
        to the clients instance attribute.
        """
        self.clients[client_name] = client


class Client:
    """Every Client has instance attributes name (which is
    used for addressing emails to the client), server
    (which is used to send emails out to other clients), and
    inbox (a list of all emails the client has received).
    """
    
    def __init__(self, server, name):
        self.inbox = []
        self.server = server
        self.name = name
        self.server.register_client(self, self.name)
    
    def compose(self, msg, recipient_name):
        """Send an email with the given message msg to the
        given recipient client.
        """
        email = Email(msg, self.name, recipient_name)
        self.server.send(email)
    
    def receive(self, email):
        """Take an email and add it to the inbox of this
        client.
        """
        self.inbox.append(email)
```

## Pet

```python
class Pet():
    
    def __init__(self, name, owner):
        self.is_alive = True # It's alive!!!
        self.name = name
        self.owner = owner
    
    def eat(self, thing):
        print(self.name + " ate a " + str(thing) + "!")
    
    def talk(self):
        print(self.name)


class Dog(Pet):
    def talk(self):
        print(self.name + ' says woof!')


class Cat(Pet):
    
    def __init__(self, name, owner, lives=9):
        Pet.__init__(self, name, owner)
        self.lives = lives
    
    def talk(self):
        """ Print out a cat's greeting.
        >>> Cat('Thomas', 'Tammy').talk()
        Thomas says meow!
        """
        print(self.name + ' says meow!')
    
    def lose_life(self):
        """Decrements a cat's life by 1. When lives reaches zero, 'is_alive'
        becomes False. If this is called after lives has reached zero, print out
        that the cat has no more lives to lose.
        """
        if self.lives > 0:
            self.lives -= 1
            if self.lives == 0:
                self.is_alive = False
        else:
            print("This cat has no more lives to lose :(")


class NoisyCat(Cat):
    """A Cat that repeats things twice."""
    
    def __init__(self, name, owner, lives=9):
        # Is this method necessary? Why or why not?
        Cat.__init__(self, name, owner, lives)
        # not necessary because NoisyCat already inherits Cat’s __init__ method
    
    def talk(self):
        """Talks twice as much as a regular cat.
        >>> NoisyCat('Magic', 'James').talk()
        Magic says meow!
        Magic says meow!
        """
        Cat.talk(self)
        Cat.talk(self)
```

## Vending Machine

```python
class VendingMachine:
    """A vending machine that vends some product for some price.

    >>> v = VendingMachine('candy', 10)
    >>> v.vend()
    'Inventory empty. Restocking required.'
    >>> v.add_funds(15)
    'Inventory empty. Restocking required. Here is your $15.'
    >>> v.restock(2)
    'Current candy stock: 2'
    >>> v.vend()
    'You must add $10 more funds.'
    >>> v.add_funds(7)
    'Current balance: $7'
    >>> v.vend()
    'You must add $3 more funds.'
    >>> v.add_funds(5)
    'Current balance: $12'
    >>> v.vend()
    'Here is your candy and $2 change.'
    >>> v.add_funds(10)
    'Current balance: $10'
    >>> v.vend()
    'Here is your candy.'
    >>> v.add_funds(15)
    'Inventory empty. Restocking required. Here is your $15.'

    >>> w = VendingMachine('soda', 2)
    >>> w.restock(3)
    'Current soda stock: 3'
    >>> w.restock(3)
    'Current soda stock: 6'
    >>> w.add_funds(2)
    'Current balance: $2'
    >>> w.vend()
    'Here is your soda.'
    """
    
    def __init__(self, product, price):
        self.quantity = 0
        self.balance = 0
        self.product = product
        self.price = price
    
    def add_funds(self, fund):
        if self.quantity == 0:
            return 'Inventory empty. Restocking required. Here is your ${0}.'.format(fund)
        else:
            self.balance += fund
            return 'Current balance: ${0}'.format(self.balance)

    def restock(self, item):
        self.quantity += item
        return 'Current {0} stock: {1}'.format(self.product, self.quantity)
    
    def vend(self):
        if self.quantity == 0:
            return 'Inventory empty. Restocking required.'
        
        change = self.balance - self.price
        if change < 0:
            return 'You must add ${0} more funds.'.format(-change)
        elif change == 0:
            self.quantity -= 1
            self.balance -= self.price
            return 'Here is your {}.'.format(self.product)
        else:
            self.quantity -= 1
            self.balance = 0
            return 'Here is your {} and ${} change.'.format(self.product, change)
```

## Mint Coin

```python
class Mint:
    """A mint creates coins by stamping on years.

    The update method sets the mint's stamp to Mint.current_year.

    >>> mint = Mint()
    >>> mint.year
    2020
    >>> dime = mint.create(Dime)
    >>> dime.year
    2020
    >>> Mint.current_year = 2100  # Time passes
    >>> nickel = mint.create(Nickel)
    >>> nickel.year     # The mint has not updated its stamp yet
    2020
    >>> nickel.worth()  # 5 cents + (80 - 50 years)
    35
    >>> mint.update()   # The mint's year is updated to 2100
    >>> Mint.current_year = 2175     # More time passes
    >>> mint.create(Dime).worth()    # 10 cents + (75 - 50 years)
    35
    >>> Mint().create(Dime).worth()  # A new mint has the current year
    10
    >>> dime.worth()     # 10 cents + (155 - 50 years)
    115
    >>> Dime.cents = 20  # Upgrade all dimes!
    >>> dime.worth()     # 20 cents + (155 - 50 years)
    125
    """
    current_year = 2020

    def __init__(self):
        self.update()

    def create(self, kind):
        return kind(self.year)

    def update(self):
        self.year = Mint.current_year

class Coin:
    def __init__(self, year):
        self.year = year

    def worth(self):
        year_gap = Mint.current_year - self.year
        if year_gap > 50:
            return self.cents + year_gap - 50
        else:
            return self.cents
        # return max(self.cents,self.cents+Mint.current_year-self.year-50)

class Nickel(Coin):
    cents = 5

class Dime(Coin):
    cents = 10
```

# Non-Local Statement

## make adder increasing

```python
def make_adder_inc(a):
    """
    >>> adder1 = make_adder_inc(5)
    >>> adder2 = make_adder_inc(6)
    >>> adder1(2)
    7
    >>> adder1(2) # 5 + 2 + 1
    8
    >>> adder1(10) # 5 + 10 + 2
    17
    >>> [adder1(x) for x in [1, 2, 3]]
    [9, 11, 13]
    >>> adder2(5)
    11
    """
    step = 0
    def adder(k):
        nonlocal step
        step += 1
        return a + k + step - 1
    return adder
```

## next Fibonacci

```python
def make_fib():
    """Returns a function that returns the next Fibonacci number
    every time it is called.

    >>> fib = make_fib()
    >>> fib()
    0
    >>> fib()
    1
    >>> fib()
    1
    >>> fib()
    2
    >>> fib()
    3
    >>> fib2 = make_fib()
    >>> fib() + sum([fib2() for _ in range(5)])
    12
    >>> from construct_check import check
    >>> # Do not use lists in your implementation
    >>> check(this_file, 'make_fib', ['List'])
    True
    """
    x, y = 0, 1
    def fib():
        nonlocal x, y
        res = x
        x, y = y, x + y
        return res
    return fib
```