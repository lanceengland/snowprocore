# Data Loading and Unloading

## Define concepts and best practices that should be considered when loading data

### Stages and stage types

As covered previously, stages are essential for loading data into Snowflake. They act as an intermediary storage area for your data files. Best practices include:

- Using an **external stage** pointing to your own cloud storage (S3, Azure Blob, GCS) for most loading scenarios. This gives you more control and allows you to use your existing data lake infrastructure.
- Using an **internal stage** for temporary, ad-hoc loading, or when you don't have an external cloud storage account.
- Organizing your stages logically to reflect your data pipelines and sources.

### File size and formats

The size and format of your data files can have a significant impact on loading performance.

- **File Size:** The optimal file size for loading is between 100MB and 250MB compressed. Snowflake is a massively parallel processing (MPP) system, and by splitting your data into multiple files of this size, you allow Snowflake to distribute the loading work across multiple nodes in your virtual warehouse, resulting in much faster loading times.
- **File Formats:** Snowflake supports a variety of file formats, including CSV, JSON, Avro, ORC, Parquet, and XML. For semi-structured data, Parquet, Avro, and ORC are generally recommended as they are compressed, binary formats that are often faster to load than text-based formats like CSV and JSON.

### Folder structures

When your data is stored in an external stage, the way you organize your files into folders can be very important. A common best practice is to partition your data into logical folder paths. For example, you might organize your data by date: `my-bucket/my-app/YYYY/MM/DD/`. This structure allows you to easily load data for a specific time period by simply pointing your `COPY` command to the relevant folder.

### Ad hoc/bulk loading

There are two primary modes of data loading in Snowflake:

- **Bulk Loading:** This is for loading large datasets and is best accomplished using the `COPY INTO` command. This command is highly optimized for loading large volumes of data from staged files.
- **Ad Hoc Loading:** For smaller, infrequent data loads, you can use the Web UI to load data directly from your local machine. For small numbers of rows, you can also use `INSERT` statements, but this is not recommended for large amounts of data.

### Snowpipe

Snowpipe is Snowflake's continuous data ingestion service. You should use Snowpipe when you have a continuous stream of new data arriving in your external stage and you want that data to be loaded into Snowflake automatically and efficiently. Snowpipe uses a serverless compute model, so you don't need to manage a virtual warehouse for loading. It's a cost-effective and scalable solution for near-real-time data ingestion.

## Outline different commands used to load data and when they should be used

### CREATE STAGE

This command is used to create a stage, which is a prerequisite for loading data. You use it to define the location and credentials for either an internal or external stage.

### CREATE FILE FORMAT

This command creates a named file format object that specifies the format of your data files. This is where you define things like the file type (CSV, JSON, etc.), compression method, field delimiter, and how to handle null values. Creating a named file format is a best practice as it allows you to reuse the same format definition across multiple `COPY` commands.

### CREATE PIPE

This command is used to create a pipe for Snowpipe. The pipe definition includes the `COPY INTO` statement that will be executed to load the data, as well as the stage where the new data files will be arriving.

### CREATE EXTERNAL TABLE

This command is used when you want to query data in an external stage without actually loading it into Snowflake. An external table reads the data directly from the files in your data lake. This is useful when you want to query data that is not yet in Snowflake, or when you want to join data in Snowflake with data in your data lake.

### COPY INTO

This is the primary command for bulk data loading. You use `COPY INTO <table> FROM @<stage>` to load data from files in a stage into a target table. It has many options for things like error handling (`ON_ERROR`), column mapping (`MATCH_BY_COLUMN_NAME`), and even performing transformations on the data as it's being loaded.

### INSERT/INSERT OVERWRITE

The `INSERT` command can be used to load small amounts of data, typically a few rows at a time. It's not efficient for large-scale loading. `INSERT OVERWRITE` is a useful command for completely replacing the contents of a table with new data.

### PUT

The `PUT` command is used to upload data files from your local machine to a Snowflake internal stage. This is a necessary step if you are using an internal stage and your data files are on your local filesystem.

### VALIDATE

The `VALIDATE` function is a very useful tool for testing your `COPY INTO` statement before you actually run it. It will check the files in your stage and report any errors that it finds, such as data type mismatches or incorrect formatting. This can save you a lot of time and effort by allowing you to identify and fix problems before you attempt to load your data.

## Define concepts and best practices that should be considered when unloading data

### File size and formats

Just as with loading, you can control the size and format of the files that Snowflake creates when you unload data.

- **File Size:** You can use the `MAX_FILE_SIZE` option in the `COPY INTO @stage` command to specify the maximum size of each output file.
- **File Formats:** You can unload data in the same formats that are supported for loading (CSV, JSON, Parquet, etc.).

#### Overview of compression methods

When unloading data, you can specify the compression method you want to use. Supported compression methods include `GZIP`, `BZ2`, `BROTLI`, and `ZSTD`. Using compression can significantly reduce the size of your unloaded files, which can save on storage costs and network transfer time.

### Empty strings and NULL values

When unloading data, you may need to control how `NULL` values and empty strings are represented in the output files. You can use options like `EMPTY_FIELD_AS_NULL` and `NULL_IF` in your file format or `COPY` command to specify this behavior.

### Unloading to a single file

By default, Snowflake will unload data in parallel into multiple files to maximize performance. However, there may be times when you need to unload all the data into a single file. You can do this by using the `SINGLE = TRUE` option in your `COPY INTO @stage` command. Be aware that this will be slower than unloading into multiple files.

### Unloading relational tables

When you need to unload data from multiple related tables, you will typically need to execute a separate `COPY INTO @stage` command for each table. There is no built-in feature to automatically unload a set of related tables while maintaining their relationships.

## Outline the different commands used to unload data and when they should be used

### GET

The `GET` command is the counterpart to the `PUT` command. It's used to download files from a Snowflake internal stage to your local machine.

### LIST

The `LIST` command (aliased as `LS`) is used to view the list of files in a stage. This is useful for verifying that your unload operation was successful and for seeing the names of the files that were created.

### COPY INTO

This is the primary command for unloading data. The syntax is `COPY INTO @<stage> FROM <table>`. You can use the same file format and copy options that you use for loading to control the format and content of the unloaded files.

### CREATE STAGE

Just as with loading, you need a stage to unload data to. You'll need to create a stage if you don't already have one where you want to store your unloaded files.

### CREATE FILE FORMAT

You'll also need a file format to specify the format of your unloaded files. You can create a new file format or use an existing one.
