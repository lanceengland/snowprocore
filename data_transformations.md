# Data Transformations

## Explain how to work with structured data

### Estimation functions

For very large datasets, calculating exact values for things like the number of distinct values in a column can be time-consuming and expensive. Snowflake provides a set of estimation functions (also known as HyperLogLog functions) that provide approximate results with a high degree of accuracy, but in a fraction of the time. A common example is `APPROX_COUNT_DISTINCT`, which is much faster than `COUNT(DISTINCT ...)`.

### Sampling

Sampling is a technique used to select a random subset of data from a table. This is extremely useful for data exploration and analysis on very large tables, as it allows you to get a quick understanding of the data without having to query the entire table.

### SAMPLE command

The `SAMPLE` clause can be added to a `SELECT` query to retrieve a random sample of data from the specified table. For example: `SELECT * FROM my_table SAMPLE (10);`

### /TABLESAMPLE command

`TABLESAMPLE` is an alternative syntax for the `SAMPLE` clause. They are functionally equivalent. For example: `SELECT * FROM my_table TABLESAMPLE (10);`

### Sampling methods

Snowflake provides two main methods for sampling:

#### Fraction-based

This method, also known as `BERNOULLI` or `ROW` sampling, includes each row of the table in the sample with a specified probability. For example, `SAMPLE ROW (10)` would give each row a 10% chance of being included in the result. This method provides a more random sample but can be slower as it has to scan the entire table.

#### Fixed-size

This method, also known as `SYSTEM` or `BLOCK` sampling, samples a specified number of data blocks (micro-partitions). For example, `SAMPLE SYSTEM (10)` would randomly select 10% of the micro-partitions and return all the rows from those blocks. This method is much faster as it doesn't need to scan the entire table, but the sample may be less random if the data is clustered.

### Supported function types

Snowflake provides a rich set of functions for data transformation:

#### System functions

These are the hundreds of built-in functions provided by Snowflake for performing a wide variety of operations on data, such as string manipulation, date and time calculations, and mathematical operations.

#### Table functions

Table functions are functions that return a set of rows, essentially behaving like a table in a query. Common examples include `FLATTEN` for parsing semi-structured data, and `SPLIT_TO_TABLE` for splitting a string into rows.

#### External functions

External functions allow you to call your own custom code that is hosted outside of Snowflake (e.g., in an AWS Lambda function or an Azure Function). This is a powerful feature that allows you to extend Snowflake's capabilities with your own custom logic written in any language.

#### User-Defined Functions (UDFs)

UDFs are functions that you write and store in Snowflake. They can be used to perform custom data transformations that are not available through the built-in functions. UDFs can be written in SQL, JavaScript, Java, or Python.

### Stored procedures

Stored procedures allow you to encapsulate complex, multi-step data transformations into a single, callable unit. They can include conditional logic, error handling, and can execute multiple SQL statements. Stored procedures are ideal for automating complex business logic and data processing workflows.

### Streams

Streams are a powerful tool for change data capture (CDC). A stream on a table will record all the inserts, updates, and deletes that occur on that table. You can then query the stream to see only the changed data, which is a fundamental building block for creating efficient ELT/ETL pipelines. For example, you can use a stream to identify new or updated rows and then merge them into a target table.

### Tasks

Tasks are used to schedule the execution of SQL statements or stored procedures. You can use tasks to automate your data transformation pipelines. For example, you could create a task that runs every hour, checks a stream for new data, and then calls a stored procedure to process that data.

## Explain how to work with semistructured data

### Supported data formats, data types, and sizes

Snowflake is uniquely designed to handle semi-structured data. It supports popular formats like JSON, Avro, ORC, Parquet, and XML. This data is loaded into a special data type called `VARIANT`, which can store any of these formats up to a size of 16MB per `VARIANT` value. Snowflake also has `ARRAY` and `OBJECT` data types for working with arrays and objects explicitly.

### VARIANT column

The `VARIANT` data type is the cornerstone of semi-structured data handling in Snowflake. It allows you to load semi-structured data directly into a table without having to define a schema upfront. Snowflake automatically parses the data and stores it in an efficient, optimized format.

### Flattening the nested structure

Often, you will need to convert your nested semi-structured data into a flat, tabular format for analysis. This process is called flattening.

#### FLATTEN command

`FLATTEN` is a table function that takes a `VARIANT`, `OBJECT`, or `ARRAY` column and produces a lateral view, meaning it creates a new row for each element in the array or each key-value pair in the object.

#### LATERAL FLATTEN command

The `LATERAL` keyword is used in conjunction with the `FLATTEN` function. A `LATERAL` join allows a subquery in the `FROM` clause to reference columns from a preceding table in the same `FROM` clause. This is how you join the result of the `FLATTEN` function back to the original table.

### Semi-structured data functions

Snowflake provides a rich set of functions for working with semi-structured data:

#### ARRAY/OBJECT creation and manipulation

You can construct arrays and objects directly in SQL using functions like `ARRAY_CONSTRUCT` and `OBJECT_CONSTRUCT`. There are also functions for manipulating them, such as `ARRAY_APPEND`, `ARRAY_CAT`, and `OBJECT_INSERT`.

#### Extracting values

You can easily extract values from a `VARIANT` column using a colon (`:`) for object keys and square brackets (`[]`) for array elements. For example, `my_variant_column:address.city` or `my_variant_column:orders[0]`. You can also use the `GET_PATH` function to extract values using a path expression.

#### Type predicates

Snowflake provides a set of `IS_` functions to check the data type of a value within a `VARIANT`, such as `IS_ARRAY`, `IS_OBJECT`, `IS_VARCHAR`, `IS_REAL`, etc. These are useful for validating and handling different data types within your semi-structured data.

## Explain how to work with unstructured data

### Define and use directory tables

A directory table is a feature that adds file-level metadata to a stage. When you enable directory tables on a stage, Snowflake automatically creates a manifest of all the files in that stage, including a special `FILE_URL` column that you can use to reference individual files. This allows you to treat your unstructured files like rows in a table.

### SQL file functions

Snowflake provides several functions for working with the files in a stage:

- `GET_PRESIGNED_URL`: Generates a temporary, downloadable URL for a file in a stage.
- `GET_ABSOLUTE_PATH`: Returns the path of a staged file, which can be used as an argument to UDFs and stored procedures.

### Types of URLs used to access data files

There are three main types of URLs used to access unstructured data files in Snowflake:

- **Scoped URL:** A temporary URL that can be used to access a file for a limited time. It's automatically generated by Snowflake when needed.
- **File URL:** A permanent URL that represents the path to a file within a stage. It can be used to identify a specific file for processing.
- **Pre-signed URL:** A downloadable URL that can be shared with users or applications outside of Snowflake to allow them to download a file directly from cloud storage.

### Processing unstructured data

Snowflake allows you to process unstructured data (like PDFs, images, or audio files) directly within the platform.

#### User-Defined Functions (UDFs) for unstructured data analysis

You can write UDFs in Java or Python that can read and process unstructured files. For example, you could write a Python UDF that uses a library like Tika to extract text from a PDF file, or a library like Pillow to get the dimensions of an image file. These UDFs can then be called in your SQL queries.

#### Stored procedure

Stored procedures can be used to orchestrate the processing of unstructured data. For example, you could have a stored procedure that lists the files in a directory table, and then loops through them, calling a UDF on each file to extract its contents and store the results in a structured table.
