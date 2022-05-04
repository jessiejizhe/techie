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

## create table

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

## read data

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

## write data

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

```python
df_reduced = df.loc[df['bid_price']>0]
df[df['deal_types'].str.contains('PROGRAMMATIC_GUARANTEED')]
df[~df['deal_types'].str.contains('PROGRAMMATIC_GUARANTEED')]
```

## NA / NULL

```python
df.isnull().sum()
df.inv_cost.fillna(0, inplace=True)
resp = df.loc[df['bid_price'].notnull()]
```

## create column

```python
df['algo'] = np.where(df['dt']>20170719, 1, 0)
```

## rename

```python
df.rename(columns={'old1':'new1', 'old2':'new2'}, inplace=True)

cum_metric_col = ['cum_' + i for i in metric_col]
df.rename(columns=dict(zip(metric_col, cum_metric_col)))
```

## merge (horizontal)

```python
pd.merge(df1, df2, on=[], how='inner')
df1.join(df2, on=[], how='inner') # use index to join
```

## append (vertical)

```python
pd.concat([df1, df2])
```

## sort

```python
df[df['line_id']==297455].sort_values('datestamp')
```

# data operation

## groupby

```python
res = df.groupby(['line_id','algo'])\
        .agg({'spending_local':sum, 'budget_local':sum, 'clicks':sum, 'goal_CPC':np.mean})\
        .reset_index()\
        .sort_values('line_id')
```

## pivot vs pivot_table

```python
df.pivot(index=['date', 'latency'], columns='series', values='metric')
pd.pivot_table(df, index=['date', 'latency'], columns='series', values='metric', aggfunc=np.mean).reset_index
```

# feature engineering

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

# reformat

## transpose

```python
df.T
```

## convert pandas.core.series.Series to DataFrame

```python
df['ds'].value_counts().to_frame()
```

# style

## suppress scientific notation
