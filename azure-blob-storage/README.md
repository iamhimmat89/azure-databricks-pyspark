# Azure Blob Storage

There are two ways to access azure blob storage data in databricks.
Before directly jumping to the solution let us check which all details are required and from where we can get those.
-	Container Name <container-name>
-	Storage Account Name <storage-account-name>
-	Storage Account Access Key <storage-account-access-key>
	-	There would be 2 access keys, we can use either one of them.

![Storage Account - Access Key Page](https://raw.githubusercontent.com/iamhimmat89/azure-databricks-pyspark/master/zimgs/blob-access-key.PNG)

![Storage Account - Storage Explorer Page](https://raw.githubusercontent.com/iamhimmat89/azure-databricks-pyspark/master/zimgs/blob-container-name.PNG)

## **1. Mount Blob Storage to Databricks File System (DBFS)**

-	Please refer below link for How to mount path in databricks and access required data.
	https://github.com/iamhimmat89/azure-databricks-pyspark/blob/master/databricks-mount-path/README.md
	
	
## **2.	Access Directly**

1.	**Using DataFrame API**

```python
spark.conf.set("fs.azure.account.key.<storage-account-name>.blob.core.windows.net", "<storage-account-access-key>")
# Recommanded to use azure key vault as below. For more details please check this link -  https://github.com/iamhimmat89/azure-databricks-pyspark/blob/master/azure-key-vault/README.md
# spark.conf.set("fs.azure.account.key.<storage-account-name>.blob.core.windows.net", dbutils.secrets.get(scope = "<scope-name>", key = "<key-name>"))

storage_url = "wasbs://<container-name>@<storage-account-name>.blob.core.windows.net"

# Read File
df = spark.read.csv(storage_url+"/<file-path>", inferSchema = True, header = True)

# Write File
# This will write file as part files similar to map-reduce job. Number of files depends on volumn/size of data
df.write.mode("overwrite").format("parquet").option("header", "true").save(storage_url+"/<target-path>")

```	

Output files are look like below:
![Output Files](https://raw.githubusercontent.com/iamhimmat89/azure-databricks-pyspark/master/zimgs/part-file.PNG)

```python	
# Use below code to write file with specific file name 
# coalesce(1) - Will make sure there should be only one output file as part-00000
df.coalesce(1).write.mode("overwrite").format("parquet").option("header", "true").save(storage_url+"/<tmp-path>")

# Move part file to target directory with specified file name and remove files from tmp directory
fileName = "output.parquet"
tmpReadPath = storage_url+"/<tmp-path>"
tgtWritePath = storage_url+"/<target-path>"

fileList = dbutils.fs.ls(tmpReadPath)

for file in fileList:
	if file[1].startswith("part-00000"):
		dbutils.fs.mv(tmpReadPath+"/"+file[1], tgtWritePath+"/"+fileName)
		
dbutils.fs.rm(tmpReadPath, recurse = True)
```	
		
2.	**Using RDD API**

- 	Setting configuration with spark.conf.set() is only visible to DataFrame and DataSet API. 
- 	RDD API has different syntax to set confiuration as below:

```	python
spark.hadoop.fs.azure.account.key.<storage-account-name>.blob.core.windows.net <storage-account-access-key>
# As mention above use key vault for storing keys 
```	

- 	For read and write blob file you can refer above section(Using DataFrame API).
