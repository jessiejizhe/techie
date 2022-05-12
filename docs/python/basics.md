# nan

count number of nan in list

```python
np.count_nonzero(~np.isnan(data))
```

# OS path

```python
import sys
import os
from os import path

os.getcwd()
sys.path.append('/home/user')
```

check item existence

```python
print("Item exists: " + str(path.exists("textfile.txt")))
```

check item types

```python
print("Item is a file: " + str(path.isfile("textfile.txt")))
print("Item is a directory: " + str(path.isdir("textfile.txt")))
```

check item path

```python
print("Item path: " + str(path.realpath("textfile.txt")))
```

# style

## number formats

```python
'${:,.2f}'.format(114)
'${:,d}'.format(int(reve))
```

## scientific notation

suppress scientific notation



# data structure

## string

reverse string

```python
def reverseString(s):
    l = s.split(' ')
    out = []
    for ll in l:
        out.append(ll[::-1])
    return ' '.join(out)

a = 'this is an apple'
at = reverseString(a)
at
```

## list

list to string

```python
weekdays = ['sun','mon','tue','wed','thu','fri','sat']
listAsString = ' '.join(weekdays)
print(listAsString)
```

list to tuple

```python
weekdays = ['sun','mon','tue','wed','thu','fri','sat']
listAsTuple = tuple(weekdays)
print(listAsTuple)
```

list to set

```python
weekdays = ['sun','mon','tue','wed','thu','fri','sat']
listAsSet = set(weekdays)
print(listAsSet)
```

count element

```python
weekdays = ['sun','mon','tue','wed','thu','fri','sun','mon','mon']

print(weekdays.count('mon'))
print([[x,weekdays.count(x)] for x in set(weekdays)])
```

flatten list

```python
input_list = [1, [2, 3, [4, 5, [6, 7]]]]
expected_output_list = [1, 2, 3, 4, 5, 6, 7]

def flatten_list(input_list):
    output_list = []
    if len(input_list)>0:
        for i in input_list:
            if isinstance(i, int):
                output_list.append(i)
            else:
                output_list += flatten_list(i)
    return output_list
```

enumerate

```python
subjects = ['Python', 'Interview', 'Questions']
for i, subject in enumerate(subjects):
    print(i, subject)
```

## array

generate an array of numbers

```python
import numpy as np
np.linspace(0, 100, num=21)
np.linspace(0, 100, num=20, endpoint=False)
```

