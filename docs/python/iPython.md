## cell execution time

```python
import time
start_time = time.time()

### bgn code run ###
### end code run ###

end_time = time.time()

# in seconds
runtime = end_time - start_time
print(f"Runtime: {runtime:.0f} seconds")

# in minutes
runtime_min = (end_time - start_time) / 60
print(f"Runtime: {runtime_min:.2f} minutes")


%%time
sum(range(1000000))


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

