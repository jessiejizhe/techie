## Hadoop Framework Basic Modules

- **Common** (libraries)
- **HDFS** (file system)
- **YARN** (resource manager & job scheduler)
- **MapReduce** (programming model to scale data across different processes)

## Improvements

### MapReduce -> YARN

Separate resource management and job scheduling/monitoring.

YARN improvements:

- global ResourceManager
- NodeManager on each node
- ApplicationMaster per application
- High Availability ResouceManager
- Timeline Server
- Use of Cgroups
- Secure Containers
- web services REST APIs

### HDFS -> HDFS2

HDFS has single NameNode and multiple DataNodes.

HDFS2 improvements:

- HDFS Federation benefits
  - increased namespace scalability
  - performance
  - isolation
- Multiple Namenode servers
- Multiple namespaces
- High Availability (redundant NameNodes)
- Heterogeneous Storage and Archival Storage (ARCHIVE, DISK, SSD, RAM_DISK)

### Hadoop -> Spark

Spark is multi-stage in-memory programming

Hadoop is 2-stage disk based map reduce programming

Spark requires a cluster management and a distributed storage system.