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

## format

```python
format_str = '.1%'
format_str = ',.0f'
format_str = '$,.2f'


'{:,.0%}'.format(x)
'{:,.0f}'.format(x)
'${:,.2f}'.format(x)


format_cpm = '${:,.2f}'
format_bbr = '${:,.0f}'
format_imp = '{:,.0f}'
format_pct = '{:.1%}'


## pad string numbers with 0
"3".zfill(2)   # returns "03"
"-42".zfill(5) # returns "-0042"

## number format examples
'${:,.2f}'.format(114)
'${:,d}'.format(int(10000))
```

print dataframe in format

```python
display(df.style.format({'var1': "{:.2f}",'var2': "{:.2f}",'var3': "{:.2%}"}))


df = df_account.groupby(['surface','format']).agg({col_ctr: ['mean', 'min'], col_cpm: ['mean', 'min']}).reset_index()
display(df.style.format({
   (col_ctr, 'mean'): '{:.2%}',
   (col_ctr, 'min'): '{:.2%}',
   (col_cpm, 'mean'): '${:.2f}',
   (col_cpm, 'min'): '${:.2f}'})
)
```

## scientific notation

suppress scientific notation
```python
pd.set_option("display.precision", 4)
pd.set_printoptions(precision=4)
pd.set_option("display.float_format", lambda x: "%.4f" % x)  # supress scientific notation
pd.set_option("display.max_rows", 200)
```

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

## combinations / permutations

```python
import itertools
for k,v in itertools.product(['opt-in', 'opt-out'], ['MAE', 'MAI', 'aeovo']):
   print('\n', k,v)
   test = ColumnQualityParam(k, v)
   print('lookup:', test.lookup.keys())
   print('passthrough:', test.passthrough.keys())




print(list(zip(['opt-in', 'opt-out'], ['MAE', 'MAI', 'aeovo'])))




lst_predict_quarters = []
for y in ['24', '25', '26']:
   for q in ['Q1', 'Q2', 'Q3', 'Q4']:
       lst_predict_quarters.append(y+q)
```