# Cassandra Quick Reference
### The Data Model
* **The Cluster (aka The Ring)** is the outermost structure in Cassandra. It is organized as a virtual ring of nodes to which the data is distributed.
* **Keyspaces** are the outermost containers for data in Cassandra. Keyspace attributes include replication factor.
* **Tables** contain ordered collections of rows, each of which is an ordered collection of columns.
* **Primary Keys** uniquely identify rows in a table. Primary keys may be **simple** (one column) or **composite**. In this latter case, the first part (can also be more than one column) in the primary key acts as the **partition key** (responsible for data distribution in the cluster) and the rest are **clustering key** columns (responsible for data sorting within a partition - ASC/DESC). 
The partition key is the minimum specifier (in the *WHERE* clause) necessary when performing a query. This can then be followed by the clustering keys, necessarily in order!
* **Columns** are the most basic unit of data storage. Each column has a specific type. Every time a value is written to a column, it gets a **timestamp** assigned to it (can be specified explicitly). These are used for resolving conflicts. Each column value also has a **TTL** (*null* by default).
Another special type of column is the **static** column, which is shared by all the rows in the same partition. It is possible to update a static column in a statement which only contains a partition key but no clustering keys. This would not work for a normal column.
* **User-defined Types** are keyspace scoped.
* **Materialized views** were introduced as an alternative to secondary indexes, to support queries on columns that are not in the clustering key. The primary key of the view must include all columns in the primary key of the base table. The most common usage is to place the additional column first, followed by the primary key columns in the base table as clustering keys in the view.

### Data Modeling Considerations
* Make sure primary keys uniquely identify the units of data stored in the table, because they do uniquely identify rows. Otherwise, data can be overwritten inadvertently.
* To avoid creating partitions that are too large, consider adding another PK column to the partition key.
* Cassandra rows should not be used as queues. More generally, avoid uses of Cassandra that rely heavily on deletions. Deleted items are just tombstones that need to be scanned past. 

### The Architecture
In Cassandra, nodes are grouped in **racks** (machines in close proximity to each other, perhaps on the same rack of equipment) and **data centers** (a logical set of racks connected by reliable network, perhaps in the same building). The default configurations includes one data center and one rack. Cassandra uses this information to improve query efficiency and store copies in different data centers to maximize availability and partition tolerance.

Cassandra nodes use **gossip** (every second) to keep track of the state of the cluster. The Gossiper maintains a list of nodes that are alive and dead.

The **snitch** is used to determine relative host proximity and helps Cassandra route requests more efficiently (e.g. which replicas will return faster, when enforcing the consistency level). The default snitch (SimpleSnitch) is unaware of the topology (racks and datacenters).

Cassandra represents the data managed by a cluster as a **ring**, mapping every partition key to a **token** value. Tokens are part of a finite range and each part of the ring is owned by a node.

### References
 * Jeff Carpenter & Eben Hewitt - Cassandra: The Definitive Guide, 2nd (2016) (https://www.amazon.com/Cassandra-Definitive-Guide-Distributed-Scale/dp/1491933666)
