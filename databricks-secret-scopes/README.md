# Databricks Secret Scopes

-	There are two types of secrets scope available with azure databricks:
	-	Azure key vault backed
	-	Databricks backed

## **1.	Azure key vault backed**
-	Add key-values into key vault using azure portal
	-	To know more about how to add key vault, please refer this link - https://github.com/iamhimmat89/azure-databricks-pyspark/blob/master/azure-key-vault/README.md

-	Create secret scope in azure databricks
	-	use <databricks-url>#secrets/createScope
	-	get DNS Name and Resource ID from azure key vault properties
	
![Output Files](https://raw.githubusercontent.com/iamhimmat89/azure-databricks-pyspark/master/zimgs/secret-scope.PNG)

![Output Files](https://raw.githubusercontent.com/iamhimmat89/azure-databricks-pyspark/master/zimgs/key-vault-2.PNG)

-	Access secret values

```python
dbutils.secrets.get(scope = "<scope-name>", key = "<key-name>")
```
	
## **2.	Databricks backed**

- 	This approach is stored in encrypted database managed by azure databricks.
-	There are 2 ways to implement this
	-	Databricks CLI - https://docs.microsoft.com/en-us/azure/databricks/dev-tools/databricks-cli#databricks-cli
	-	Secrets API - https://docs.microsoft.com/en-us/azure/databricks/dev-tools/api/latest/secrets#secrets-api
-	Secrets permissions are managed by secret ACL's
