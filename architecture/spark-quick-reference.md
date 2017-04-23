# Spark Quick Reference

### Difference between shared memory vs. distributed data parallelism?
Both are data-parallel programming models.
  * Shared memory data parallelism refers to the context of a single node with a dataset in memory and multiple processes working in parallel on the same in-memory collection (and optionally combine when done). Scala has parallel collections with the same API as regular collections for implementing this.
  * Distributed data parallelism means the data is split over several nodes. Independent nodes (as opposed to threads) work on the data in parallel. Extra concern here: network latency.

### Main challenges in distributed data parallel applications and how Spark handles them
  * Partial failures (of some nodes). Spark handles this by keeping only immutable data in memory (as far as possible) and keeping track of transformations that may have failed and re-applying them.
  * Latency (some operations take longer e.g. due to network communication), which influences the programming model and how it is used. (cannot be ignored). Spark handles this by distinguishing between transformations and actions and keeping the data in memory instead of persisting to disk on each iteration.

### Spark’s main abstraction for implementing distributed data-parallelism
The RDD (Resilient Distributed Dataset) - immutable distributed collection.
They can be created in two main ways:
  * From the SparkContext / SparkSession (e.g. `textFile` or `parallelize`)
  * By transforming another RDD

### The two types of operations supported by RDDs
  * Transformations: returns a new RDD as a result. Examples: map, flatMap, filter, distinct.
  * Actions: returns some different type of result or saves it somewhere. Examples: collect, count, take, reduce, forEach.
Transformations are lazy and actions are eager. This helps Spark deal with latency.

### Transformations that combine two RDDs:
  * union
  * intersection
  * subtract
  * cartesian

### How Spark handles caching and why caching is needed
By default, RDDs are recomputed every time an action is run on them. This can be expensive in an iterative algorithm. To avoid this, once an RDD is computed we can call `persist()` or `cache()` to avoid recomputing the RDD.
`cache()` stores everything in memory as Java objects. `persist()` can be configured in terms of storage and format.

### How Spark programs are executed
There is a master and a bunch of workers. The node that acts as the master runs the Driver Program and has the SparkContext. 
The workers have executors and do the actual work and return results to the driver.
Between the two, there is a Cluster Manager, which does all the scheduling and resource management (e.g. YARN or Mesos).
The driver runs the main program and sends code to the executors.

### Pair RDDs
They are just RDDs with a type parameter that is a tuple/pair: RDD[(U,V)]. This is treated specially by Spark and supports extra operations per key: groupByKey, reduceByKey, join.
Why: complex data types often need to be projected down to key-value pairs.
Creating a pair RDD: from operations on other RDDs, such as map.

### Transformations and actions available on Pair RDDs
Transformations:
  * groupByKey: RDD[(K, Iterable[V])] - group all elements with the same key.
  * reduceByKey: ((V,V) => V): RDD[(K, V)] - much more efficient than groupByKey + reduce, because it does local reduction before shuffling.
  * mapValues: applies functions only to values (ignore the key)
  * countByKey: returns a Scala Map.
  * keys (not distinct, would need to be called explicitly)
  * (inner)join, leftOuterJoin, rightOuterJoin: work with 2 Pair RDDs with keys of the same type and values of possibly different types.

### reduceByKey is more efficient than groupByKey + reduce
reduceByKey: ((V,V) => V): RDD[(K, V)] - much more efficient than groupByKey + reduce, because it does local reduction before shuffling.

### Shuffling
It’s just moving the data around the network and it happens implicitly in some operations, like groupByKey.

### Partitioning
The data within an RDD is split into partitions and data in a partition is all on one machine. Each machine contains at least one partition (or more).
By default the number of partitions is equal to the total number of cores on the executor nodes.
Spark has two kinds of partitioning (custom partitioning is only available for Pair RDDs, because we partition based on keys):
Hash partitioning: key.hashCode() % numPartitions
Range partitioning: can be used when keys can be ordered
Some transformations preserve or change the partitioner of the parent RDD, and some transformations (like map and flatMap) produce an RDD without a partitioner. mapValues does preserve the partitioner, because it guarantees that the keys won’t be changed.

Why: Partitioning an RDD (+cache) can avoid shuffling: when grouping by key, no shuffle is needed because everything can be done locally. Also, calling join on 2 RDDs that are pre-partitioned with the same partitioning and cached can be done locally, with no shuffling.

### Narrow vs. Wide dependencies
A transformation has narrow dependencies when each partition of a parent RDD is only used by one partition of the child RDD. (no shuffle needed and pipelining is possible) (e.g. map, filter, union and even joins where a partitioning is already in place!)
A transformation has wide dependencies when a partition in the child RDD may be derived from multiple partitions in the parent RDD (this will require a shuffle). (e.g. groupByKey or join when its inputs are not already partitioned)

### Spark SQL
Just a library on top of Spark. It adds 3 main APIs (SQL, Dataframes and Datasets) and two specialized backend components: 
  * Catalyst: query optimizer: Compiles Spark SQL programs (e.g. using Dataframes) down to RDDs. Can optimize (e.g. reordering, or not transferring all data, just columns and skipping partitions that are not needed) because it knows about all possible data types and operations.
  * Tungsten: off-heap serializer. Highly specialized encoders (because it knows all data types). 


### Dataframes
An API on top of normal Spark RDDs. Allows for writing queries declaratively and allows optimizations (because it only supports some operations: select, agg, groupBy, join...all these are (lazy) transformations).
Limitations: 
  * Elements in Dataframes are untyped (the Scala compiler cannot check types, Spark does). Errors are caught only at run-time.
  * Data has to be expressed as case classes or data types that Spark SQL knows.

### Datasets
They combine the optimizations of DataFrames with the type safety of RDDs. (we can freely mix APIs).
(type DataFrame = Dataset[RDD])
