---
title: terminologies
created: '2022-04-19T23:22:55.469Z'
modified: '2022-04-20T23:19:40.424Z'
---

# terminologies

1. Indexing
2. Partitioning
3. Shard
3. Chossing a partition key 
4. Types of partition and how they are done 
5. Vertical vs Horizontal scaling 
6. Column vs Row oriented Databases
    1. https://dataschool.com/data-modeling-101/row-vs-column-oriented-databases/
    2. https://en.wikipedia.org/wiki/Column-oriented_DBMS
7. Wire protocol
8. Refrential integrity
9. Conformity in db
10. Schema agnostic
11. Logical vs Physical Partitioning
12. Cosmosdb free 30 day subscription
      https://azure.microsoft.com/try/cosmosdb/



## Partitioning: 
- A partition is a division or splitting of a logical database or its constituent elements into distinct independent parts. 
- It is mainly done for better performance and availability or for load balancing.
- There are different criterias that current Relational Database Management Systems provide to split the database where they take a partition key and assign a partition based on certain creteria like: 
        
      1. Range partitioning: selects a partition by determining if the partitioning key is within a certain range. An example could be a partition for all rows where the "zipcode" column has a value between 70000 and 79999. It distributes tuples based on the value intervals (ranges) of some attribute. In addition to supporting exact-match queries (as in hashing), it is well-suited for range queries. For instance, a query with a predicate “A between A1 and A2” may be processed by the only node(s) containing tuples.


      2. List partitioning: a partition is assigned a list of values. If the partitioning key has one of these values, the partition is chosen. For example, all rows where the column Country is either Iceland, Norway, Sweden, Finland or Denmark could build a partition for the Nordic countries.

      3. Composite partitioning

      4. Hash partitioning etc.


Partitioning Methords: 

1. Horizontal partitioning: example, customers with ZIP codes less than 50000 are stored in CustomersEast, while customers with ZIP codes greater than or equal to 50000 are stored in CustomersWest. The two partition tables are then CustomersEast and CustomersWest, while a view with a union might be created over both of them to provide a complete view of all customers.

2. Vertical partitioning: normalization ka baap. which is generally used for olap db's. which stores data in column formate.
storing infrequently used or very wide columns, taking up a significant amount of memory. A common form of vertical partitioning is to split static data from dynamic data, since the former is faster to access than the latter, particularly for a table where the dynamic data is not used as often as the static. Creating a view across the two newly created tables restores the original table with a performance penalty, but accessing the static data alone will show higher performance. A columnar database can be regarded as a database that has been vertically partitioned until each column is stored in its own table.


## Shard
https://en.wikipedia.org/wiki/Shard_(database_architecture)



