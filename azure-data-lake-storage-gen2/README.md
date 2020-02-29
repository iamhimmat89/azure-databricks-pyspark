# Azure Data Lake Storage - Gen2

Azure data lake storage (ADLS) Gen2 is design mainly for big data anlytics which also includes features of existing storage account (azure blob storage and ADLS Gen1).

-	Key Features
	-	Performance (Query, Data Load) 
	-	Granular storage (at directory and file level)
	-	Hadoop compatible access
	-	Data consistency

There are basically two ways to access ADLS Gen2 in Databricks

## **1. Mount Azure Data Lake Storage Gen2 to Databricks File System (DBFS)**

-	Please refer below link for How to mount path in databricks and access required data.
	https://github.com/iamhimmat89/azure-databricks-pyspark/blob/master/databricks-mount-path/README.md
	

## **2. Access Directly**

1.	**Using DataFrame API**

```python
spark.conf.set("fs.azure.account.key.<storage-account-name>.dfs.core.windows.net", "<storage-account-access-key>")
# Recommanded to use azure key vault as below. For more details please check this link -  https://github.com/iamhimmat89/azure-databricks-pyspark/blob/master/azure-key-vault/README.md
# spark.conf.set("fs.azure.account.key.<storage-account-name>.dfs.core.windows.net", dbutils.secrets.get(scope = "<scope-name>", key = "<key-name>"))

storage_url = "abfss://<file-system>@<storage-account-name>.dfs.core.windows.net"

```

2.	**Using RDD API**

```python

spark.hadoop.fs.azure.account.key.<storage-account-name>.dfs.core.windows.net <storage-account-access-key>
# As mention above use key vault for storing keys 
```
