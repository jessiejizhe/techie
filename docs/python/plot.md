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

## color

sns color approx google suites

`sns_palette=['#007AFF', '#FFD966']`
- blue: #007AFF
- red: red
- yellow: #FFD966

## format

```python
from matplotlib.ticker import FuncFormatter, StrMethodFormatter, PercentFormatter
ax1.yaxis.set_major_formatter(StrMethodFormatter('${x:.2f}'))


def decimal_to_percentage(x, pos):
   return f"{x*100:.2f}%"
plt.gca().yaxis.set_major_formatter(FuncFormatter(decimal_to_percentage))
```

## scatter plot

basic

```python
df.plot(kind='scatter', x='num_children', y='num_pets', color='red')

df.plot.scatter(x='imp', y='ctr', c='goal', figsize=(12, 6), colormap='Set2')

plt.show()
```

grouped

```python
df['format_int2'] = pd.Categorical(df['format']).codes
df['format_int3'] = df['format_int2'].astype('category')


df.plot.scatter(x='imp', y='ctr', c='format_int3', figsize=(12, 6), colormap='Set2')
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

grouped

```python
df_melt = pd.melt(df_merge, id_vars=['display_surface'], value_vars=lst_value_vars)

# plot
plt.figure(figsize=(12, 6))
sns.set_style("whitegrid")

plots = sns.barplot(x='display_surface', y='value', hue='variable', data=df_melt, palette=['#007AFF','red','#FFD966'])

if type_str == 'pct':
    format_str = '.1%'
elif type_str == 'abs':
    format_str = ',.0f'
else:
    raise Exception('Please choose from {pct, abs}.')

for bar in plots.patches:
    plots.annotate(format(bar.get_height(), format_str),
                (bar.get_x() + bar.get_width() / 2,
                    bar.get_height()), ha='center', va='center',
                size=8, xytext=(0, 8),
                textcoords='offset points')

# plt.axhline(y=0, color='black', linewidth=0.8)

plt.title('[' + experiment + ' vs prod] '+type_str+' delta: VPV, Impressions, Revenue', fontsize=20, pad=40)
plt.xticks(fontsize=12)
plt.xlabel('')
if type_str == 'pct':
    plt.yticks(plt.yticks()[0], ['{:,.0%}'.format(x) for x in plt.yticks()[0]], fontsize=10)
else:
    plt.yticks(plt.yticks()[0], ['{:,.0f}'.format(x) for x in plt.yticks()[0]], fontsize=10)
plt.ylabel('')
plt.legend(loc='upper center', ncol=len(lst_value_vars), bbox_to_anchor=(0.5, 1.08))
plt.box(False)

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

example 3

```python
def plot_with_x_axis_cutoff(pv_imp, pv_cpm, dimension, cutoff):


   if dimension == 'imp_vol':
       title = 'surface imp volume'
   elif dimension == 'global_ad_position':
       title = 'session position'
   else:
       raise ValueError('can only choose from {imp_vol, global_ad_position}')

   fig, (ax1, ax2) = plt.subplots(1, 2, figsize = (20, 5))

   ax1.plot(pv_imp[pv_imp[dimension]<cutoff]['fbv'], label='fbv')
   ax1.plot(pv_imp[pv_imp[dimension]<cutoff]['igr'], label='igr')
   ax1.set_title('%imp x ' + title, fontsize = 12)
   ax1.set_xlabel(title, fontsize = 10)
   ax1.set_ylabel('%imp', fontsize = 10)
   ax1.legend()

   ax2.plot(pv_cpm[pv_cpm[dimension]<cutoff]['fbv'], label='fbv')
   ax2.plot(pv_cpm[pv_cpm[dimension]<cutoff]['igr'], label='igr')
   ax2.set_title('CPM x ' + title, fontsize = 12)
   ax2.set_xlabel(title, fontsize = 10)
   ax2.set_ylabel('CPM', fontsize = 10)
   ax2.legend()

   plt.suptitle('[Global] ' + title, fontsize = 20)
   plt.show()
```


## heatmap

```python
import seaborn as sns 

def plot_heatmap(df, date, title, subtitle, fmt=".0%"):
   """
   df = pv_res
   date = "(4/18/2024)"
   title = "FBR CPM vs IGR CPM Ratio"
   subtitle = "(raw FBR CPM) / (adj IGR CPM)"
   """
   sns.set_style("whitegrid")
   sns.set(font_scale=1)

   plt.figure(figsize=[10, 8])

   plt.suptitle(title + " " + date, fontsize=18, x=0.45)
   plt.title(subtitle, fontsize=12)

   # fmt='.2f', '.2%'
   ax = sns.heatmap(
       df,
       linewidths=0.5,
       annot=True,
       annot_kws={"fontsize": 12},
       fmt=fmt,
       cmap="Blues",
       cbar_kws={"shrink": 0.2},
       # mask=df.isnull(),
   )

   ax.set(xlabel="", ylabel="")
   ax.figure.axes[-1].yaxis.label.set_size(20)
   ax.invert_yaxis()


   plt.yticks(rotation=0)
   plt.show()

pv_res = pd.pivot_table(
   df,
   index="region",
   columns="age_bucket",
   values="user_pct",
   aggfunc=np.sum,
   sort=False,
)

for c in pv_res.columns:
   pv_res[c] = pv_res[c].astype(float).where(pv_res[c].notnull(),np.nan)

plot_heatmap(pv_res, date, title, subtitle, fmt=".0%")
```

## waterfall

```python
def plot_slide_4(res_rev, type_str, metric):

    """
    type_str: {abs, raw}
    """

   res_rev_total = res_rev[res_rev['surface']=='total']
   res_rev_waterfall = res_rev[res_rev['surface']!='total'].copy()
   res_rev_waterfall['display_surface'] = res_rev_waterfall.apply(rename_display_surface, axis=1)

   res_waterfall = res_rev_waterfall.groupby('display_surface').agg({'delta_abs_'+metric:'sum'}).reset_index()
   res_waterfall.loc[len(res_waterfall)] = ['Other', res_rev_total[['delta_abs_'+metric]].sum()[0] - res_waterfall[['delta_abs_'+metric]].sum()[0]]
   res_waterfall.loc[len(res_waterfall)] = ['Subtotal', res_rev_total[['delta_abs_'+metric]].sum()[0]]
   res_waterfall['delta_abs_'+metric+'_daily'] = res_waterfall['delta_abs_'+metric] / agg_day
   res_waterfall['delta_abs_'+metric+'_daily_text'] = ['{:,.0f}'.format(x) if x>=0 else '({:,.0f})'.format(-x) for x in res_waterfall['delta_abs_'+metric+'_daily']]

   total_delta_pct = res_bbr[res_bbr['surface']=='total']['delta_pct_'+metric].values[0]
   total_delta_daily = res_waterfall[res_waterfall['display_surface']=='Subtotal']['delta_abs_'+metric+'_daily'].values[0]
   res_waterfall['delta_abs_'+metric+'_daily_pct'] = res_waterfall['delta_abs_'+metric+'_daily'] / total_delta_daily * total_delta_pct
   res_waterfall['delta_abs_'+metric+'_daily_pct_text'] = ['{:.2%}'.format(x) if x>=0 else '({:.2%})'.format(-x) for x in res_waterfall['delta_abs_'+metric+'_daily_pct']]

   res_waterfall['idx'] = res_waterfall.apply(set_idx, axis=1)
   res_waterfall['measure'] = np.where(res_waterfall["display_surface"]=="Subtotal", "total", "relative")
   res_waterfall_plot = res_waterfall.sort_values('idx')

   if type_str == 'abs':
       plot_metric = 'delta_abs_'+metric+'_daily'
       text_metric = 'delta_abs_'+metric+'_daily_text'
   else:
       plot_metric = 'delta_abs_'+metric+'_daily_pct'
       text_metric = 'delta_abs_'+metric+'_daily_pct_text'

   ## plot waterfall
   fig = go.Figure()
   fig.add_trace(
       go.Waterfall(
           x = res_waterfall_plot['display_surface'],
           y = res_waterfall_plot[plot_metric],
           measure = res_waterfall_plot['measure'],
           text = res_waterfall_plot[text_metric],
           textposition = "outside",
           orientation = "v",
           decreasing = {"marker":{"color":"red"}},
           increasing = {"marker":{"color":"blue"}},
           totals = {"marker":{"color":"lightgrey"}},
           connector = {'line':{'color':'grey', 'dash':'dot'}},
           cliponaxis = False
       )
   )

   fig.add_hline(y=0, line_width = 1)
   fig.update_layout(
       title_text = "Revenue (" + metric.upper() + ") Daily Effect",
       title_x = 0.5,
       plot_bgcolor='white',
       autosize=False,
       width=1200,
       height=600
   )
   fig.update_yaxes(
       mirror=True,
       ticks='outside',
       gridcolor='lightgrey'
   )
   fig.show()

   return res_waterfall_plot
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

- [How To Create Sankey Diagrams from DataFrames in Python](https://medium.com/kenlok/how-to-create-sankey-diagrams-from-dataframes-in-python-e221c1b4d6b0)
- [Sankey-view Documentation](https://readthedocs.org/projects/sankeyview/downloads/pdf/latest/)

step 1: data

```python
def gen_data(df, left_col_source, right_col_target, metric):
  
   label = pd.DataFrame(list(set(df[left_col_source].to_list() + df[right_col_target].to_list())), columns=['column_name'])
   label = label.assign(row_number=range(len(label)))
   label_join_1 = label
   label_join_2 = label

   res = pd.merge(df, label_join_1, left_on=left_col_source, right_on='column_name', how = 'left')\
       .drop([left_col_source, 'column_name'], axis=1)\
       .rename(columns={'row_number': 'source_id'})
   res = pd.merge(res, label_join_2, left_on=right_col_target, right_on='column_name', how = 'left')\
       .drop([right_col_target, 'column_name'], axis=1)\
       .rename(columns={'row_number': 'target_id'})

   return label, res
```

step 2: plot

```python
import plotly.graph_objects as go
from plotly.offline import download_plotlyjs, init_notebook_mode, iplot, plot
init_notebook_mode(connected=True)


def plot_sankey(res, label, metric, bar_color):
   link = dict(
       source = res['source_id'],
       target = res['target_id'],
       value = res[metric])

   node = dict(
           pad = 15,
           thickness=20,
           line=dict(color='black', width=0.5),
           label = label['column_name'],
           color=bar_color
       )
   data = go.Sankey(link = link, node=node)
   # print(data)

   fig = go.Figure(data)
   fig.update_layout(
       autosize=False,
       width=800,
       height=1200,
       title_text = metric
   )
   fig.show()
```

step 3: call udf

```python
imp_label, imp_res = gen_data(df, 'Optimization Goal', 'Conversion Type', 'imp')
plot_sankey(imp_res, imp_label, 'imp', 'green')
```