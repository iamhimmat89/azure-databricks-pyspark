# Azure Cosmos DB

-	Download azure-cosmosdb-spark library compatible to databricks cluster
	-	Go to - https://search.maven.org/
	-	Search for 'azure-cosmosdb-spark' 
	-	Click on latest version and download jar

-	Install jar file 
	-	Go to the databricks clusters
	-	Select cluster on which azure-cosmosdb-spark jar is going to install 
	-	Go to Libaries 
	-	Click on Install New and complete the installation
	
	![Cosmos DB](https://raw.githubusercontent.com/iamhimmat89/azure-databricks-pyspark/master/zimgs/cosmosdb.PNG)
	
	-	Note: There are multiple ways to install azure-cosmosdb-spark jar on cluster. you can use any of the method to install jar.
	
-	Reading from Cosmos DB 

```python
# config
config = {
	"Endpoint" : "https://<account-name>.documents.azure.com:443/", # Azure Cosmos DB account portal -> Keys -> URI
	"Masterkey" : "<master-key>",	# Azure Cosmos DB account portal -> Keys -> PRIMARY KEY
	"Database" : "<database-name>", # Azure Cosmos DB account portal -> Data Explorer -> under SQL API : parent/root name 
	"preferredRegions" : "Central US;East US2",
	"Collection" : "<collection>", # Azure Cosmos DB account portal -> Data Explorer -> under SQL API : under database : select required collection 
	"SamplingRatio" : "1.0",
	"schema_samplesize" : "1000",
	"query_pagesize" : "2147483647",
	"query_custom" : "SELECT c.col1, c.col2 FROM c"
}

df = spark.read.format("com.microsoft.azure.cosmosdb.spark").options(**config).load()
df.count()
```

-	Writing to Cosmos DB

```python
df = df = spark.read.csv("<file-path>", inferSchema = True, header = True)

# Config
config = {
	"Endpoint" : "https://<account-name>.documents.azure.com:443/", # Azure Cosmos DB account portal -> Keys -> URI
	"Masterkey" : "<master-key>",	# Azure Cosmos DB account portal -> Keys -> PRIMARY KEY
	"Database" : "<database-name>", # Azure Cosmos DB account portal -> Data Explorer -> under SQL API : parent/root name 
	"Collection" : "<collection>", # Azure Cosmos DB account portal -> Data Explorer -> under SQL API : under database : select required collection 
	"Upsert" : "true"
}

df.write.format("com.microsoft.azure.cosmosdb.spark").mode("append").options(config).save()
```

-	Common Errors

	-	Error: java.lang.ClassNotFoundException: Failed to find data source: com.microsoft.azure.cosmosdb.spark. Please find package at http://spark.apache.org/third-party-projects.html
	```python
	Solution: 
		This error is due to azure-cosmosdb-spark package is not installed. Please check first 2 steps for the same.
	```
	
	-	Error: java.lang.UnsupportedOperationException: Writing in a non-empty collection.
	```python
	Solution: 
		# This is due to sepcified collection is non-empty. Either use another collection or add mode("append")
		df.write.format("com.microsoft.azure.cosmosdb.spark").mode("append").options(config).save()
	```
	
	