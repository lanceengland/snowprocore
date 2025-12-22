# Snowflake AI Data Cloud Features and Architecture

## Outline key features of the Snowflake Data Cloud

### Interoperable stage

A stage in Snowflake is a location where data files are stored for loading into or unloading from Snowflake tables. Stages are essential for interoperability, allowing Snowflake to seamlessly connect with a wide range of external cloud storage solutions. There are two main types of stages:

- **Internal Stages:** These are stages where data is stored securely within your Snowflake account. Snowflake manages the underlying storage for these stages. Internal stages are a good option when you need to stage data before loading it into Snowflake and don't want to manage your own cloud storage.
- **External Stages:** These are stages that point to data stored in your own cloud storage accounts on Amazon S3, Google Cloud Storage, or Microsoft Azure. By using external stages, you can load data into Snowflake directly from your existing cloud storage, and unload data from Snowflake to your cloud storage. This makes Snowflake highly interoperable with your existing data lake and cloud environment.

### Elastic compute

Elastic compute in Snowflake is delivered through a feature called Virtual Warehouses. A virtual warehouse is a cluster of compute resources (CPU, memory, and temporary storage) that executes your queries, data loading, and other DML operations. The key "elastic" aspect comes from the ability to:

- **Scale Up/Down:** You can resize a warehouse at any time to a larger or smaller size (T-shirt sizes like X-Small, Small, Medium, Large, etc.). This is useful when you need more processing power for a complex query, and then you can scale it down to save costs.
- **Scale In/Out:** You can configure a multi-cluster warehouse that automatically scales out by adding more clusters of the same size to handle concurrent query workloads. As the number of concurrent queries increases, the warehouse will automatically add clusters up to a maximum you define. When the query load decreases, it will automatically scale in by shutting down the extra clusters.

This separation of storage and compute allows you to pay for compute only when you are using it, and to scale your compute resources independently of your storage.

### Snowflake's layers

Snowflake's architecture is a hybrid of traditional shared-disk and shared-nothing architectures. It consists of three distinct layers:

1. **Database Storage:** This is the foundation of Snowflake, where all your data is stored. Snowflake takes the data you load and organizes it into its internal, compressed, columnar format, stored in the cloud storage of the cloud provider where your Snowflake account is hosted (AWS, Azure, or GCP). This layer is designed for scalability, resilience, and efficiency.
2. **Query Processing (Compute):** This layer is where the actual query execution happens. It's composed of the virtual warehouses described above. Each virtual warehouse is an independent compute cluster that doesn't share compute resources with other virtual warehouses. This layer is what makes Snowflake's elastic compute possible.
3. **Cloud Services:** This is the "brain" of Snowflake. This layer is a collection of services that coordinate activities across Snowflake. These services include authentication, infrastructure management, metadata management, query parsing and optimization, and access control. The services layer is what makes Snowflake a fully managed service, as it handles all the complex tasks of managing a database system.

### Overview of Snowflake editions

Snowflake offers several editions, each providing a different level of features and services. The main editions are:

- **Standard:** This is the introductory edition, offering all of Snowflake's standard features, including the unique architecture, virtual warehouses, and Snowpipe.
- **Enterprise:** This edition provides all the features of the Standard edition, plus additional features for large-scale enterprises, such as multi-cluster warehouses for high concurrency, and the ability to create materialized views.
- **Business Critical:** This edition includes all the features of the Enterprise edition, but with additional data protection features for organizations that handle highly sensitive data. These features include support for HIPAA and PCI compliance, and failover/failback for business continuity.
- **Virtual Private Snowflake (VPS):** This is the highest level of Snowflake, providing the most comprehensive security and isolation. With VPS, you get a completely separate Snowflake environment, with no shared resources with any other Snowflake customers.

## Outline key Snowflake tools and interfaces

### Snowsight

Snowsight is the modern web interface for Snowflake. It's a powerful tool that allows you to:

- Write and run SQL queries.
- Create and manage database objects like tables, views, and warehouses.
- Visualize query results with charts and dashboards.
- Monitor query history and performance.
- Manage your Snowflake account and users.
- Access and use Snowpark, Streamlit, and other Snowflake features.

### SnowSQL

SnowSQL is the command-line client for connecting to Snowflake. It's a powerful tool for both interactive and batch-based SQL operations. You can use SnowSQL to:

- Run ad-hoc queries.
- Execute SQL scripts.
- Load data from files into Snowflake tables.
- Unload data from Snowflake tables to files.
- Automate database tasks.

### Snowflake connectors

Snowflake provides a wide range of connectors that allow you to connect your favorite tools and applications to Snowflake. These connectors are built and maintained by Snowflake and its partners. Some of the most popular connectors include:

- **Spark Connector:** Allows you to read data from and write data to Snowflake in your Apache Spark applications.
- **Python Connector:** Provides a Python interface to Snowflake, allowing you to execute queries and manage database objects from your Python scripts.
- **Kafka Connector:** Enables you to stream data from Apache Kafka topics directly into Snowflake tables.

### Snowflake drivers

Snowflake provides drivers for various programming languages and data-access interfaces, allowing you to build your own applications that connect to Snowflake. These drivers include:

- **JDBC Driver:** For Java-based applications.
- **ODBC Driver:** For applications that use the Open Database Connectivity standard.
- **.NET Driver:** For applications built on the .NET framework.
- **Go Driver:** For applications written in the Go programming language.
- **Node.js Driver:** For server-side JavaScript applications.

### Snowpark

Snowpark is a developer framework for Snowflake that allows you to query and process data in Snowflake using your favorite programming languages like Python, Java, and Scala. With Snowpark, you can build complex data pipelines and applications that run natively in Snowflake, leveraging Snowflake's elastic compute engine. Snowpark provides a DataFrame-style API that is similar to what you might find in tools like Apache Spark or pandas.

### SnowCD

SnowCD (Snowflake Connectivity Diagnostic) is a command-line tool that helps you diagnose and troubleshoot your network connection to Snowflake. It runs a series of tests to check for things like network latency, DNS resolution, and firewall issues. SnowCD is a useful tool to have when you're experiencing connectivity problems with Snowflake.

### Streamlit in Snowflake

Streamlit is an open-source Python library that makes it easy to create beautiful, custom web apps for machine learning and data science. With Streamlit in Snowflake, you can build and deploy Streamlit apps that run natively in Snowflake. This allows you to create interactive data applications that are securely connected to your data in Snowflake, without having to manage any additional infrastructure.

### Cortex (AI/ML services)

Snowflake Cortex is a set of managed AI and machine learning services that are built into Snowflake. These services allow you to leverage the power of AI and ML without having to be a machine learning expert. Some of the services offered by Cortex include:

- **LLM Functions:** Let you use large language models (LLMs) directly in your SQL queries.
- **Forecasting:** Allows you to generate forecasts for your time-series data.
- **Anomaly Detection:** Helps you identify unusual patterns and outliers in your data.
- **Contribution Explorer:** Helps you discover the key drivers behind your business metrics.

### Snowflake SQL API

The Snowflake SQL API is a REST API that allows you to execute SQL statements in Snowflake. This is useful for building custom applications and integrations that need to interact with Snowflake programmatically. With the SQL API, you can:

- Submit SQL statements for execution.
- Check the status of the execution.
- Retrieve the results.

## Outline Snowflake's catalog and objects

### Databases

A database in Snowflake is a logical grouping of schemas, tables, views, and other objects. You can have multiple databases in your Snowflake account. Databases are the top-level organizational unit for your data.

### Stages

As mentioned earlier, a stage is a location for storing data files. Stages can be internal (managed by Snowflake) or external (in your own cloud storage). They are used for loading data into and unloading data from Snowflake tables.

### Schema types

A schema is a logical grouping of database objects like tables and views within a database. Snowflake supports three types of schemas:

- **Permanent:** These are the standard schemas that persist until they are explicitly dropped.
- **Transient:** These schemas are similar to permanent schemas, but they are not automatically recovered in the event of a failure. This makes them a good option for temporary or staging data, as they have lower storage costs.
- **Temporary:** These schemas are session-based, meaning they are automatically dropped at the end of the session in which they were created.

### Table types

Snowflake supports several types of tables:

- **Permanent:** The default table type. These tables persist until explicitly dropped and have a 7-day Time Travel retention period (up to 90 days for Enterprise edition).
- **Transient:** These tables persist until dropped, but have no Time Travel beyond 24 hours and no Fail-safe. They are useful for temporary data that doesn't need the same level of protection as permanent tables.
- **Temporary:** These tables are session-specific and are dropped at the end of the session.
- **External:** These tables allow you to query data stored in files in an external stage (S3, Azure Blob, GCS) as if it were a regular table in Snowflake.
- **Iceberg:** These tables are a special type of external table that uses the Apache Iceberg table format, enabling new use cases like table evolution and time travel on your data lake.

### View types

A view is a saved query that can be queried like a table. Snowflake has three types of views:

- **Standard (or non-materialized) Views:** The query that defines the view is executed each time the view is queried.
- **Materialized Views:** The result of the view's query is stored as a table and is automatically updated as the underlying base table changes. Materialized views can improve query performance for complex queries.
- **Secure Views:** These are designed for data privacy. They prevent users from seeing the underlying query and can be used to expose only certain rows or columns of a table to specific users.

### Data types

Snowflake supports a rich set of SQL data types, including:

- **Numeric:** `NUMBER`, `INTEGER`, `FLOAT`, `DECIMAL`
- **String:** `VARCHAR`, `STRING`, `TEXT`
- **Binary:** `BINARY`, `VARBINARY`
- **Logical:** `BOOLEAN`
- **Date and Time:** `DATE`, `TIME`, `TIMESTAMP`
- **Semi-structured:** `VARIANT`, `ARRAY`, `OBJECT`
- **Geospatial:** `GEOGRAPHY`, `GEOMETRY`

### User Defined Functions (UDFs)

A UDF is a user-defined function that can be called in SQL queries. UDFs allow you to extend Snowflake's functionality by writing your own custom logic. Snowflake UDFs can be written in:

- **SQL:** For simple, scalar functions.
- **JavaScript:** For more complex logic.
- **Java:** For computationally intensive logic that can be packaged as a JAR file.
- **Python:** For leveraging the rich ecosystem of Python libraries.

### User Defined Table Functions (UDTFs)

A UDTF is a user-defined function that returns a set of rows, essentially behaving like a table. UDTFs can be written in SQL, JavaScript, Java, or Python and are useful for more complex data transformations and processing.

### Stored procedures

A stored procedure is a set of SQL statements and procedural logic that is stored in the database. Stored procedures can be called from SQL and can be used to encapsulate complex business logic and to automate repetitive tasks. Snowflake stored procedures can be written in SQL (using Snowflake Scripting), JavaScript, Java, or Python.

### Streams

A stream is an object that captures change data capture (CDC) information for a table. It records all inserts, updates, and deletes made to a table, allowing you to take action on the changed data. Streams are a powerful tool for building data pipelines that process only the new or changed data.

### Tasks

A task is a scheduled SQL statement or stored procedure. Tasks allow you to automate your data pipelines and other repetitive tasks in Snowflake. You can create a schedule for your tasks to run at a specific time or interval. Tasks can be chained together to create complex workflows.

### Pipes

A pipe is an object that defines a continuous data ingestion process from an external stage. Snowpipe, the service that uses pipes, automatically loads new data files as they arrive in the stage into a target table. This is a highly efficient and scalable way to load streaming data into Snowflake.

### Shares

A share is a named Snowflake object that encapsulates all of the information required to share a database. Shares are the fundamental object that enables Snowflake's Secure Data Sharing. You can share databases with other Snowflake accounts, and even with non-Snowflake users through reader accounts.

### Sequences

A sequence is a database object that generates a sequence of unique numbers. Sequences are often used to generate primary key values for tables.

## Outline Snowflake storage concepts

### Micro-partitions

Micro-partitions are the fundamental unit of storage in Snowflake. When you load data into Snowflake, it's automatically divided into small, immutable micro-partitions, typically 50-500MB of uncompressed data. For each micro-partition, Snowflake stores metadata about the data within it, such as the range of values for each column. This metadata is what allows Snowflake to perform very efficient query pruning, where it only scans the micro-partitions that are relevant to a query.

### Data clustering

Data clustering is the process of co-locating related data within the same or nearby micro-partitions. By clustering your data, you can significantly improve query performance, as Snowflake will have to scan fewer micro-partitions to retrieve the data for your queries. You can define a clustering key for a table, and Snowflake will automatically maintain the clustering of the data as you load new data into the table.

### Data storage monitoring

Snowflake provides several ways to monitor your data storage usage. You can use the `ACCOUNT_USAGE` schema to query views that provide detailed information about your storage usage, such as:

- `TABLE_STORAGE_METRICS`: For monitoring storage used by tables.
- `STORAGE_USAGE`: For an account-level overview of storage usage.

You can also use the Snowsight web interface to visualize your storage usage over time. Monitoring your storage is important for understanding your costs and for identifying opportunities to optimize your storage.
