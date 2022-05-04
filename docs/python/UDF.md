# optional arg

```python
def foo(a, b = None):
    if b is not None:
        print(a+b)
    else:
        print(a)

foo(3)    #3
foo(3, 5) #8
```

