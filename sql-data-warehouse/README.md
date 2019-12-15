# Azure SQL Data Warehouse

-	We can access SQL Data Warehouse (SQL DW) from databricks using a JDBC connector.
-	The databricks cluster and SQL DW instance access common storage container (blob) to transfer data between two system.
-	Please refer below link to set up and access blob storage data:  
	https://github.com/iamhimmat89/azure-databricks-pyspark/tree/master/blob-storage

![SQL Config](https://raw.githubusercontent.com/iamhimmat89/azure-databricks-pyspark/master/zimgs/sql-conf.PNG)

## **1.	Batch**
	
```python
# Blob storage account set up
spark.conf.set("fs.azure.account.key.<storage-account-name>.blob.core.windows.net", "<storage-account-access-key>")

# Read data from a SQL DW table.
df = spark.read \
  .format("com.databricks.spark.sqldw") \
  .option("url", "jdbc:sqlserver://<connection-string>") \
  .option("tempDir", "wasbs://<container-name>@<storage-account-name>.blob.core.windows.net/<dir>") \
  .option("forwardSparkAzureStorageCredentials", "true") \
  .option("dbTable", "<table-name>") \
  .load()
  
# Read data using a SQL DW query.
df = spark.read \
  .format("com.databricks.spark.sqldw") \
  .option("url", "jdbc:sqlserver://<connection-string>") \
  .option("tempDir", "wasbs://<container-name>@<storage-account-name>.blob.core.windows.net/<dir>") \
  .option("forwardSparkAzureStorageCredentials", "true") \
  .option("query", "select x, y from <table-name>") \
  .load()

# code <data transformations>
  
# Write data to SQL DW table.
df.write \
  .format("com.databricks.spark.sqldw") \
  .option("url", "jdbc:sqlserver://<connection-string>") \
  .option("forwardSparkAzureStorageCredentials", "true") \
  .option("dbTable", "my_table_in_dw_copy") \
  .option("tempDir", "wasbs://<container-name>@<storage-account-name>.blob.core.windows.net/<dir>") \
  .save()
```

## **2.	Streaming**

```python
# Blob storage account set up
spark.conf.set(
  "fs.azure.account.key.<storage-account-name>.blob.core.windows.net",
  "<your-storage-account-access-key>")

# streaming source
df = spark.readStream \
  .format("rate") \
  .option("rowsPerSecond", "10000") \
  .option("numPartitions", "8") \
  .load()

# code <data transformations>
  
# Write data to SQL DW table.
df.writeStream \
  .format("com.databricks.spark.sqldw") \
  .option("url", "jdbc:sqlserver://<connection-string>") \
  .option("tempDir", "wasbs://<container-name>@<storage-account-name>.blob.core.windows.net/<dir>") \
  .option("forwardSparkAzureStorageCredentials", "true") \
  .option("dbTable", <table-name>) \
  .option("checkpointLocation", "/<checkpoint_loc>") \
  .start()
```

