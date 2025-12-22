# Performance and Cost Optimization Concepts

## Explain the use of the Query Profile

The Query Profile is an essential tool in Snowsight for understanding and optimizing query performance. When you run a query, you can open its profile from the Query History page to see a detailed breakdown of how Snowflake executed it.

### Explain plans

The query profile displays the query execution plan as a graphical tree of operators. Each node in the tree represents an operation, such as a table scan, join, or aggregation. By analyzing the plan, you can understand the steps Snowflake took to execute your query and identify potential bottlenecks, such as a node that is taking a long time to execute.

### Data spilling

Data spilling occurs when a virtual warehouse does not have enough memory to hold the intermediate results of a query. When this happens, Snowflake will "spill" the data to the warehouse's local SSD, and in some cases, to remote cloud storage. Spilling is a performance bottleneck because disk I/O is much slower than memory access. The query profile will clearly indicate if an operator has spilled to local or remote disk, which is a sign that you might need to use a larger warehouse.

### Use of the data cache

The query profile shows how effectively the query used the warehouse's data cache (also known as the warehouse cache or SSD cache). If you see a high percentage of data being read from the cache, it means that Snowflake was able to avoid re-reading data from remote storage, which results in better performance.

### Micro-partition pruning

Micro-partition pruning is one of the most important aspects of Snowflake's performance. The query profile shows you exactly how effective the pruning was for each table scan. It will display the total number of micro-partitions in the table and how many were actually scanned. If you see that a query is scanning a large number of partitions, it might be an indication that you need to add a clustering key to the table.

### Query history

The Query History page in Snowsight is where you can find all the queries that have been run in your account. From this page, you can filter and search for specific queries and then click on a query to open its Query Profile.

## Explain virtual warehouse configurations

### Types of warehouses

Snowflake offers two main types of virtual warehouses:

- **Standard Warehouse:** This is the default type and is suitable for most general-purpose query workloads.
- **Snowpark-optimized Warehouse:** This type of warehouse has larger memory and is specifically designed for workloads that use Snowpark, which often involve more memory-intensive operations.

### Multi-clustering warehouses

Multi-cluster warehouses are a feature of the Enterprise edition and above. They allow you to automatically scale out your compute resources to handle high concurrency.

#### Scaling policies

- **Standard:** This policy prioritizes starting new clusters quickly to handle the increased load. This is the default and is best for workloads with unpredictable query volumes.
- **Economy:** This policy conserves credits by trying to make full use of the running clusters before starting new ones. It will wait longer to start a new cluster, which can save money but may result in some queries being queued.

#### Scaling modes

- **Maximized:** This mode starts all clusters up to the maximum you've defined, so the warehouse can handle the maximum level of concurrency right away.
- **Auto-scale:** This is the default mode, where the warehouse will automatically start and stop clusters as the query load increases and decreases.

### Warehouse sizing

Warehouses come in T-shirt sizes (X-Small, Small, Medium, etc.). The size of the warehouse determines the amount of compute resources it has. A larger warehouse has more memory and CPU, which can improve the performance of complex queries. The general recommendation is to start with a smaller size and then increase the size if you are seeing performance issues like data spilling.

### Warehouse settings and access

- **`AUTO_SUSPEND`:** This is a crucial setting for cost control. It automatically suspends a warehouse after a specified period of inactivity, so you are not paying for compute when it's not being used.
- **`AUTO_RESUME`:** This setting automatically resumes a suspended warehouse when a new query is submitted to it.
- **Access:** Access to warehouses is controlled by the `USAGE` privilege, which can be granted to roles.

## Outline virtual warehouse performance tools

### Monitoring warehouse loads

Snowsight provides a set of dashboards for monitoring your warehouse load. You can see the number of queries that are running, the number of queries that are queued, and the overall utilization of the warehouse. This is essential for understanding if your warehouse is properly sized for your workload.

### Scaling up compared to scaling out

- **Scaling Up:** This means increasing the size of a single warehouse (e.g., from a Small to a Medium). You should scale up when you have complex, long-running queries that need more memory or CPU.
- **Scaling Out:** This means adding more clusters to a multi-cluster warehouse. You should scale out when you have a high number of concurrent users and queries.

### Query acceleration service

The Query Acceleration Service (QAS) is a feature that can dramatically improve the performance of large, ad-hoc queries. It works by offloading parts of the query workload to serverless compute resources provided by Snowflake. This can be a cost-effective way to boost performance for occasional large queries without having to run a larger warehouse all the time.

## Optimize query performance

### Describe the use of materialized views

A materialized view is a pre-computed dataset that is stored as a table. If you have a complex query that is run frequently, you can create a materialized view for it. Then, when you query the view, you are simply querying the pre-computed results, which is much faster than re-executing the complex query every time. Snowflake automatically keeps the materialized view up-to-date as the data in the underlying base table changes.

### Use of specific SELECT commands

A simple but effective optimization is to only select the columns you actually need, instead of using `SELECT *`. This reduces the amount of data that needs to be read from storage and transferred over the network.

### Clustering

As discussed before, defining a clustering key for a large table can significantly improve query performance by improving micro-partition pruning. A well-clustered table will allow Snowflake to scan far fewer micro-partitions to answer a query.

### Search optimization service

The Search Optimization Service is a feature that builds a persistent data structure (a search access path) on a table to speed up point-lookup queries (i.e., queries that return a small number of rows). It's particularly useful for dashboards and other interactive applications where you need fast lookups on large tables.

### Persisted query results

This refers to Snowflake's result cache, which is explained below.

### Understanding the impact of different types of caching

Snowflake has multiple layers of caching that work together to improve query performance:

#### Metadata cache

This cache is part of the Cloud Services layer and stores metadata about your tables, such as the number of micro-partitions and the range of values for each column. This allows Snowflake to quickly prune partitions without having to scan them.

#### Result cache

This cache stores the results of every query that is executed. If you or someone else runs the exact same query again and the underlying data has not changed, Snowflake will simply return the results from the cache, which is instantaneous and uses no compute credits.

#### Warehouse cache

This is a cache of table data that is stored on the SSDs of your virtual warehouse. When a query needs to access data, it will first check the warehouse cache. If the data is there, it can be read much faster than from remote cloud storage.

## Describe cost optimization concepts and best practices in Snowflake

### Understanding and exploring the costs of different Snowflake features and services

#### Cost insights feature in Snowsight

Snowsight has built-in dashboards that allow you to visualize and explore your Snowflake costs. You can see your costs broken down by compute, storage, and serverless features, and you can drill down to see the costs of individual warehouses and services.

#### Use of different table types and sizes

You can optimize storage costs by using `TRANSIENT` tables for temporary or staging data. Transient tables do not have a Fail-safe period, so their storage costs are lower.

#### Use of views

Standard views do not have any storage costs associated with them. However, materialized views do incur both storage and compute costs for maintaining the view.

#### Use of search optimization paths

The Search Optimization Service has both a storage cost for the search access path and a compute cost for building and maintaining it.

#### Storage costs

This is the cost of storing your data in Snowflake. It's typically charged per terabyte per month.

#### Compute costs

This is the cost of running your virtual warehouses. It's charged in Snowflake credits per second, with a 60-second minimum.

### Understand and explore cloud services costs in Snowflake

The Cloud Services layer provides all the management and coordination services for your Snowflake account. The use of this layer is free up to 10% of your daily compute credits. If your cloud services usage exceeds this amount, you will be charged for the overage.

### Costs considerations when using serverless features

Features like Snowpipe, Automatic Clustering, and the Search Optimization Service run on serverless compute resources managed by Snowflake. These features have their own pricing models and will appear as separate line items on your bill.

### Cost considerations when moving data among regions

#### Replication

Replicating a database to another region has a storage cost for the replicated data in the secondary account, as well as a data transfer cost for the data that is moved between regions.

#### Fail-over

In a failover scenario, you will incur compute costs in the secondary account as it becomes the primary and starts running your workloads.

### Monitor and control costs

#### Resource monitors

Resource monitors are a critical tool for controlling your compute costs. You can create a resource monitor to track the credit usage of one or more warehouses. You can configure the monitor to send an alert or to automatically suspend the warehouse when it reaches a certain threshold of credit consumption.

#### Snowflake Budgets service

The Budgets service in Snowsight is a newer feature that allows you to set a monthly spending budget for your account and track your actual spend against it.

### Attribute costs

#### Cost center tagging

You can use Snowflake's object tagging feature to attach tags to your warehouses and other objects. For example, you could tag each warehouse with the name of the department that uses it. You can then use the `TAGS` column in the `ACCOUNT_USAGE` views to attribute costs to different cost centers.

#### Use of the ACCOUNT USAGE schema

The `ACCOUNT_USAGE` schema provides a rich set of views for analyzing your costs in detail. You can query views like `WAREHOUSE_METERING_HISTORY`, `STORAGE_USAGE`, and `SERVERLESS_TASK_HISTORY` to get a granular understanding of your Snowflake spending.
