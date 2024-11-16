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

explore tables

```python
df.columns
df.dtypes
```

column statistics

```python
df.describe()
df['$column_name'].describe()
df.describe(percentiles=[0, 1/3, 2/3, 1])
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


# data types
for c in df.columns:
   df[c] = df[c].astype('str')


# add new column with row numbers
df = df.assign(row_number=range(len(df)))


# convert varchar/string/category to int
df['format_int2'] = pd.Categorical(df['format']).codes
df['format_int3'] = df['format_int2'].astype('category')




df.plot.scatter(x='imp', y='ctr', c='goal', figsize=(12, 6), colormap='Set2')


region_dic =  {
 'east': 1,
 'oregon':2,
 'sweden' : 3}
region_mapping = {v:k for k,v in region_dic.items()}
df.rename(columns = lambda x: x.strip('pre_'), inplace=True)




def rename(x):
   if x == 2922:
       return 'control'
   elif x == 8256:
       return 'no_surplus'
   elif x == 8257:
       return 'MRCU_RCU_RATIO_9'
   elif x== 8258:
       return 'MRCU_RCU_RATIO_11'
   elif x== 8259:
       return 'MRCU_RCU_RATIO_13'
   elif x== 8260:
       return 'MRCU_RCU_RATIO_15'


exp['version'] = exp['rack_version'].apply(lambda x: rename(x))
mrcu['version'] = mrcu['rack_version'].apply(lambda x: rename(x))


def rename_region(row):
   if row['region'] == 'US and Canada':
       return 'US/CA'
   elif row['region'] == 'Western Europe':
       return 'WE'
   elif row['region'] == 'South and Central Asia':
       return 'SCA'
   elif row['region'] == 'Southeast Asia':
       return 'SEA'
   else:
       return 'RoW'


df['region2'] = df.apply(rename_region, axis=1)
df_imp['quarter'] = df_imp['date_id'].apply(lambda x: rename_date_id(x))
```

## inser value

```python
df.at[4, 'B'] = 10
```

## NA / NULL

```python
df.isnull().sum()
df.inv_cost.fillna(0, inplace=True)
resp = df.loc[df['price'].notnull()]

# np.nan == np.nan returns FALSE
df[i] = df[i].apply(lambda x:np.nan if (x is pd.NA or x==r'' or x is None) else x)
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

```python
# merge (horizontal)
pd.merge(df1, df2, on=[], how='inner')
df1.join(df2, on=[], how='inner') # use index to join


# append (vertical)
pd.concat([df1, df2])


# cross join
data1 = {'A': [1, 2]}
data2 = {'B': ['a', 'b', 'c']}
df1 = pd.DataFrame(data1, index =[0, 1])
df2 = pd.DataFrame(data2, index =[2, 3, 4])
df1['key'] = 1
df2['key'] = 1
result = pd.merge(df1, df12, on='key').drop("key", 1)
```

## groupby

```python
res = df.groupby(['id','type'])\
        .agg({'spend':sum, 'budget':sum, 'cnt':sum, 's':np.mean})\
        .reset_index()\
        .sort_values('id')


# print in certain format
df = df_account.groupby(['surface','format']).agg({col_ctr: ['mean', 'min'], col_cpm: ['mean', 'min']}).reset_index()
display(df.style.format({
   (col_ctr, 'mean'): '{:.2%}',
   (col_ctr, 'min'): '{:.2%}',
   (col_cpm, 'mean'): '${:.2f}',
   (col_cpm, 'min'): '${:.2f}'})
)


df.groupby(lambda x: True).agg().reset_index()


res = ad.groupby('campaign_id').size().reset_index(name='counts')
res[res['counts']>1].head()


# column pct
df['rev_share'] = df.groupby(['group'])['total_legal_revenue_1_day'].transform(lambda x: x/x.sum())

columns_to_compute = res.columns[1:]
   for col in columns_to_compute:
       df[col] = df[col] / df[col].sum()
   display(df.style.format('{:.0%}'))


# row pct (temporary transform)
pct = df.T.iloc[1:].apply(lambda x: x / x.sum(), axis=0)
pct.rename(columns={0: '1683', 1: '1687', 2: '1688'}, inplace=True)
display(res_pct.T.style.format('{:.0%}'))
# or hardcode inside
pct['pct_display'] = pct['pct'].apply(lambda x: f'{x:.0%}')


# cumulative
df_grp = df.groupby(groupby_cols)['cnt'].sum().reset_index()
df_grp['cum_cnt'] = df_grp['cnt'].cumsum()
df_grp['cum_p_cnt'] = df_grp['cum_cnt']/df_grp['cnt'].sum()
```

## column
```python
# data types
for c in df.columns:
   df[c] = df[c].astype('str')


# add new column with row numbers
df = df.assign(row_number=range(len(df)))


# convert varchar/string/category to int
df['format_int2'] = pd.Categorical(df['format']).codes
df['format_int3'] = df['format_int2'].astype('category')


df.plot.scatter(x='imp', y='ctr', c='goal', figsize=(12, 6), colormap='Set2')


region_dic =  {
 'east': 1,
 'oregon':2,
 'sweden' : 3}
region_mapping = {v:k for k,v in region_dic.items()}
```

## pivot vs pivot_table

```python
df.pivot(index=['date', 'latency'], columns='series', values='metric')

pd.pivot_table(df, index=['date', 'latency'], columns='series', values='metric', aggfunc=np.mean).reset_index
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
