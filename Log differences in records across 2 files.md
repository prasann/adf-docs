## About this template

This is a reusable pipeline to integrate with all the data pipeline to log all the unprocessed records, at the end of the pipeline run.

Data pipelines uses source (`.csv`) files from Blob storage, will perform transformations (joins, aggregates etc.) and will write a processed file (`.csv`) back to the Blob storage in the staging area. This reusable pipeline, is useful to monitor the missed out records as part of transformation.

## Prerequisites

* [Azure blob storage with ADLS Gen2 enabled storage](https://learn.microsoft.com/en-us/azure/storage/blobs/create-data-lake-storage-account). 

## How to use this solution template

This pipeline has to be integrated at the end of the data processing pipeline post the data transformation operations.

Use `Execute Pipeline` activity to invoke the reusable pipeline. Pass in the required parameter.

## Template approach

**Input parameters**
* Complete path of the sourceFile 
* Complete path of the stagingFile
* Column in the sourceFile to check upon
* Corresponding column in the stagingFile to compare with

**Steps:**

![Data flow for unprocessed records](/images/log-unprocessed-df.png) 

This pipeline has a single `Dataflow activity` in it, which does the job of comparing the two files and write the differences to a location.

1. Select the source column (mentioned in the parameter) from the source file.
2. Select the staging column (mentioned in the parameter) from the staging files.
3. Perform `exists` check between the two datasets.
4. Write the differences to a file in the defined location.

Note: While performing `exists` check against columns from different table, there was a challenge when the name of both the column where same. Hence the select operation, renames the column from source and staging to a static value.
