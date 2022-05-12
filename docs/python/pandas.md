```python
import numpy as np
import pandas as pd
import json

import os
import glob

pd.__version__

import warnings
warnings.filterwarnings("ignore", category=FutureWarning)
```

# data I/O

## create

empty table

```python
res = pd.DataFrame()
```

from lists

```python
l_dates = ['2022-01-01', '2022-01-02', '2022-01-03']
l_latency = [10, 20, 30]
df_latency = pd.DataFrame(zip(l_dates, l_latency),
                          columns=['date', 'latency'])
```

## read

working directory path

```python
dir_path = os.getcwd()
```

csv

```python
df = pd.read_csv(dir_path + "file.csv") # default sep=','
df = pd.read_csv(dir_path + "file.csv", sep='\t')
```

json

```python
file_input = 'file.json'
with open(file_input) as f:
    d = json.load(f)
df = pd.json_normalize(data=d, record_path='result', meta='timestamp')
```

read all files into one table under the same folder

```python
##  option 1
dir_path = os.getcwd()
df = pd.concat(map(pd.read_csv, glob.glob(os.path.join('', dir_path + '*.csv'))))

## option 2
path = r'/Users/project_folder/'
all_files = glob.glob(os.path.join(path, "*.csv"))
df_from_each_file = (pd.read_csv(f) for f in all_files)
df = pd.concat(df_from_each_file, ignore_index=True)
```

## write

```python
df.to_csv('output_file.csv', index=False)
```

# data explore

metadata

```python
df.columns
df.shape
df.size
df.dtypes

print('size: {0}\n\n{1}'.format(df.shape, df.dtypes))
```

print table

```python
df.head()
df.tail()

from IPython.display import HTML
HTML(df.to_html(index=False))
```

column statistics

```python
df.describe()
df['$column_name'].describe()
```

# data process

## filter

1. logical operator

   ```python
   df[df.value > 1]
   df[(df.value > 1) & (df.value < 5)]
   ```

2. isin

   ```python
   names = ['John','Jane']
   df[df.name.isin(names)]
   ```

3. str accessor

   ```python
   df[df.name.str.startswith('J')]
   df[df.name.str.contains('y')]
   ```

4. not / tilde (~)

   ```python
   df[~df.name.str.startswith('J')]
   ```

5. query

   ```python
   df.query('product == "A" and value > 1')
   ```

6. nlargest / nsmallest

   ```python
   df.nlargest(3, 'value')
   df.nsmallest(2, 'value')
   ```

7. loc / iloc

   ```python
   df.iloc[3:5, :] #rows 3 and 4, all columns
   df.loc[3:5, :] #rows 3 and 4, all columns
   ```

## create col

```python
df['algo'] = np.where(df['dt']>20220101, 1, 0)
```

## rename col

```python
df.rename(columns={'old1':'new1', 'old2':'new2'}, inplace=True)

cum_metric_col = ['cum_' + i for i in metric_col]
df.rename(columns=dict(zip(metric_col, cum_metric_col)))
```

## NA / NULL

```python
df.isnull().sum()
df.inv_cost.fillna(0, inplace=True)
resp = df.loc[df['price'].notnull()]
```

## rolling avg

```python
df['avg_P14D'] = df['y'].rolling(14).mean()
```

## sort

```python
df[df['id']==100].sort_values('time')
```

# table transform

## join

**merge (horizontal)**

```python
pd.merge(df1, df2, on=[], how='inner')
df1.join(df2, on=[], how='inner') # use index to join
```

**append (vertical)**

```python
pd.concat([df1, df2])
```

## groupby

```python
res = df.groupby(['id','type'])\
        .agg({'spend':sum, 'budget':sum, 'cnt':sum, 's':np.mean})\
        .reset_index()\
        .sort_values('id')
```

## pivot vs pivot_table

```python
df.pivot(index=['date', 'latency'], columns='series', values='metric')

pd.pivot_table(df, index=['date', 'latency'], columns='series', values='metric', aggfunc=np.mean).reset_index
```

## cumulative

```python
df_grp = df.groupby(groupby_cols)['cnt'].sum().reset_index()
df_grp['cum_cnt'] = df_grp['cnt'].cumsum()
df_grp['cum_p_cnt'] = df_grp['cum_cnt']/df_grp['cnt'].sum()
```

## transpose

```python
df.T
```

## Series to DataFrame

convert `pandas.core.series.Series` to `DataFrame`

```python
df['ds'].value_counts().to_frame()
```

## bucketize

```python
cutoff = [0,1,1.5,2,4,50]
spend['grp'] = pd.cut(spend['metric'], cutoff, include_lowest=True)
```

# datetime

```python
pd.to_datetime('2022-01-01')
df['date_obj'].dt.date
df['date_obj'].dt.strftime('%Y-%m-%d')
```

# json

```python
import json
import pandas as pd
from pandas.io.json import json_normalize

df = pd.read_csv('ss.csv', sep='\t', index_col = False)

with open('ssResult.json') as f:
    d = json.load(f)

res = json_normalize(d)
res.rename(columns={'event.bucket_name':'bucket', 'event.id':'id', 'timestamp':'dt', 'event.sum_revenue':'revenue', 'event.sum_amount':'amount'}, inplace=True)
res.drop(['version'], axis=1, inplace=True)
res['dt'] = res.dt.str.slice(0,10)
res['dt'] = res.dt.str.replace('-','')

res.groupby('dt')[['id']].size()
```

# [apply fn to row](https://towardsdatascience.com/apply-function-to-pandas-dataframe-rows-76df74165ee4)

use `%timeit $command` in jupyter cell for runtime

loop over all rows in df (56s)

```python
def loop_impl(df):
 cutoff_date = datetime.date.today() + datetime.timedelta(days=2)
 result = []
 for i in range(len(df)):
   row = df.iloc[i]
   result.append(
     eisenhower_action(
       row.priority == 'HIGH', row.due_date <= cutoff_date)
   )
 return pd.Series(result)
```

iterrows (9s)

```python
def iterrows_impl(df):
 cutoff_date = datetime.date.today() + datetime.timedelta(days=2)
 return pd.Series(
   eisenhower_action(
     row.priority == 'HIGH', row.due_date <= cutoff_date)
   for index, row in df.iterrows()
 )
```

itertuples (211ms)

itertuples processes rows as tuples.

```python
def itertuples_impl(df):
 cutoff_date = datetime.date.today() + datetime.timedelta(days=2)
 return pd.Series(
   eisenhower_action(
     row.priority == 'HIGH', row.due_date <= cutoff_date)
   for row in df.itertuples()
 )
```

apply (1.85s)

Pandas DataFrame apply function is quite versatile and is a popular choice. To make it process the rows, you have to pass axis=1 argument.

```python
def apply_impl(df):
 cutoff_date = datetime.date.today() + datetime.timedelta(days=2)
 return df.apply(
     lambda row:
       eisenhower_action(
         row.priority == 'HIGH', row.due_date <= cutoff_date),
     axis=1
 )
```

list comprehension (78ms)

A column in DataFrame is a Series that can be used as a list in a list comprehension expression. If multiple columns are needed, then zip can be used to make a list of tuples.

```python
[ foo(x) for x in df['x'] ]


def list_impl(df):
 cutoff_date = datetime.date.today() + datetime.timedelta(days=2)
 return pd.Series([
   eisenhower_action(priority == 'HIGH', due_date <= cutoff_date)
   for (priority, due_date) in zip(df['priority'], df['due_date'])
 ])
```

map (71ms)

Python’s map function that takes in function and iterables of parameters, and yields results.

```python
def map_impl(df):
 cutoff_date = datetime.date.today() + datetime.timedelta(days=2)
 return pd.Series(
   map(eisenhower_action,
     df['priority'] == 'HIGH',
     df['due_date'] <= cutoff_date)
 )
```

vectorization (20ms)

The real power of Pandas shows up in vectorization. But it requires unpacking the function as a vector expression.

```python
def map_impl(df):
 cutoff_date = datetime.date.today() + datetime.timedelta(days=2)
 return pd.Series(
   map(eisenhower_action,
     df['priority'] == 'HIGH',
     df['due_date'] <= cutoff_date)
 )
```

numpy vectorize (35ms)

NumPy offers alternatives for migrating from Python to Numpy through vectorization. For example, it has a vectorize() function that vectorzie any scalar function to accept and return NumPy arrays.

```python
def np_vec_impl(df):
 cutoff_date = datetime.date.today() + datetime.timedelta(days=2)
 return np.vectorize(eisenhower_action)(
   df['priority'] == 'HIGH',
   df['due_date'] <= cutoff_date
 )
```

Numba Decorators (19ms)

Numba is commonly used to speed up applying mathematical functions. It has various decorators for JIT compilation and vectorization.

```python
import numba
@numba.vectorize
def eisenhower_action(is_important: bool, is_urgent: bool) -> int:
 return 2 * is_important + is_urgent
def numba_impl(df):
 cutoff_date = datetime.date.today() + datetime.timedelta(days=2)
 return eisenhower_action(
   (df['priority'] == 'HIGH').to_numpy(),
   (df['due_date'] <= cutoff_date).to_numpy()
 )
```

Multiprocessing with pandarallel (2s)

The pandarallel package utilizes multiple CPUs and split the work into multiple threads.

```python
from pandarallel import pandarallel
pandarallel.initialize()
def pandarallel_impl(df):
  cutoff_date = datetime.date.today() + datetime.timedelta(days=2)
  return df.parallel_apply(
    lambda row: eisenhower_action(
      row.priority == 'HIGH', row.due_date <= cutoff_date),
    axis=1
  )
```

Parallelize with Dask (2s)

Dask is a parallel computing library that supports scaling up NumPy, Pandas, Scikit-learn, and many other Python libraries. It offers efficient infra for processing a massive amount of data on multi-node clusters.

```python
import dask.dataframe as dd
def dask_impl(df):
  cutoff_date = datetime.date.today() + datetime.timedelta(days=2)
  return dd.from_pandas(df, npartitions=CPU_COUNT).apply(
    lambda row: eisenhower_action(
      row.priority == 'HIGH', row.due_date <= cutoff_date),
    axis=1,
    meta=(int)
  ).compute()
```

Opportunistic Parallelization with Swifter (22ms)

Swifter automatically decides which is faster: to use Dask parallel processing or a simple Pandas apply. It is very simple to use: just all one word to how one uses Pandas apply function: df.swifter.apply.

```python
import swifter
def swifter_impl(df):
  cutoff_date = datetime.date.today() + datetime.timedelta(days=2)
  return df.swifter.apply(
    lambda row: eisenhower_action(
      row.priority == 'HIGH', row.due_date <= cutoff_date),
    axis=1
  )
```
