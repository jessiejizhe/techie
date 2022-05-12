# basic

## array to string

```python
def array2str(x):
    r = ','.join(str(e) for e in x)
    if r == '':
        r = str(0)
    return r
```

# dataframe

## proportion

```python
def getSumKey(key):
    return 'sum(' + key + ')'

def getProportion(df, grpByKey, prptnKey):
    tmp = df.groupBy(*grpByKey).sum()
    df_j_tmp = df.join(tmp, *grpByKey)
    for key in prptnKey:
        df_j_tmp = df_j_tmp.withColumn(key+'_p', 100*df_j_tmp[key]/df_j_tmp[getSumKey(key)])
        df_j_tmp = df_j_tmp.drop(getSumKey(key))
    return df_j_tmp
```

# args

## optional arg

```python
def foo(a, b = None):
    if b is not None:
        print(a+b)
    else:
        print(a)

foo(3)    #3
foo(3, 5) #8
```

## multiple args

```python
def multi_add(*args):
    result = 0
    for x in args:
        result = result + x
    return result

multi_add(1,2,3,4,5)
```

# format

## number to B/M/K

```python
def convert_big_num_smart(num):
    if num >= 1e9:
        big_num = str(round(num / 1e9, 1)) + 'B'
    elif num >= 1e6:
        big_num = str(round(num / 1e6, 1)) + 'M'
    elif num >= 1e3:
        big_num = str(round(num / 1e3, 1)) + 'K'
    else:
        big_num = str(round(num, 1))
    return big_num
```

