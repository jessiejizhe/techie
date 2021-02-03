## Spark

born at UC Berkeley, managed by Apache

### Advantages over MapReduce

- ~20 highly efficient distributed operations, any combination of them

- good for iterative algorithms, i.e. machine learning, by in-memory caching of data
- Native Python, Scala, R interfaces; interactive shells

### Architecture

- Master Node
  - Driver Program
    - Spark Context (object, gateway to connect spark instance and submit jobs)
  - Cluster Manager
    - 2 interfaces
      - YARN
      - Standalone
    - Provision/Restart Workers
- Worker Node
  - Spark Executor JVM (Java Virtual Machine) <--> HDFS

### RDD

Resilient Distributed Dataset: data containers (immutable)

**Dataset** are created from

- HDFS, S3, HBase, JSON, text, Local hierarchy of folders
- transforming another RDD

**Distributed**

- distributed across the cluster of machines
- divided in partitions, atomic chunks of data

**Resilient**

- Recover from errors, e.g. node failure, slow processes
- Track history of each partition, re-run

**Create RDD**

```python
integer_RDD = sc.parallelize(range(10), 3)
text_RDD =
sc.textFile("file:///home/cloudera/testfile1")
text_RDD =
sc.textFile("/user/cloudera/input/testfile1")
```

**check partitions**

```python
# Gather all data on the driver
integer_RDD.collect()
# Maintain splitting in partitions
integer_RDD.glom().collect()
```

**check data**

```python
# outputs the first line
text_RDD.take(1)
```

**wordcount**

```python
# map
def split_words(line):
	return line.split()

def create_pair(word):
	return (word, 1)

pairs_RDD = text_RDD.flatMap(split_words).map(create_pair)

# reduce
def sum_counts(a, b):
	return a + b

wordcounts_RDD = pairs_RDD.reduceByKey(sum_counts)
```

### Transformations

- RDD are immutable
- never modify RDD inplace
- transform RDD to another RDD
- transformations are lazy (nothing happens straight away)
- narrow vs wide
  - narrow
    - `map()`
    - `filter()`
  - wide
    - `groupByKey()`
    - `reduceByKey(func)`
    - `repartition(numPartitions) `

**Apply transformation**

- `map` applys function to each element of RDD, works on partition instead of on element

  ```python
  def lower(line):
  	return line.lower()
  
  lower_text_RDD = text_RDD.map(lower)
  ```

- `flatMap(func)` - map then flatten output

  ```python
  def split_words(line):
  	return line.split()
  
  words_RDD =
  text_RDD.flatMap(split_words)
  ```

- `filter(func)` - keep only elements where func is true

  ```python
  def starts_with_a(word):
  	return word.lower().startswith("a")
  
  words_RDD.filter(starts_with_a).collect()
  ```

- `sample(withReplacement, fraction, seed)` - get a random data fraction

- `coalesce(numPartitions)` - merge partitions to reduce them to numPartitions

  ```python
  sc.parallelize(range(10), 4).glom().collect()
  
  sc.parallelize(range(10), 4).coalesce(2).glom().collect()
  ```

- `groupByKey()` - wide transformations of (K, V) pairs to (K, iterable of all V) -- shuffle

  ```python
  pairs_RDD.groupByKey().collect()
  
  for k,v in pairs_RDD.groupByKey().collect():
  	print "Key:", k, ",Values:", list(v)
  ```
  
- `reduceByKey(func)` - wide transformation of  (K, V) pairs to (K, result of reduction by func on all V)
- `repartition(numPartitions)` : similar to coalesce, shuffles all data to increase or decrease number of partitions to numPartitions

**Shuffle**

- Global redistribution of data
- High impact on performance
- Process
  - write to local disk
  - requests data over the network

### DAG

Directed Acyclic Graph are used to **track dependencies** (also known as lineage or provenance).

**DAG in Spark**

- nodes are RDDs
- arrows are Transformations
- to recover lost partitions

### Actions

- Final stage of workflow
- Triggers execution of the DAG
- `collect()` and `take` return results to the Driver or writes to HDFS

**Examples**

- `collect()` - copy all elements to the driver
- `take(n)` - copy first n elements
- `reduce(func)` - aggregate elements with func (takes 2 elements, returns 1) 
- `saveAsTextFile(filename)` - save to local file or HDFS

### Memory Caching

- By default each job re-processes from HDFS
- Mark RDD with `.cache()`
- Lazy

**When to cache?**

- Generally not the input data
- Do validation and cleaning
- Cache for iterative algorithm

**How to cache?**

- Memory (most common)
- Disk (rare)
- Both (for heavy calculations)

**Speedup**

- Easily 10x or even 100x depending on application
- Caching is gradual
- Fault tolerant

### Broadcast

**Broadcast variables**

- Large variable used in all nodes
- Transfer just once per Executor
- Efficient peer-to-peer transfer
  - as soon as one node gets a chunk of the data, it will copy this variable by sharing its chunk of data with the other executor

```python
config = sc.broadcast({"order":3, "filter":True})
config.value
```

**Accumulator**

- Common pattern of accumulating to a variable across the cluster
- Write-only on nodes

```python
accum = sc.accumulator(0)

def test_accum(x):
	accum.add(x)

sc.parallelize([1, 2, 3, 4]).foreach(test_accum)
accum.value
```

