## cell execution time

```python
%%time
sum(range(1000000))
```

or

```python
%timeit $command
```

## [Built-in magic commands](https://ipython.org/ipython-doc/3/interactive/magics.html)

current working directory path

```python
%pwd
```

print the most recent 1-3 commands in the current session

```python
%history -n 1-3
```

what classes are available

```python
%config
```

