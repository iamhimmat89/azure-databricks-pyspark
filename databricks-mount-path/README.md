# Databricks Mount Path


## **1. Mount Azure Blob Storage to Databricks File System (DBFS)**

- 	We can mount a folder inside container or entire container. In this approach data is never copied/synched locally, it is just a pointer to blob storage container.
- 	Note - There are three types of blob storage supports by azure; block, append, and page. we can only mount block blobs to DBFS.

```python
# Mount
dbutils.fs.mount(
	source = "wasbs://<container-name>@<storage-account-name>.blob.core.windows.net",
	mount_point = "/mnt/<dbfs-path>", # dbfs-path where blob will be mounted in DBFS 
	extra_configs = {fs.azure.account.key.<storage-account-name>.blob.core.windows.net:<storage-account-access-key>}
)
# Here instead of using access key directly, we should always use azure key vault. 
# Below is the syntax for the same 
# extra_configs = {fs.azure.account.key.<storage-account-name>.blob.core.windows.net:dbutils.secrets.get(scope = "<scope-name>", key = "<key-name>")}
# For more details, how to set-up key vault and all. Please check this link - https://github.com/iamhimmat89/azure-databricks-pyspark/blob/master/azure-key-vault/README.md

# Access Files
df = spark.read.csv("/mnt/<dbfs-path>/...", inferSchema = True, header = True) # OR
df = spark.read.csv("dbfs:/<dbfs-path>/...", inferSchema = True, header = True)

# Unmount 
dbutils.fs.unmount("/mnt/<dbfs-path>")
```


## **2. Mount Azure Data Lake Storage Gen2 to Databricks File System (DBFS)**

-	All users in the workspace have access to mounted ADLS Gen2 account.

```python
# Mount
configs = {"fs.azure.account.auth.type": "OAuth",
           "fs.azure.account.oauth.provider.type": "org.apache.hadoop.fs.azurebfs.oauth2.ClientCredsTokenProvider",
           "fs.azure.account.oauth2.client.id": "<application-id>",
           "fs.azure.account.oauth2.client.secret": dbutils.secrets.get(scope = "<scope-name>", key = "<key-name-for-service-credential>"),
           "fs.azure.account.oauth2.client.endpoint": "https://login.microsoftonline.com/<directory-id>/oauth2/token"}

dbutils.fs.mount(
  source = "abfss://<file-system>@<storage-account-name>.dfs.core.windows.net/",
  mount_point = "/mnt/<dbfs-path>",
  extra_configs = configs)

# Access Files
df = spark.read.csv("/mnt/<dbfs-path>/...", inferSchema = True, header = True) # OR
df = spark.read.csv("dbfs:/<dbfs-path>/...", inferSchema = True, header = True)

# Unmount 
dbutils.fs.unmount("/mnt/<dbfs-path>")
```


