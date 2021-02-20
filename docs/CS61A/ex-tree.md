# Tree (object)

## Tree Class

```python
class Tree:
    """A tree is a label and a list of branches."""
    def __init__(self, label, branches=[]):
        self.label = label
        for branch in branches:
            assert isinstance(branch, Tree)
        self.branches = list(branches)
    def __repr__(self):
        if self.branches:
            branch_str = ', ' + repr(self.branches)
        else:
            branch_str = ''
        return 'Tree({0}{1})'.format(repr(self.label), branch_str)
    def __str__(self):
        return '\n'.join(self.indented())
    def indented(self):
        lines = []
        for b in self.branches:
            for line in b.indented():
                lines.append('  ' + line)
        return [str(self.label)] + lines
    def is_leaf(self):
        return not self.branches
```

if using data abstraction:

```python
def tree(label, branches=[]):
    for branch in branches:
        assert is_tree(branch) # check: each branch is a tree
    return [label] + branches

def label(tree):
    return tree[0]

def branches(tree):
    return tree[1:]

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

## Fibonacci Tree

```python
def fib_tree(n):
    """A Fibonacci tree.
    >>> print(fib_tree(4))
    3
      1
        0
        1
      2
        1
        1
          0
          1
    """
    if n == 0 or n == 1:
        return Tree(n)
    else:
        left = fib_tree(n-2)
        right = fib_tree(n-1)
        fib_n = left.label + right.label
        return Tree(fib_n, [left, right])
```

if using data abstraction:

```python
def fib_tree(n):
    if n <= 1:
        return tree(n)
    else:
        left, right = fib_tree(n-2), fib_tree(n-1)
        return tree(label(left) + label(right), [left, right])
```

## Create a Tree

```python
>>> t = Tree(1, [Tree(3), Tree(4)])
>>> t
Tree(1, [Tree(3), Tree(4)])
>>> print(t)
1
  3
  4
```

## Sum Lables

```python
def sum_labels(t):
    """
    Sum the labels of a Tree instance, which may be None.
    >>> sum_labels(fib_tree(5))
    10
    """
    return t.label + sum([sum_labels(b) for b in t.branches])
```



## Leaves

```python
def leaves(tree):
    """Return the leaf values of a tree.

    >>> leaves(fib_tree(4))
    [0, 1, 1, 0, 1]
    """
    if tree.is_leaf():
        return [tree.label]
    else:
        return sum([leaves(b) for b in tree.branches], [])
    	# all_leaves = []
        # for b in t.branches:
        #     all_leaves.extend(leaves(b))
        # return all_leaves
```

## Height

```python
def height(tree):
    """The height of a tree."""
    if tree.is_leaf():
        return 0
    else:
        return 1 + max([height(b) for b in tree.branches])
```

## Prune

```python
def prune(t, n):
    """Prune sub-trees whose label value is n.

    >>> t = fib_tree(5)
    >>> prune(t, 1)
    >>> print(t)
    5
      2
      3
        2
    """
    t.branches = [b for b in t.branches if b.label != n]
    for b in t.branches:
        prune(b, n)
```

# Tree (function)

## Tree definition

```python
def tree(label, branches=[]):
    for branch in branches:
        assert is_tree(branch) # check: each branch is a tree
    return [label] + branches

def label(tree):
    return tree[0]

def branches(tree):
    return tree[1:]

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

## Fibonacci Tree

```python
def fib_tree(n):
    if n <= 1:
        return tree(n)
    else:
        left, right = fib_tree(n-2), fib_tree(n-1)
        return tree(label(left) + label(right), [left, right])
```

```python
>>> fib_tree(0)
[0]
>>> fib_tree(1)
[1]
>>> fib_tree(2)
[1, [0], [1]]
>>> fib_tree(3)
[2, [1], [1, [0], [1]]]
>>> fib_tree(4)
[3, [1, [0], [1]], [2, [1], [1, [0], [1]]]]
```

## Count Partitions

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

## Leaves

Implement leaves, which returns a list of the leaf labels of a tree

hint: if sum a list of lists, you get a list containing the elements of those lists

```python
>>> sum([ [1], [2, 3], [4] ], [])
[1, 2, 3, 4]

>>> sum([ [[1]], [2] ], [])
[[1], 2]
```

sum only gets rid of one level, doesn't remove all the nested

```python
def leaves(tree):
    if is_leaf(tree):
        return [label(tree)]
    else:
        return sum(_____________, [])

# need the sum(list of leaf labels for each branch)
# so, it will be
[leaves(b) for b in branches(tree)]
```

finally

```python
def leaves(tree):
    """Return a list containing the leaf labels of tree."""
    if is_leaf(tree):
        return [label(tree)]
    else:
        return sum([leaves(b) for b in branches(tree)], [])
```

## Count Leaves

```python
def count_leaves(t):
    if is_leaf(t):
        return 1
    else:
        return ([count_leaves(b) for b in branches(t)])
```

## Increment Leaves

a function that creates a tree from another tree is also recursive

```python
def increment_leaves(t):
    """Return a tree like t but with leaf labels incremented."""
    if is_leaf(t):
        return tree(label(t) + 1)
    else:
        # increment all the leaves in the branch
        bs = [increment_leaves(b) for b in branches(t)]
 		return tree(label(t), bs)

def increment(t):
    """Return a tree like t but with all labels incremented."""
    # don't need base case
    return tree(label(t) + 1, [increment(b) for b in branches(t)])
```

## Print Trees

cannot see the structure

```python
def print_tree(t):
    print(label(t))
    for b in branches(t):
        print_tree(b)
```

indent to see the structure

```python
def print_tree(t, indent=0):
    print('  ' * indent + str(label(t)))
    for b in branches(t):
        print_tree(b, indent+1)
```

## Print All Paths

```python
def print_all_paths(t):
    """
    Print all the paths from the root to a leaf.
    >>> t = tree(1, [tree(2, [tree(3), tree(5)]), tree(4)])
    >>> print_all_paths(t)
    [1, 2, 3]
    [1, 4]
    """
    for path in all_paths(t):
        print(path)

def all_paths(t):
    if is_leaf(t):
        yield [label(t)]
    for b in branches(t):
        for path in all_paths(b):
            yield [label(t)] + path
```

## Summing Paths

referring back to factorial

```python
def fact(n):
    if n == 0:
        return 1
    else:
        return n * fact(n-1)

def fact_times(n, k):
    """Return k * n * (n-1) * ... * 1"""
    if n == 0:
        return k
    else:
        return fact_times(n-1, k*n) # k*n*(n-1)!

def fact(n):
    return fact_times(n, 1)
```

define print function

```python
def print_sums(t, so_far):
    so_far = so_far + label(t)
    if is_leaf(t):
        print(so_far)
    else:
        for b in branches(t):
            print_sums(b, so_far)
```

test with examples

```python
numbers = tree(3, [tree(4), tree(5, [tree(6)])])

haste = tree('h', [tree('a', [tree('s'),
                              tree('t')]),
                   tree('e')])
```

results

```python
>>> print_sums(numbers, 0)
7
14
>>> print_sums(haste, '')
has
hat
he
```

## Copy Tree

```python
def copy_tree(t):
    """Returns a copy of t. Only for testing purposes.
    >>> t = tree(5)
    >>> copy = copy_tree(t)
    >>> t = tree(6)
    >>> print_tree(copy)
    5
    """
    return tree(label(t), [copy_tree(b) for b in branches(t)])
```

## Tree Height

```python
def height(t):
    """Return the height of a tree.
    hint: height of a tree is the length of
    the longest path from root to a leaf.
    
    >>> t = tree(3, [tree(5, [tree(1)]), tree(2)])
    >>> height(t)
    2
    """
    if is_leaf(t):
        return 0
    else:
        return 1 + max([height(branch) for branch in branches(t)])
    # alternatively
    # return 1 + max([0] + [height(branch) for branch in branches(t)])
```

## Tree Max Path

```python
def max_path(t):
    """Return the maximum path of the tree.
    >>> t = tree(1, [tree(5, [tree(1), tree(3)]), tree(10)])
    >>> max_path(t)
    11
    """
    if is_leaf(t):
        return [t[0]]
    else:
        return [t[0]] + max([max_path(branch) for branch in branches(t)])
```

Max Path Sum

```python
def max_path_sum(t):
    """Return the maximum path sum of the tree.
    >>> t = tree(1, [tree(5, [tree(1), tree(3)]), tree(10)])
    >>> max_path_sum(t)
    11
    """
    if is_leaf(t):
        return t[0]
    else:
        return max([max_path_sum(branch) for branch in branches(t)]) + t[0]
```

## Square Tree

```python
def square_tree(t):
    """Return a tree with the square of every element in t
    """
    sq_branches = [square_tree(branch) for branch in branches(t)]
    return tree(label(t)**2, sq_branches)
```

## Find Element in Tree

```python
def berry_finder(t):
    """Returns True if t contains a node with the value 'berry' and 
    False otherwise.
    """
    assert is_tree(t)
    if label(t) == 'berry':
        return True
    else:
        for b in branches(t):
            if berry_finder(b):
                return True
    return False
```

## Tree Find Path

```python
def find_path(tree, x):
    """
    find the path from the parent to the node contains x
    >>> t = tree(2, [tree(7, [tree(3), tree(6, [tree(5), tree(11)])] ), tree(15)])
    >>> find_path(t, 5)
    [2, 7, 6, 5]
    >>> find_path(t, 10) # returns None
    """
    if label(tree) == x:
        return [label(tree)]
    for b in branches(tree):
        path = find_path(b, x)
        if path:
            return [label(tree)] + path
```

## Prune Tree

```python
def prune_tree(t, k):
    """
    k = depth
    take in a tree and return a new tree that only contains
    the first k levels of the original tree
    """
    assert is_tree(t)
    if k == 0:
        return tree(label(t))
    else:
        return tree(label(t) + [prune_tree(b, k-1) for b in branches(t)])
```

## Prune Leaves

```python
def prune_leaves(t, vals):
    """Return a modified copy of t with all leaves that have a label
    that appears in vals removed.  Return None if the entire tree is
    pruned away.
    >>> t = tree(2)
    >>> print(prune_leaves(t, (1, 2)))
    None
    >>> numbers = tree(1, [tree(2), tree(3, [tree(4), tree(5)]), tree(6, [tree(7)])])
    >>> print_tree(numbers)
    1
      2
      3
        4
        5
      6
        7
    >>> print_tree(prune_leaves(numbers, (3, 4, 6, 7)))
    1
      2
      3
        5
      6
    """
    if is_leaf(t):
        if label(t) in vals:
            return None
        else:
            return t
    pruned = [prune_leaves(b, vals) for b in branches(t)]
    return tree(label(t), [b for b in pruned if b is not None])
```

## Sprout Leaves

```python
def sprout_leaves(t, leaves):
    """Sprout new leaves containing the data in leaves at each leaf in
    the original tree t and return the resulting tree.
    >>> t1 = tree(1, [tree(2), tree(3)])
    >>> print_tree(t1)
    1
      2
      3
    >>> new1 = sprout_leaves(t1, [4, 5])
    >>> print_tree(new1)
    1
      2
        4
        5
      3
        4
        5
    """
    if is_leaf(t):
        return tree(label(t), [tree(leaf) for leaf in leaves])
    else:
        return tree(label(t), [sprout_leaves(b, leaves) for b in branches(t)])
```

## Replace Leaf

```python
def replace_leaf(t, find_value, replace_value):
    """Returns a new tree where every leaf value equal to find_value has
    been replaced with replace_value.

    >>> yggdrasil = tree('odin',
    ...                  [tree('balder',
    ...                        [tree('thor'),
    ...                         tree('freya')]),
    ...                   tree('frigg',
    ...                        [tree('thor')]),
    ...                   tree('thor',
    ...                        [tree('sif'),
    ...                         tree('thor')]),
    ...                   tree('thor')])
    >>> laerad = copy_tree(yggdrasil) # copy yggdrasil for testing purposes
    >>> print_tree(replace_leaf(yggdrasil, 'thor', 'freya'))
    odin
      balder
        freya
        freya
      frigg
        freya
      thor
        sif
        freya
      freya
    >>> laerad == yggdrasil # Make sure original tree is unmodified
    True
    """
    if is_leaf(t) and label(t) == find_value:
        return tree(replace_value)
    else:
        return  tree(label(t), [replace_leaf(b, find_value, replace_value) for b in branches(t)])
```

## Add Tree

iterative

```python
def add_trees(t1, t2):
    result_label = label(t1) + label(t2)
    result_branches = []
    i = 0
    while i < min(len(branches(t1)), len(branches(t2))):
        b1, b2 = branches(t1)[i], branches(t2)[i]
        new_branch = add(tree(b1, b2))
        result_branches = result_branches + [new_branch]
        i += 1
    result_branches = result_branches + branches(t1)[i:]
    result_branches = result_branches + branches(t2)[i:]
    return tree(result_label, result_branches)
```

use `zip` to tidy up

```python
def add_trees(t1, t2):
    result_label = label(t1) + label(t2)
    result_branches = []
    i = 0
    for b1, b2 in zip(branches(t1), branches(t2)):
        new_branch = add(tree(b1, b2))
        result_branches = result_branches + [new_branch]
    i = len(result_branches)
    result_branches = result_branches + branches(t1)[i:]
    result_branches = result_branches + branches(t2)[i:]
    return tree(result_label, result_branches)
```

list comprehension

```python
def add_trees(t1, t2):
    """
    >>> numbers = tree(1,
    ...                [tree(2,
    ...                      [tree(3),
    ...                       tree(4)]),
    ...                 tree(5,
    ...                      [tree(6,
    ...                            [tree(7)]),
    ...                       tree(8)])])
    >>> print_tree(add_trees(numbers, numbers))
    2
      4
        6
        8
      10
        12
          14
        16
    >>> print_tree(add_trees(tree(2), tree(3, [tree(4), tree(5)])))
    5
      4
      5
    >>> print_tree(add_trees(tree(2, [tree(3)]), tree(2, [tree(3), tree(4)])))
    4
      6
      4
    >>> print_tree(add_trees(tree(2, [tree(3, [tree(4), tree(5)])]), \
    tree(2, [tree(3, [tree(4)]), tree(5)])))
    4
      6
        8
        5
      5
    """
    result_label = label(t1) + label(t2)
    result_branches = [add_tree(b1, b2) for b1, b2 in zip(branches(t1), branches(t2))]
    i = len(result_branches)
    result_branches = result_branches + branches(t1)[i:]
    result_branches = result_branches + branches(t2)[i:]
    return tree(result_label, result_branches)
```

## Tree Map

```python
def tree_map(fn, t):
    """Maps the function fn over the entries of t and returns the
    result in a new tree.
    >>> numbers = Tree(1,
    ...                [Tree(2,
    ...                      [Tree(3),
    ...                       Tree(4)]),
    ...                 Tree(5,
    ...                      [Tree(6,
    ...                            [Tree(7)]),
    ...                       Tree(8)])])
    >>> print(tree_map(lambda x: 2**x, numbers))
    2
      4
        8
        16
      32
        64
          128
        256
    """
    "*** YOUR CODE HERE ***"
    if is_leaf(t):
        return tree(fn(label(t)))
    else:
        return tree(fn(label(t)), [tree_map(fn, b) for b in branches(t)])
```

## In-Order Traversal (TBD)



## Pre-Order Traversal

```python
def preorder(t):
    """Return a list of the entries in this tree in the order that they
    would be visited by a preorder traversal (see problem description).
    >>> numbers = tree(1, [tree(2), tree(3, [tree(4), tree(5)]), tree(6, [tree(7)])])
    >>> preorder(numbers)
    [1, 2, 3, 4, 5, 6, 7]
    >>> preorder(tree(2, [tree(4, [tree(6)])]))
    [2, 4, 6]
    """
    if is_leaf(t):
        return [label(t)]
    else:
        res_list = [label(t)]
        for b in branches(t):
            res_list += preorder(b)
    return res_list
```

return as tree

```python
def preorder(t):
    """Return a list of the entries in this tree in the order that they
    would be visited by a preorder traversal (see problem description).
    >>> numbers = tree(1, [tree(2), tree(3, [tree(4), tree(5)]), tree(6, [tree(7)])])
    >>> preorder(numbers)
    [1, 2, 3, 4, 5, 6, 7]
    >>> preorder(tree(2, [tree(4, [tree(6)])]))
    [2, 4, 6]
    """
    if is_leaf(t):
        return [label(t)]
    else:
        return [[label(t)] + preorder(b) for b in branches(t)]

```

## Post-Order Traversal (TBD)



## Collect Words

```python
def collect_words(t):
    """"Return a list of all the words contained in the tree where the value of each node in
the tree is an individual letter. Words terminate at the leaf of a tree.

    >>> greetings = tree('h', [tree('i'),
    ...                        tree('e', [tree('l', [tree('l', [tree('o')])]),
    ...                                   tree('y')])])
    >>> collect_words(greetings)
	['hi', 'hello', 'hey']

    """
    if is_leaf(t):
        return [label(t)]
    else:
        words = []
        for b in branches(t):
            words += [label(t) + w for w in collect_words(b)]
    return words
```

## Word in Path

```python
def has_path(t, word):
    """Return whether there is a path in a tree where the entries along the path
    spell out a particular word.

    >>> greetings = tree('h', [tree('i'),
    ...                        tree('e', [tree('l', [tree('l', [tree('o')])]),
    ...                                   tree('y')])])
    >>> has_path(greetings, 'h')
    True
    >>> has_path(greetings, 'i')
    False
    >>> has_path(greetings, 'hi')
    True
    >>> has_path(greetings, 'hello')
    True
    >>> has_path(greetings, 'hey')
    True
    >>> has_path(greetings, 'bye')
    False
    """
    assert len(word) > 0, 'no path for empty word.'
    if label(t) != word[0]:
        return False
    elif len(word) == 1:
        return True
    else:
        for b in branches(t):
            if has_path(b, word[1:]):
                return True
        return False
```

## Non-consecutive Trade Profit

maximum profit to make on trades:  
if execute S, then cannot execute trades;  
that have an edge directly connected to S.

```python
def profit(t):
    """Return the max profit.
    >>> t = tree(2, [tree(3), tree(4, [tree(5)])])
    >>> profit(t)
    8
    """
    return helper(t, False)

def helper(t, used_parent):
    if used_parent:
        return sum([helper(b, False) for b in branches(t)])
    else:
        use_label_total = label(t) labelsum([helper(b, True) for b in branches(t)])
        skip_label_total = 0 +  sum([helper(b, False) for b in branches(t)])
        return max(use_label_total, skip_label_total)
```



W