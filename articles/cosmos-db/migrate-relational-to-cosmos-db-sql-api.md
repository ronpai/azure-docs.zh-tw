---
title: 將一對多關聯性型資料移轉至 Azure Cosmos DB SQL API
description: 瞭解如何處理對 SQL API 進行一對多關聯性的複雜資料移轉
author: TheovanKraay
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: how-to
ms.date: 12/12/2019
ms.author: thvankra
ms.openlocfilehash: 3aec6cf2fa515d71b93d84cf8c4fc901fc847162
ms.sourcegitcommit: 3bcce2e26935f523226ea269f034e0d75aa6693a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/23/2020
ms.locfileid: "92487427"
---
# <a name="migrate-one-to-few-relational-data-into-azure-cosmos-db-sql-api-account"></a>將一對多關聯性型資料移轉至 Azure Cosmos DB SQL API 帳戶

若要從關係資料庫移轉至 Azure Cosmos DB SQL API，您可能需要變更資料模型以進行優化。

其中一個常見的轉換是在一個 JSON 檔內內嵌相關的子資料，藉此反正規化資料。 在這裡，我們將使用 Azure Data Factory 或 Azure Databricks 來查看一些選項。 如需 Cosmos DB 資料模型的一般指引，請參閱 [Azure Cosmos DB 中的資料模型](modeling-data.md)。  

## <a name="example-scenario"></a>範例案例

假設我們的 SQL database、Orders 和 OrderDetails 中有下列兩個數據表。


:::image type="content" source="./media/migrate-relational-to-cosmos-sql-api/orders.png" alt-text="顯示 SQL 資料庫中 Orders 和 OrderDetails 資料表的螢幕擷取畫面。" border="false" :::

在遷移期間，我們想要將這種一對一的關聯性合併成一個 JSON 檔。 若要這樣做，我們可以使用 "FOR JSON" 建立 T-SQL 查詢，如下所示：

```sql
SELECT
  o.OrderID,
  o.OrderDate,
  o.FirstName,
  o.LastName,
  o.Address,
  o.City,
  o.State,
  o.PostalCode,
  o.Country,
  o.Phone,
  o.Total,
  (select OrderDetailId, ProductId, UnitPrice, Quantity from OrderDetails od where od.OrderId = o.OrderId for json auto) as OrderDetails
FROM Orders o;
```

此查詢的結果如下所示： 

:::image type="content" source="./media/migrate-relational-to-cosmos-sql-api/for-json-query-result.png" alt-text="顯示 SQL 資料庫中 Orders 和 OrderDetails 資料表的螢幕擷取畫面。" lightbox="./media/migrate-relational-to-cosmos-sql-api/for-json-query-result.png":::

在理想的情況下，您會想要使用單一 Azure Data Factory (ADF) 複製活動來查詢 SQL 資料作為來源，並將輸出直接寫入 Azure Cosmos DB 接收做為適當的 JSON 物件。 目前，無法在一個複製活動中執行所需的 JSON 轉換。 如果我們嘗試將上述查詢的結果複製到 Azure Cosmos DB SQL API 容器中，我們會看到 OrderDetails 欄位是檔的字串屬性，而不是預期的 JSON 陣列。

我們可以透過下列其中一種方式解決目前的限制：

* **使用具有兩個複製活動的 Azure Data Factory**： 
  1. 從 SQL 取得 JSON 格式的資料到中繼 blob 儲存位置中的文字檔，以及 
  2. 從 JSON 文字檔將資料載入 Azure Cosmos DB 中的容器。

* **使用 Azure Databricks 從 SQL 讀取並寫入 Azure Cosmos DB** -我們會在此提供兩個選項。


讓我們更詳細地查看這些方法：

## <a name="azure-data-factory"></a>Azure Data Factory

雖然我們無法在目的地 Cosmos DB 檔中將 OrderDetails 內嵌為 JSON 陣列，但我們可以使用兩個不同的複製活動來解決此問題。

### <a name="copy-activity-1-sqljsontoblobtext"></a>複製活動 #1： SqlJsonToBlobText

針對來源資料，我們會使用 SQL 查詢將結果集取得為具有一個 JSON 物件的單一資料行， (使用 SQL Server OPENJSON 和 JSON 路徑功能來代表每個資料列的順序) ：

```sql
SELECT [value] FROM OPENJSON(
  (SELECT
    id = o.OrderID,
    o.OrderDate,
    o.FirstName,
    o.LastName,
    o.Address,
    o.City,
    o.State,
    o.PostalCode,
    o.Country,
    o.Phone,
    o.Total,
    (select OrderDetailId, ProductId, UnitPrice, Quantity from OrderDetails od where od.OrderId = o.OrderId for json auto) as OrderDetails
   FROM Orders o FOR JSON PATH)
)
```

:::image type="content" source="./media/migrate-relational-to-cosmos-sql-api/adf1.png" alt-text="顯示 SQL 資料庫中 Orders 和 OrderDetails 資料表的螢幕擷取畫面。" ) 或不會出現在資料中的另一個字元，並將「引號字元」設定為「沒有引號字元」。

:::image type="content" source="./media/migrate-relational-to-cosmos-sql-api/adf2.png" alt-text="顯示 SQL 資料庫中 Orders 和 OrderDetails 資料表的螢幕擷取畫面。":::

### <a name="copy-activity-2-blobjsontocosmos"></a>複製活動 #2： BlobJsonToCosmos

接下來，我們會藉由新增第二個複製活動，以查看第一個活動所建立之文字檔的 Azure Blob 儲存體，來修改 ADF 管線。 它會將它處理為「JSON」來源，以在文字檔中的每個 JSON 資料列插入 Cosmos DB 接收器作為一份檔。

:::image type="content" source="./media/migrate-relational-to-cosmos-sql-api/adf3.png" alt-text="顯示 SQL 資料庫中 Orders 和 OrderDetails 資料表的螢幕擷取畫面。":::

此外，我們也可以在管線中新增「刪除」活動，以便在每次執行之前，先刪除/Orders/資料夾中剩餘的所有舊檔案。 ADF 管線現在看起來像這樣：

:::image type="content" source="./media/migrate-relational-to-cosmos-sql-api/adf4.png" alt-text="顯示 SQL 資料庫中 Orders 和 OrderDetails 資料表的螢幕擷取畫面。":::

在觸發上述管線之後，我們會看到在中繼 Azure Blob 儲存體位置中建立的檔案，每個資料列包含一個 JSON 物件：

:::image type="content" source="./media/migrate-relational-to-cosmos-sql-api/adf5.png" alt-text="顯示 SQL 資料庫中 Orders 和 OrderDetails 資料表的螢幕擷取畫面。":::

我們也會看到已將內嵌 OrderDetails 插入 Cosmos DB 集合的訂單檔：

:::image type="content" source="./media/migrate-relational-to-cosmos-sql-api/adf6.png" alt-text="顯示 SQL 資料庫中 Orders 和 OrderDetails 資料表的螢幕擷取畫面。":::


## <a name="azure-databricks"></a>Azure Databricks

我們也可以在 [Azure Databricks](https://azure.microsoft.com/services/databricks/) 中使用 Spark，將資料從我們的 SQL Database 來源複製到 Azure Cosmos DB 目的地，而不需要在 Azure Blob 儲存體中建立中繼文字/JSON 檔案。 

> [!NOTE]
> 為了清楚和簡單起見，下列程式碼片段包含明確內嵌的虛擬資料庫密碼，但您應該一律使用 Azure Databricks 秘密。
>

首先，我們會建立必要的 [SQL connector](https://docs.databricks.com/data/data-sources/sql-databases-azure.html) 和 [Azure Cosmos DB 連接器](https://docs.databricks.com/data/data-sources/azure/cosmosdb-connector.html) 程式庫，並將其連結至我們的 Azure Databricks 叢集。 重新開機叢集，以確定已載入程式庫。

:::image type="content" source="./media/migrate-relational-to-cosmos-sql-api/databricks1.png" alt-text="顯示 SQL 資料庫中 Orders 和 OrderDetails 資料表的螢幕擷取畫面。":::

接下來，我們會針對 Scala 和 Python 呈現兩個範例。 

### <a name="scala"></a>Scala
在這裡，我們會取得 SQL 查詢的結果，並將 "FOR JSON" 輸出轉換成資料框架：

```scala
// Connect to Azure SQL https://docs.databricks.com/data/data-sources/sql-databases-azure.html
import com.microsoft.azure.sqldb.spark.config.Config
import com.microsoft.azure.sqldb.spark.connect._
val configSql = Config(Map(
  "url"          -> "xxxx.database.windows.net",
  "databaseName" -> "xxxx",
  "queryCustom"  -> "SELECT o.OrderID, o.OrderDate, o.FirstName, o.LastName, o.Address, o.City, o.State, o.PostalCode, o.Country, o.Phone, o.Total, (SELECT OrderDetailId, ProductId, UnitPrice, Quantity FROM OrderDetails od WHERE od.OrderId = o.OrderId FOR JSON AUTO) as OrderDetails FROM Orders o",
  "user"         -> "xxxx", 
  "password"     -> "xxxx" // NOTE: For clarity and simplicity, this example includes secrets explicitely as a string, but you should always use Databricks secrets
))
// Create DataFrame from Azure SQL query
val orders = sqlContext.read.sqlDB(configSql)
display(orders)
```

:::image type="content" source="./media/migrate-relational-to-cosmos-sql-api/databricks2.png" alt-text="顯示 SQL 資料庫中 Orders 和 OrderDetails 資料表的螢幕擷取畫面。":::

接下來，我們會連接到 Cosmos DB 資料庫和集合：

```scala
// Connect to Cosmos DB https://docs.databricks.com/data/data-sources/azure/cosmosdb-connector.html
import org.joda.time._
import org.joda.time.format._
import com.microsoft.azure.cosmosdb.spark.schema._
import com.microsoft.azure.cosmosdb.spark.CosmosDBSpark
import com.microsoft.azure.cosmosdb.spark.config.Config
import org.apache.spark.sql.functions._
import org.joda.time._
import org.joda.time.format._
import com.microsoft.azure.cosmosdb.spark.schema._
import com.microsoft.azure.cosmosdb.spark.CosmosDBSpark
import com.microsoft.azure.cosmosdb.spark.config.Config
import org.apache.spark.sql.functions._
val configMap = Map(
  "Endpoint" -> "https://xxxx.documents.azure.com:443/",
  // NOTE: For clarity and simplicity, this example includes secrets explicitely as a string, but you should always use Databricks secrets
  "Masterkey" -> "xxxx",
  "Database" -> "StoreDatabase",
  "Collection" -> "Orders")
val configCosmos = Config(configMap)
```

最後，我們會定義架構，並使用 from_json 在將資料框架儲存至 CosmosDB 集合之前套用該。

```scala
// Convert DataFrame to proper nested schema
import org.apache.spark.sql.types._
val orderDetailsSchema = ArrayType(StructType(Array(
    StructField("OrderDetailId",IntegerType,true),
    StructField("ProductId",IntegerType,true),
    StructField("UnitPrice",DoubleType,true),
    StructField("Quantity",IntegerType,true)
  )))
val ordersWithSchema = orders.select(
  col("OrderId").cast("string").as("id"),
  col("OrderDate").cast("string"),
  col("FirstName").cast("string"),
  col("LastName").cast("string"),
  col("Address").cast("string"),
  col("City").cast("string"),
  col("State").cast("string"),
  col("PostalCode").cast("string"),
  col("Country").cast("string"),
  col("Phone").cast("string"),
  col("Total").cast("double"),
  from_json(col("OrderDetails"), orderDetailsSchema).as("OrderDetails")
)
display(ordersWithSchema)
// Save nested data to Cosmos DB
CosmosDBSpark.save(ordersWithSchema, configCosmos)
```

:::image type="content" source="./media/migrate-relational-to-cosmos-sql-api/databricks3.png" alt-text="顯示 SQL 資料庫中 Orders 和 OrderDetails 資料表的螢幕擷取畫面。":::


### <a name="python"></a>Python

另一種方法是，如果源資料庫不支援 "FOR JSON" 或類似的作業) ，或您可能想要對非常大型的資料集使用平行作業，則可能需要在 Spark (中執行 JSON 轉換。 我們在此提供 PySpark 範例。 首先，在第一個資料格中設定來源和目標資料庫連接：

```python
import uuid
import pyspark.sql.functions as F
from pyspark.sql.functions import col
from pyspark.sql.types import StringType,DateType,LongType,IntegerType,TimestampType
 
#JDBC connect details for SQL Server database
jdbcHostname = "jdbcHostname"
jdbcDatabase = "OrdersDB"
jdbcUsername = "jdbcUsername"
jdbcPassword = "jdbcPassword"
jdbcPort = "1433"
 
connectionProperties = {
  "user" : jdbcUsername,
  "password" : jdbcPassword,
  "driver" : "com.microsoft.sqlserver.jdbc.SQLServerDriver"
}
jdbcUrl = "jdbc:sqlserver://{0}:{1};database={2};user={3};password={4}".format(jdbcHostname, jdbcPort, jdbcDatabase, jdbcUsername, jdbcPassword)
 
#Connect details for Target Azure Cosmos DB SQL API account
writeConfig = {
    "Endpoint": "Endpoint",
    "Masterkey": "Masterkey",
    "Database": "OrdersDB",
    "Collection": "Orders",
    "Upsert": "true"
}
```

然後，我們會在此情況下查詢源資料庫 (SQL Server) 訂單和訂單詳細資料記錄，並將結果放入 Spark 資料框架中。 我們也會建立包含所有訂單識別碼的清單，以及平行作業的執行緒集區：

```python
import json
import ast
import pyspark.sql.functions as F
import uuid
import numpy as np
import pandas as pd
from functools import reduce
from pyspark.sql import SQLContext
from pyspark.sql.types import *
from pyspark.sql import *
from pyspark.sql.functions import exp
from pyspark.sql.functions import col
from pyspark.sql.functions import lit
from pyspark.sql.functions import array
from pyspark.sql.types import *
from multiprocessing.pool import ThreadPool
 
#get all orders
orders = sqlContext.read.jdbc(url=jdbcUrl, table="orders", properties=connectionProperties)
 
#get all order details
orderdetails = sqlContext.read.jdbc(url=jdbcUrl, table="orderdetails", properties=connectionProperties)
 
#get all OrderId values to pass to map function 
orderids = orders.select('OrderId').collect()
 
#create thread pool big enough to process merge of details to orders in parallel
pool = ThreadPool(10)
```

然後，建立將訂單寫入目標 SQL API 集合的函式。 此函式會篩選指定訂單識別碼的所有訂單詳細資料，並將其轉換為 JSON 陣列，然後將陣列插入 JSON 檔，以供該順序寫入目標 SQL API 集合：

```python
def writeOrder(orderid):
  #filter the order on current value passed from map function
  order = orders.filter(orders['OrderId'] == orderid[0])
  
  #set id to be a uuid
  order = order.withColumn("id", lit(str(uuid.uuid1())))
  
  #add details field to order dataframe
  order = order.withColumn("details", lit(''))
  
  #filter order details dataframe to get details we want to merge into the order document
  orderdetailsgroup = orderdetails.filter(orderdetails['OrderId'] == orderid[0])
  
  #convert dataframe to pandas
  orderpandas = order.toPandas()
  
  #convert the order dataframe to json and remove enclosing brackets
  orderjson = orderpandas.to_json(orient='records', force_ascii=False)
  orderjson = orderjson[1:-1] 
  
  #convert orderjson to a dictionaory so we can set the details element with order details later
  orderjsondata = json.loads(orderjson)
  
  
  #convert orderdetailsgroup dataframe to json, but only if details were returned from the earlier filter
  if (orderdetailsgroup.count() !=0):
    #convert orderdetailsgroup to pandas dataframe to work better with json
    orderdetailsgroup = orderdetailsgroup.toPandas()
    
    #convert orderdetailsgroup to json string
    jsonstring = orderdetailsgroup.to_json(orient='records', force_ascii=False)
    
    #convert jsonstring to dictionary to ensure correct encoding and no corrupt records
    jsonstring = json.loads(jsonstring)
    
    #set details json element in orderjsondata to jsonstring which contains orderdetailsgroup - this merges order details into the order 
    orderjsondata['details'] = jsonstring
 
  #convert dictionary to json
  orderjsondata = json.dumps(orderjsondata)
 
  #read the json into spark dataframe
  df = spark.read.json(sc.parallelize([orderjsondata]))
  
  #write the dataframe (this will be a single order record with merged many-to-one order details) to cosmos db using spark the connector
  #https://docs.microsoft.com/azure/cosmos-db/spark-connector
  df.write.format("com.microsoft.azure.cosmosdb.spark").mode("append").options(**writeConfig).save()
```

最後，我們會使用執行緒集區上的 map 函式來呼叫上面的，以平行方式執行，並傳入我們稍早建立的訂單識別碼清單：

```python
#map order details to orders in parallel using the above function
pool.map(writeOrder, orderids)
```
在這兩種方法中，我們都應該在 Cosmos DB 的集合中，于每個訂單檔內取得正確儲存的內嵌 OrderDetails：

:::image type="content" source="./media/migrate-relational-to-cosmos-sql-api/databricks4.png" alt-text="顯示 SQL 資料庫中 Orders 和 OrderDetails 資料表的螢幕擷取畫面。":::

## <a name="next-steps"></a>後續步驟
* 瞭解 [Azure Cosmos DB 中的資料模型](./modeling-data.md)
* 瞭解 [如何在 Azure Cosmos DB 上建立資料模型和資料分割](./how-to-model-partition-example.md)