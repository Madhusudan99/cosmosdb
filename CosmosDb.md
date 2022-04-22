---
title: CosmosDb
created: '2022-04-19T16:08:00.417Z'
modified: '2022-04-20T22:49:34.268Z'
---

# CosmosDb

Why Cosmos or any NoSQL db?
- Needs to be highly responsive and always online
- Low Latency 
- High Availability
- scaling, high availability, geo-replication, multiple write locations, automatic and transparent shard management, transparent replication between operational and analytical stores, and more.



- Need applications that respond in real time to large changes in usage/fluctuation.
- Is able to store large amounts of data and can also deliver a response to the user in near single digit milliseconds time
- Microsoft backs this by an SLA(Service Level Aggrement). And it also guarantees this speed at any scale.


- Turn Key multi region data distribution 




- Choose from multiple database APIs including the native Core (SQL) API, API for MongoDB, Cassandra API, Gremlin API, and Table API.
- Change feed makes it easy to track and manage changes to database containers and create triggered events with Azure Functions.
- Azure Cosmos DB's schema-less service automatically indexes all your data, regardless of the data model, to deliver blazing fast queries.

    What is Index? or Indexing?? which results in faster queries



### Considerations when choosing an API
- Core(SQL) API is native to Azure Cosmos DB.
- Microsoft has full control over the interface, service, and the SDK client libraries. Any new feature that is rolled out to Azure Cosmos DB is first available on SQL API accounts.

API for MongoDB, Cassandra, Gremlin, and Table implement the wire protocol of open-source database engines. These APIs are best suited if the following conditions are true:

1. If you have existing MongoDB, Cassandra, or Gremlin applications.
2. If you don't want to rewrite your entire data access layer.





### Capacity planning when migrating data
Trying to do capacity planning for a migration to Azure Cosmos DB API for SQL or MongoDB from an existing database cluster? You can use information about your existing database cluster for capacity planning.

If all you know is the number of vcores and servers in your existing sharded and replicated database cluster, read about estimating request units using vCores or vCPUs.

If you know typical request rates for your current database workload, read about estimating request units using Azure Cosmos DB capacity planner for SQL API and API for MongoDB




### Why NoSQL? 
If you are managing data whose structures are constantly changing at a high rate, particularly if transactions can come from external sources where it is difficult to enforce conformity across the database, you may want to consider a more schema-agnostic approach using a managed NoSQL database service.


### Azure cosmos containers
A container is horizontally partitioned and then replicated across multiple regions.
A container is a schema-agnostic container of items. Items in a container can have arbitrary schemas. For example, an item that represents a person and an item that represents an automobile can be placed in the same container. By default, all items that you add to a container are automatically indexed without requiring explicit index or schema management. You can customize the indexing behavior by configuring the indexing policy on a container.


### Logical Partioning 
In partitioning, the items in a container are divided into distinct subsets called logical partitions. Which is formed based on the value of a partition key that is associated with each item in a container.
Also note that all the items will have the same partition key value.


Logical partitions
A logical partition consists of a set of items that have the same partition key. For example, in a container that contains data about food nutrition, all items contain a foodGroup property. You can use foodGroup as the partition key for the container. Groups of items that have specific values for foodGroup, such as Beef Products, Baked Products, and Sausages and Luncheon Meats, form distinct logical partitions.

A logical partition also defines the scope of database transactions. You can update items within a logical partition by using a transaction with snapshot isolation. When new items are added to a container, new logical partitions are transparently created by the system. You don't have to worry about deleting a logical partition when the underlying data is deleted.

There is no limit to the number of logical partitions in your container. Each logical partition can store up to 20GB of data. Good partition key choices have a wide range of possible values. For example, in a container where all items contain a foodGroup property, the data within the Beef Products logical partition can grow up to 20 GB. Selecting a partition key with a wide range of possible values ensures that the container is able to scale.

You can use Azure Monitor Alerts to monitor if a logical partition's size is approaching 20 GB.

### Physical Partitions
- A container is scaled by distributing data and throughput across physical partitions.
- Usually one or more logical partitions are mapped to a single physical partition.
- Smaller containers have one or more logical partitions but they only require a single physical partiotion
- Physical partitions are completly internally managed by cosmos itself

- Provisioned throughput is the maximum amount of capacity that an application can consume from a table or index. If your application exceeds your provisioned throughput capacity on a table or index, it is subject to request throttling. Throttling prevents your application from consuming too many capacity units.

Factors that affect the number of physical partitions:
1. If the current throughtput is more than 10,000 RU/s of provisioned throughtput.
    1.  each individual physical partition can provide a throughput of up to 10,000 request units per second). The 10,000 RU/s limit for physical partitions implies that logical partitions also have a 10,000 RU/s limit, as each logical partition is only mapped to one physical partition.  
2. If your current partitions size becomes larger than 50 GB(Physical Partitions have a Limit of 50GB).

## Physical partitions are an internal implementation of the system and they are entirely managed by Azure Cosmos DB. When developing your solutions, don't focus on physical partitions because you can't control them. Instead, focus on your partition keys. If you choose a partition key that evenly distributes throughput consumption across logical partitions, you will ensure that throughput consumption across physical partitions is balanced.

There is no limit to the total number of physical partitions in your container. As your provisioned throughput or data size grows, Azure Cosmos DB will automatically create new physical partitions by splitting existing ones. Physical partition splits do not impact your application's availability. After the physical partition split, all data within a single logical partition will still be stored on the same physical partition. A physical partition split simply creates a new mapping of logical partitions to physical partitions.

### If most of your workload's requests are queries and most of your queries have an equality filter on the same property, this property can be a good partition key choice. For example, if you frequently run a query that filters on UserID, then selecting UserID as the partition key would reduce the number of cross-partition queries.

However, if your container is small, you probably don't have enough physical partitions to need to worry about the performance impact of cross-partition queries. Most small containers in Azure Cosmos DB only require one or two physical partitions.

If your container could grow to more than a few physical partitions, then you should make sure you pick a partition key that minimizes cross-partition queries. Your container will require more than a few physical partitions when either of the following are true:

Your container will have over 30,000 RU's provisioned

Your container will store over 100 GB of data


### In-partition query
When you query data from containers, if the query has a partition key filter specified, Azure Cosmos DB automatically optimizes the query. It routes the query to the physical partitions corresponding to the partition key values specified in the filter.

For example, consider the below query with an equality filter on DeviceId. If we run this query on a container partitioned on DeviceId, this query will filter to a single physical partition.

      SELECT * FROM c WHERE c.DeviceId = 'XMS-0001'

As with the earlier example, this query will also filter to a single partition. Adding the additional filter on Location does not change this:


      SELECT * FROM c WHERE c.DeviceId = 'XMS-0001' AND c.Location = 'Seattle'


Here's a query that has a range filter on the partition key and won't be scoped to a single physical partition. In order to be an in-partition query, the query must have an equality filter that includes the partition key:
      SELECT * FROM c WHERE c.DeviceId > 'XMS-0001'

