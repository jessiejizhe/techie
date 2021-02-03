# Hadoop Framework Basic Modules

- **Common** (libraries)
- **HDFS** (file system)
- **YARN** (resource manager & job scheduler)
- **MapReduce** (programming model to scale data across different processes)

# Improvements

## MapReduce -> YARN

Separate resource management and job scheduling/monitoring.

**YARN improvements**

- global ResourceManager
- NodeManager on each node
- ApplicationMaster per application
- High Availability ResouceManager
- Timeline Server
- Use of Cgroups
- Secure Containers
- web services REST APIs

## HDFS -> HDFS2

HDFS has single NameNode and multiple DataNodes.

**HDFS2 improvements**

- HDFS Federation benefits
  - increased namespace scalability
  - performance
  - isolation
- Multiple Namenode servers
- Multiple namespaces
- High Availability (redundant NameNodes)
- Heterogeneous Storage and Archival Storage (ARCHIVE, DISK, SSD, RAM_DISK)

## Hadoop -> Spark

Spark is multi-stage in-memory programming

Hadoop is 2-stage disk based map reduce programming

Spark requires a cluster management and a distributed storage system.

# Hadoop Frameworks

supports in-memory caching data

## Tez

- Dataflow graphs
- Custom data types
- Can run complex DAG of tasks
- Dynamic DAG changes
- Resource usage efficiency

## Spark

- Advanced DAG execution engine
- Supports cyclic data flow (good for machine learning)
- In-memory computing
- Java, Scala, Python, R
- Existing optimized libraries

# Hadoop Resource Scheduling

Schedulers are by default - FIFO (queue).

## Fairshare Scheduler

- try to balance out the resource allocation across applications over time
- Balances out resource allocation among apps over time
- Can organize into queues/sub-queues 
- Guarantee minimum shares
- Limits per user/app
- Weighted app priorities

## Capacity Scheduler

- guaranteed capacity for each application or group, and there are safeguards to prevent a user or an application from taking down the whole cluster by running it out of resources
- queues and sub-queues
- Capacity Guarantee with elasticity
- ACLs for security
  - An **ACL** (access control list) provides a way to set different permissions for specific named users or named groups, not only the file's owner and the file's group.
- Runtime changes/draining apps
- Resource based scheduling

# Hadoop-Based Applications

## Databases / Stores

### avro
data structures within context of Hadoop MapReduce jobs

### Hbase
- Scalable data store
- Non-relational distributed database
- Runs on top of HDFS
- Compression
- In-memory operations: MemStore, BlockCache
- Features
  - Consistency
  - High Availability
  - Automatic Sharding
  - Replication
  - Security
  - SQL like access (Hive, Spark, Impala)

### Cassandra
distributed  data management system

## Querying

### Pig
- Platform for data processing, good for ETL
- Components
  - Pig Latin: High level language
  - infrastructure layer
- Execution environment
  - local
  - MapReduce
  - Tez
- Extensible (can write custom functions)

### Hive
- Data warehouse software
- HiveQL: SQL like language to structure and query data
- Data in HDFS, HBase
- Execution environment
  - MapReduce
  - Tez
  - Spark
- Custom mappers/reducers
- Table and storage management
  - Beeline
  - Hive command line interface (CLI)
  - HCatalog
  - webHcat (REST API for HCatalog)

### Impala

### Spark

## Machine Learning / Graph Processing

### Giraph

### Mahout

### Spark
