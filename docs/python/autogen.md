# generate query string

add prefix
```python
lst_col = ['productA', 'productB']
lst_p_col [ 'p_'+c for c in lst_col]
lst_avg_col [ 'avg_'+c for c in lst_col]
```

list to string
```python
lst_groupby_col = ['productA', 'productB']
str_groupby_col = ', '.join(lst_groupby_col)
```

generate corresponding groupby query
```python
col_len = len(lst_groupby_col)
groupby_n = ", ".join(list(map(str, range(1, col_len))))
```