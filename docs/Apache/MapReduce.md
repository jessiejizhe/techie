## MapReduce

A layer of software to help you bring computation to the data and organize the output

### Framework

- user defines
  - <key, value> pair 
  - mapper & reducer functions
- Hadoop handles the logistics: shuffle, group, distribute

### Flow

- User defines a map function `map()`
- `map()` reads data and outputs `<key,value>`
- User defines a reduce function `reduce()`
- `reduce()` reads `<key,value>` and outputs result 

### Principle

- In general
  - 1 mapper per data split (typically)
  - 1 reducer per computer core (best parallelism)
- Composite <keys>
- Extra info in <values>
  Cascade Map/Reduce jobs
- bin keys into ranges to reduce computational cost
  - N keys into R groups
  - if size (N/R) increases
  - shuffle cost increases
  - reducer complexity decreases
- Aggregate map output when possible (combiner option)

### Joining Data

Combine datasets by key

- A standard data management function
- Joins can be inner, left or right outer

### Summary

- Task Decomposition
  - mappers are separate and independent
  - mappers work on data parts
- Common mappers
  - Filter (subset data)
  - Identity (just pass data)
  - Splitter (as for counting)

### Limitations
  - Must fit <key, map> paradigm
  - Map/Reduce data not persistent
  - Requires programming/debugging
  - Not interactive
  - Force pipeline into Map and Reduce steps (cannot accommodate map-reduce-map .etc)
  - Read from disk for each MapReduce job (bad for iterative algorithms, i.e. machine learning)
