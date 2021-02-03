## HDFS

### Overview
**Design Concept**

- Scalable distributed filesystem
- Distribute data on local disks on several nodes
- Low cost commodity hardware

**Design Factors**

- Hundreds/Thousands of nodes
- Portability across heterogeneous hardware/software
- Handle large data sets
- High throughput

**Approach**

- Simplified coherency model – write once read many
- Data Replication – helps handle hardware failures
- Move computation close to data
- Relax POSIX requirements – increase throughput
  - POSIX - Portable Operating System Interface, an IEEE standard designed to facilitate application portability

**Summary**

Single NameNode

- a master server that manages the file system namespace and regulates access to files by clients

Multiple DataNotes

- typically one per node in the cluster
- Functions
  - Manage storage
  - Serving read/write requests from clients
  - Block creation, deletion, replication based on instructions from NameNode

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

### Write Process

- write process is initiated by a client
- Data gets cached on the client
- NameNode contacted once a block of data is accumulated
- NameNode responds with list of DataNodes
  - NameNode is Rack aware
  - **Rack awareness** is the knowledge of network structure(topology). i.e. location of different data node across the Hadoop cluster.
- 1stDataNode receives data, writes to local and forwards to 2nd DataNode ...
- NameNode commits file creation into persistent store.
- NameNode receives heartbeat and block reports from all DataNodes.

### Read Process

- Client gets DataNode list from NameNode
- Read from replica closest to reader

### Performance Envelope

**HDFS block size**

- default block size is 64MB
- good for large files

**Importance of #blocks in a file**

- NameNode memory usage
  - Every block represented as object (default replication this will be further increased 3X)
- Number of map tasks
  - data typically processed block at a time

**A lot of #small files**

- Impact on NameNode
  - Memory usage
    - ~150 bytes per object
    - 1 billion objects => 300GB memory!
  - Network load
    - Number of checks with DataNodes proportional to number of blocks
- Performance Impact
  - Map tasks
    - depends on #blocks
    - 10GB of data, 32k file size => 327680 map tasks
    - ⇒lots of queued tasks
    - ⇒large overhead of spin up/tear down for each task (latency)
    - ⇒Inefficient disk I/O with small sizes

**Large files**

- lots of small files is bad! 
- Solutions
  - Merge/Concatenate files
  - Sequence files
  - HBase, HIVE configuration
  - CombineFileInputFormat
    - optimizes maps

### Tuning Parameters

Parameters in `hdfs-site.xml`

**Block Size**

- default 64MB
- typically bumped up to 128MB
- parameter: dfs.blocksize, dfs.block.size

**Replication**

- default is 3
- parameter: dfs.replication
- tradeoffs
  - Lower it to reduce replication cost, less robust
  - Higher replication can make data local to more workers

**node count**

**map task**

**[Full list](http://hadoop.apache.org/docs/current/hadoop-project-dist/hadoop-hdfs/hdfs-default.xml)**

- dfs.datanode.handler.count (10): Sets the number of server threads on each datanode.
- dfs.namenode.fs-limits.max-blocks-per-file: Maximum number of blocks per file.

### Robustness

NameNode receives heartbeat and block reports from DataNodes

**Replication trade off w.r.t robustness**

- Might lose a node or local disk during the run – cannot recover if there is no replication.
- If there is data corruption of a block from one of the DataNodes – again cannot recover without replication.

### Common Failures & Mitigations

**Common Failures**

- DataNode Failures: Server can fail, disk can crash, data corruption.
- Network Failures
- NameNode Failures: Disk failure, node failure

**Mitigation of Common Failures**

- Periodic heartbeat: from DataNode to NameNode. For DataNodes without recent heartbeat
  - Marked dead, no new IO sent
  - Blocks below replication factor re-replicated on other nodes.
- Data Corruption
  - Checksum computed on file creation
  - Checksums stored in HDFS namespace.
  - Used to check retrieved data, re-read from alternate replica if need.
  - Multiple copies of central meta data structures.
- Failover to standby NameNode – manual by default.

### HDFS Access

**[HDFS command](https://hadoop.apache.org/docs/current/hadoop-project-dist/hadoop-hdfs/HDFSCommands.html)**

- invoked via bin/hdfs script
- user commands - filesystem shell commands
- administrator commands
- debug commands

**HDFS NFS Gateway**

- Mount HDFS as a filesystem on the client
- Browse files using regular filesystem commands
- Upload/download files from HDFS
- Stream data to HDFS

**Apache Flume**

collecting, aggregating streaming data and moving into HDFS

**Apache Sqoop**

Bulk transfers between Hadoop and datastores.

### HDFS APIs

#### Native Java API for HDFS**

- **Base class**: org.apache.hadoop.fs.FileSystem

- **Important classes**
  - FSDataInputStream
    - read : read bytes
    - readFully : read from stream to buffer
    - seek: seek to given offset
    - getPos: get current position in stream
  - FSDataOutputStream
    - getPos: get current position in stream
    - hflush: flush out the data in client's user buffer
    - close: close the underlying output stream
- **Methods**: get, open, create

**Reading from HDFS using API**

- get an instance of FileSystem

  `FileSystem fs = FileSystem.get(URI.create(uri),conf);`

- Open an input stream

  `in = fs.open(new Path(uri));`

- Use IO utilities to copy from input stream

  `IOUtils.copyBytes(in, System.out,4096,false);`

- Close the stream

  `IOUtils.closeStream(in)`

**Writing to HDFS using API**

- get an instance of FileSystem 

  `FileSystem fs = FileSystem.get(URI.create(outuri),conf);`

- Create a file

  `out = fs.create(new Path(outuri));`

- Write to output stream

  `out.write(buffer, 0, nbytes);`

- Close the file

  `out.close();`

#### C API for HDFS

libhdfs, header file (hdfs.h)

#### WebHDFS REST API

Enabling WebHDFS in `hdfs-site.xml`

- dfs.webhdfs.enabled
- dfs.web.authentication.kerberos.principal
- dfs.web.authentication.kerberos.keytab

HTTP Operations

- HTTP GET: file status, checksums, attributes
- HTTP PUT: create, change ownership, rename, permissions,snapshot
- HTTP POST: append, concat
- HTTP DELETE: Delete files, snapshot
