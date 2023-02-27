## About this template

This is a reusable pipeline that takes in a folder name as a parameter and identifies the latest file in that folder.

Files in the folders are prefixed by the epoch timestamp. For example, `1675747156-file-name.csv`. Latest file is the file that has the greatest (or latest) epoch timestamp as a prefix. 

## Prerequisites

* [Azure blob storage with ADLS Gen2 enabled storage](https://learn.microsoft.com/en-us/azure/storage/blobs/create-data-lake-storage-account). 

## How to use this solution template

1. Use `Execute Pipeline` activity to invoke the reusable pipeline. Pass in the "Container Name" and the "Folder Path" as parameters. 
2. Use `Lookup` activity to read the file containing the latest file value. 
3. Consume the value from the file in any activity within the pipeline by setting a pipeline variable. 

## Template approach

**Input parameters for the pipeline**
* Container name of the folder
* Complete path of the folder. 

**Steps in the pipeline**
1. **Get metadata activity**: Get the filenames for all the files in the folder. 
2. **Filter formats**: Chose the file formats that will need to be evaluated. 
3. **Iterate through the files and find the latest file:** 
	1. Use a pipeline variable (`greatestEpochVal`) to hold the greatest epoch value. 
	2. Check and set the variable value with the current file's prefix. 
	3. While setting the `greatestEpochVal` , keep track of the complete filename in another pipeline variable (`latestFileName`). 

```shell
@greater(first(split(item().name, '-')),variables('greatestEpochVal')) 
```

![Condition Checks](/images/condition-checks.png) 

**Note**: At the time of authoring this sample, ADF (Azure Data Factory) pipelines don't have the ability to return a value to the parent (or the caller) pipeline. One of the workarounds to achieve this is to write the return value to a file and read it from the parent. JSON (JavaScript Object Notation) format is chosen since it is easy to be consumed by the parent pipeline through a "Lookup Activity" 

However, this is a feature that's in development and expected to be present in Q1 2023 as per the feature request. (https://feedback.azure.com/d365community/idea/85ca4a78-6c26-ec11-b6e6-000d3a4f032c)  

Once this feature is in place, we will not need the below step to persist the value in the file. 

1. **Persist the latest file info** (Workaround): The `latestFileName` variable will contain the latest file name in the folder. Write that value to a json file within the same folder path. This can be done using a `Copy Activity`. In the Copy Activity, use an empty json file as a source and use `Additional Columns` property (in "Source" tab) to add the content to the JSON file. 

![Pipeline view](/images/pipeline-view.png) 



