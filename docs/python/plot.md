```python
import numpy as np
import pandas as pd

import matplotlib.pyplot as plt
import matplotlib.ticker as mtick
from matplotlib import rcParams
%matplotlib inline

import seaborn as sns

import warnings
warnings.filterwarnings('ignore')
```

## pandas df

```python
df = pd.DataFrame({
    'name':['john','mary','peter','jeff','bill','lisa','jose'],
    'age':[23,78,22,19,45,33,20],
    'gender':['M','F','M','M','M','F','M'],
    'state':['california','dc','california','dc','california','texas','texas'],
    'num_children':[2,0,0,3,2,1,4],
    'num_pets':[5,1,0,5,2,2,3]
})
```

## scatter plot

```python
df.plot(kind='scatter', x='num_children', y='num_pets', color='red')
plt.show()
```

## bar chart

basic

```python
df.plot(kind='bar', x='name', y='age')
plt.show()
```

group by

```python
df.groupby('state')['name'].nunique().plot(kind='bar')
plt.show()
```

stacked bar plot w/ group by

```python
df.groupby(['state','gender']).size().unstack().plot(kind='bar', stacked=True)
plt.show()
```

stacked bar w/ group by (normalized %)

```python
df.groupby(['gender','state']).size().groupby(level=0).apply(
    lambda x: 100 * x / x.sum()
).unstack().plot(kind='bar',stacked=True)

plt.gca().yaxis.set_major_formatter(mtick.PercentFormatter())
plt.show()
```

## line chart

basic

```python
ax = plt.gca() # gca = get current axis
df.plot(kind='line',x='name',y='num_children',ax=ax)
df.plot(kind='line',x='name',y='num_pets', color='red', ax=ax)
plt.show()
```

group by

```python
fig, ax = plt.subplots(figsize=(12,5))
df.groupby(['total_latency','device'])['cnt'].sum().unstack()\
    .plot(kind='line', ax=ax)

pd.pivot_table(df, index='total_latency', columns='device_type',
               values='cnt').plot(subplots=True, figsize=(15,7))
```

## histogram

basic

```python
df[['age']].plot(kind='hist',bins=[0,20,40,60,80,100],rwidth=0.8)
plt.show()
```

date histogram

```python
df2 = pd.DataFrame({
    'name':[
        'john','lisa','peter','carl','linda','betty'
    ],
    'date_of_birth':[
        '01/21/1988','03/10/1977','07/25/1999','01/22/1977','09/30/1968','09/15/1970'
    ]
})

df2['date_of_birth'] = pd.to_datetime(df2['date_of_birth'],infer_datetime_format=True)

plt.clf()
df2['date_of_birth'].map(lambda d: d.month).plot(kind='hist')
plt.show()
```

## histogram with line

example 1

```python
# 1.) Necessary imports.    
import numpy as np
import matplotlib.pyplot as plt
from scipy.optimize import curve_fit

# 2.) Define fit function.
def fit_function(x, A, beta, B, mu, sigma):
    return (A * np.exp(-x/beta) + B * np.exp(-1.0 * (x - mu)**2 / (2 * sigma**2)))

# 3.) Generate exponential and gaussian data and histograms.
data = np.random.exponential(scale=2.0, size=100000)
data2 = np.random.normal(loc=3.0, scale=0.3, size=15000)
bins = np.linspace(0, 6, 61)
data_entries_1, bins_1 = np.histogram(data, bins=bins)
data_entries_2, bins_2 = np.histogram(data2, bins=bins)

# 4.) Add histograms of exponential and gaussian data.
data_entries = data_entries_1 + data_entries_2
binscenters = np.array([0.5 * (bins[i] + bins[i+1]) for i in range(len(bins)-1)])

# 5.) Fit the function to the histogram data.
popt, pcov = curve_fit(fit_function, xdata=binscenters, ydata=data_entries, p0=[20000, 2.0, 2000, 3.0, 0.3])
print(popt)

# 6.) Generate enough x values to make the curves look smooth.
xspace = np.linspace(0, 6, 100000)

# Plot the histogram and the fitted function.
plt.bar(binscenters, data_entries, width=bins[1] - bins[0], color='navy', label=r'Histogram entries')
plt.plot(xspace, fit_function(xspace, *popt), color='darkorange', linewidth=2.5, label=r'Fitted function')

# Make the plot nicer.
plt.xlim(0,6)
plt.xlabel(r'x axis')
plt.ylabel(r'Number of entries')
plt.title(r'Exponential decay with gaussian peak')
plt.legend(loc='best')
plt.show()
plt.clf()
```

example 2

```python
import matplotlib
import numpy as np
import matplotlib.pyplot as plt

np.random.seed(19680801)

# example data
mu = 100  # mean of distribution
sigma = 15  # standard deviation of distribution
x = mu + sigma * np.random.randn(437)

num_bins = 50

fig, ax = plt.subplots()

# the histogram of the data
n, bins, patches = ax.hist(x, num_bins, density=1)

# add a 'best fit' line
y = ((1 / (np.sqrt(2 * np.pi) * sigma)) *
     np.exp(-0.5 * (1 / sigma * (bins - mu))**2))
ax.plot(bins, y, '--')
ax.set_xlabel('Smarts')
ax.set_ylabel('Probability density')
ax.set_title(r'Histogram of IQ: $\mu=100$, $\sigma=15$')

# Tweak spacing to prevent clipping of ylabel
fig.tight_layout()
plt.show()
```

example 3

```python
rcParams['patch.force_edgecolor'] = True
#rcParams['patch.facecolor'] = 'b'

def plotHistwithLine(df, bin_size, title):
    
    deal = df.groupby(['d']).agg({'imp':sum, 'resp':sum}).reset_index()
    deal['win_rate'] = deal['imp'] / deal['resp']
    deal['win_rate'] = deal['win_rate'].apply(lambda x: 1 if x>1 else x) # take care cases of win_rate>1
    
    plt.figure(figsize=(8,6))
    
    ax = plt.gca()
    ax2= plt.twinx()
    
    sns.distplot(deal['win_rate'], ax=ax, hist=True, kde=False, bins=bin_size)
    sns.distplot(deal['win_rate'], ax=ax2, hist=False, kde=True, bins=bin_size, color='b')
    
    #plt.xlim(0,1)
    plt.title(title)
    ax.set_xlabel('% win rate')
    ax.set_ylabel('num of deals')
    #ax2.axes.get_xaxis().set_ticks([])
    #ax2.set_xticks([])
    #ax2.set_visible(False)
    
    plt.show()
```



## pairplots

```python
sns.pairplot(df.loc[:, df.dtypes=='int64'])
```

## correlation heatmaps

```python
corr = df.loc[:, df.dtypes=='int64'].corr()
sns.heatmap(corr, xticklabels=corr.columns, yticklabels=corr.columns, cmap=sns.diverging_palette(220, 10, as_cmap=True))
plt.show()
```

## boxplots

### data

```python
spread = np.random.rand(50) * 100
center = np.ones(25) * 50
flier_high = np.random.rand(10) * 100 + 100
flier_low = np.random.rand(10) - 100
data = np.concatenate((spread, center, flier_high, flier_low), 0)
data
```

basic

```python
plt.boxplot(data)
plt.show()
```

notched

```python
plt.boxplot(data, 1)
plt.show()
```

change outlier symbols

```python
plt.boxplot(data, 0, 'gD')
plt.show()
```

don't show outliers

```python
plt.boxplot(data, 0, '')
plt.show()
```

horizontal

```python
plt.boxplot(data, 0, 'rs', 0)
plt.show()
```

change change whiker length

```python
plt.boxplot(data, 0, 'rs', 0, 0.75)
plt.show()
```

### 2-D array

```python
spread = np.random.rand(50) * 100
center = np.ones(25) * 50
flier_high = np.random.rand(10) * 100 + 100
flier_low = np.random.rand(10) - 100
d2 = np.concatenate((spread, center, flier_high, flier_low), 0)
data.shape = (-1 ,1)
d2.shape = (-1, 1)
data2 = [data, d2, d2[::2, 0]]
```

basic

```python
plt.boxplot(data2)
plt.show()
```

## plot from pivot

data

```python
df = pd.DataFrame({
    'date': ['1/1/2020','1/2/2020','1/3/2020','1/4/2020','1/5/2020',
             '1/1/2020','1/2/2020','1/3/2020','1/4/2020','1/5/2020',
             '1/1/2020','1/2/2020','1/3/2020','1/4/2020','1/5/2020'],
    'SSP': ['SSP1','SSP1','SSP1','SSP1','SSP1',
            'SSP2','SSP2','SSP2','SSP2','SSP2',
            'SSP3','SSP3','SSP3','SSP3','SSP3'],
    'spend': [5, 2, 4, 2, 3,
              6, 0, 5, 2, 4,
              7, 3,5, 6, 2]
})
pv_df = df.pivot('date', columns='SSP', values='spend')
pv_df.reset_index()
```

lines in one graph

```python
pv_df.plot(figsize=(8,4), legend=True, grid=True, xticks=range(5), rot=10)
```

line per graph

```python
pv_df.plot(subplots=True, figsize=(8,6), legend=True, grid=True, xticks=range(5))
```

## subplots

example 1

```python
fig = plt.figure(figsize=(15,14))
ax1 = plt.subplot(2, 2, 1)
ax2 = plt.subplot(2, 2, 2)
ax3 = plt.subplot(2, 2, 3)
ax4 = plt.subplot(2, 2, 4)

df_sub.boxplot(column=['total_latency'], by='device', ax=ax1)
df_sub.boxplot(column=['total_latency'], by='media', ax=ax2)
df_sub[df_sub['total_latency']<200].boxplot(column=['total_latency'], by='device', ax=ax3)
df_sub[df_sub['total_latency']<200].boxplot(column=['total_latency'], by='media', ax=ax4)
plt.show()

# all in one plot 
fig, ax = plt.subplots(figsize=(12,5))
df_sub[df_sub['total_latency']<60].groupby(['total_latency', 'device'])['cnt'].sum().unstack().plot(kind='line', ax=ax)

# stacked plots
pd.pivot_table(df_sub[df_sub['total_latency']<60],
               index='total_latency', columns='device', values='cnt'
              ).plot(subplots=True, figsize=(15,7))
```

example 2

```python
fig, (ax) = plt.subplots(ncols=2, nrows=2, figsize=(18, 12))

sns.boxplot(x='dt',y='cnt',data=data[(df['group']=='group1')&(data['type']=='multi')], hue='host', palette="Blues", ax=ax[0,0])
sns.boxplot(x='dt',y='cnt',data=data[(df['group']=='group1')&(data['type']=='solo')], hue='host', palette="Blues", ax=ax[0,1])
sns.boxplot(x='dt',y='cnt',data=data[(df['group']=='group2')&(data['type']=='multi')], hue='host', palette="Blues", ax=ax[1,0])
sns.boxplot(x='dt',y='cnt',data=data[(df['group']=='group2')&(data['type']=='solo')], hue='host', palette="Blues", ax=ax[1,1])

ax[0,0].set_title('[group1] highlight')
ax[0,1].set_title('[group1] others')
ax[1,0].set_title('[group2] highlight')
ax[1,1].set_title('[group2] others')

ax[0,0].legend(loc='lower right')
ax[0,1].legend(loc='lower right')
ax[1,0].legend(loc='lower right')
ax[1,1].legend(loc='lower right')

fig.suptitle('traffic', fontsize=20)

plt.show()
```



## annotate

aggregation and annotation

```python
x = np.arange(5)
y = res['[total] SUM of inv_cost']['sum']
plt.bar(x,y)
for i, j in zip(x,y):
    plt.annotate(str(j),xy=(i,j))
plt.show()
```

## sankey diagram

[How To Create Sankey Diagrams from DataFrames in Python](https://medium.com/kenlok/how-to-create-sankey-diagrams-from-dataframes-in-python-e221c1b4d6b0)

[Sankey-view Documentation](https://readthedocs.org/projects/sankeyview/downloads/pdf/latest/)
