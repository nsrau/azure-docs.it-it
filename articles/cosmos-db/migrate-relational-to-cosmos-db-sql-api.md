---
title: Eseguire la migrazione di dati relazionali uno-a-pochi in Azure Cosmos DB API SQL
description: Informazioni su come gestire la migrazione dei dati complessi per le relazioni uno-a-pochi nell'API SQL
author: TheovanKraay
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 12/12/2019
ms.author: thvankra
ms.openlocfilehash: 467e9627a2623779bd808ca5aebdf76d8a5eda42
ms.sourcegitcommit: 8e9a6972196c5a752e9a0d021b715ca3b20a928f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/11/2020
ms.locfileid: "75896636"
---
# <a name="migrate-one-to-few-relational-data-into-azure-cosmos-db-sql-api-account"></a>Eseguire la migrazione di dati relazionali uno-a-pochi in Azure Cosmos DB account API SQL

Per eseguire la migrazione da un database relazionale a Azure Cosmos DB API SQL, può essere necessario apportare modifiche al modello di dati per l'ottimizzazione.

Una trasformazione comune è la denormalizzazione dei dati incorporando gli elementi secondari correlati all'interno di un documento JSON. Di seguito sono riportate alcune opzioni per l'utilizzo di Azure Data Factory o Azure Databricks. Per indicazioni generali sulla modellazione dei dati per Cosmos DB, vedere [modellazione dei dati in Azure Cosmos DB](modeling-data.md).  

## <a name="example-scenario"></a>Scenario di esempio

Si supponga di disporre delle due tabelle seguenti nel database SQL, Orders e OrderDetails.


![Order Details](./media/migrate-relational-to-cosmos-sql-api/orders.png)

Si vuole combinare questa relazione uno-a-pochi in un documento JSON durante la migrazione. A tale scopo, è possibile creare una query T-SQL usando "FOR JSON" come indicato di seguito:

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

I risultati di questa query avranno un aspetto simile al seguente: 

![Order Details](./media/migrate-relational-to-cosmos-sql-api/for-json-query-result.png#lightbox)


Idealmente, è consigliabile usare una singola attività di copia Azure Data Factory (ADF) per eseguire query sui dati SQL come origine e scrivere l'output direttamente in Azure Cosmos DB sink come oggetti JSON appropriati. Attualmente, non è possibile eseguire la trasformazione JSON necessaria in un'attività di copia. Se si tenta di copiare i risultati della query precedente in un contenitore dell'API SQL di Azure Cosmos DB, il campo OrderDetails viene visualizzato come una proprietà di stringa del documento, invece della matrice JSON prevista.

È possibile aggirare questa limitazione corrente in uno dei modi seguenti:

* **Usare Azure Data Factory con due attività di copia**: 
  1. Ottenere i dati in formato JSON da SQL in un file di testo in una posizione di archiviazione BLOB intermedia e 
  2. Caricare i dati dal file di testo JSON in un contenitore in Azure Cosmos DB.

* **Usare Azure Databricks per leggere da SQL e scrivere nel Azure Cosmos DB** . in questo caso, vengono presentate due opzioni.


Esaminiamo questi approcci in modo più dettagliato:

## <a name="azure-data-factory"></a>Data factory di Azure

Sebbene non sia possibile incorporare OrderDetails come matrice JSON nel documento di Cosmos DB di destinazione, è possibile aggirare il problema usando due attività di copia separate.

### <a name="copy-activity-1-sqljsontoblobtext"></a>#1 attività di copia: SqlJsonToBlobText

Per i dati di origine, si usa una query SQL per ottenere il set di risultati come una singola colonna con un oggetto JSON (che rappresenta l'ordine) per riga usando le funzionalità SQL Server OPENJSON e FOR JSON PATH:

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

![Copia ADF](./media/migrate-relational-to-cosmos-sql-api/adf1.png)


Per il sink dell'attività di copia SqlJsonToBlobText, è necessario scegliere "testo delimitato" e puntare a una cartella specifica nell'archivio BLOB di Azure con un nome file univoco generato in modo dinamico, ad esempio '@concat(pipeline (). RunId,'. JSON ').
Poiché il file di testo non è effettivamente "delimitato" e non si vuole che venga analizzato in colonne separate usando le virgole e si desidera mantenere le virgolette doppie ("), si imposta" delimitatore di colonna "su una scheda (" \t ") o un altro carattere che non si verifica nei dati e" virgolette "  su "nessun carattere virgolette".

![Copia ADF](./media/migrate-relational-to-cosmos-sql-api/adf2.png)

### <a name="copy-activity-2-blobjsontocosmos"></a>#2 attività di copia: BlobJsonToCosmos

Modificare quindi la pipeline di ADF aggiungendo la seconda attività di copia che cerca nell'archivio BLOB di Azure il file di testo creato dalla prima attività. Lo elabora come origine "JSON" da inserire in Cosmos DB sink come un documento per ogni riga JSON trovata nel file di testo.

![Copia ADF](./media/migrate-relational-to-cosmos-sql-api/adf3.png)

Facoltativamente, viene aggiunta anche un'attività "Delete" alla pipeline in modo che elimini tutti i file precedenti rimanenti nella cartella/Orders/prima di ogni esecuzione. La pipeline di ADF è ora simile alla seguente:

![Copia ADF](./media/migrate-relational-to-cosmos-sql-api/adf4.png)

Dopo aver attivato la pipeline precedente, viene visualizzato un file creato nel percorso di archiviazione BLOB di Azure intermedio contenente un oggetto JSON per riga:

![Copia ADF](./media/migrate-relational-to-cosmos-sql-api/adf5.png)

Vengono inoltre visualizzati i documenti Orders con OrderDetails incorporati correttamente inseriti nella raccolta Cosmos DB:

![Copia ADF](./media/migrate-relational-to-cosmos-sql-api/adf6.png)


## <a name="azure-databricks"></a>Azure Databricks

È anche possibile usare Spark in [Azure Databricks](https://azure.microsoft.com/services/databricks/) per copiare i dati dall'origine del database SQL alla destinazione Azure Cosmos DB senza creare i file di testo/JSON intermedi nell'archivio BLOB di Azure. 

> [!NOTE]
> Per maggiore chiarezza e semplicità, i frammenti di codice riportati di seguito includono password di database fittizie in modo esplicito, ma è consigliabile usare sempre Azure Databricks segreti.
>

Prima di tutto, creare e alleghiamo le librerie [SQL Connector](https://docs.databricks.com/data/data-sources/sql-databases-azure.html) e [Azure Cosmos DB Connector](https://docs.databricks.com/data/data-sources/azure/cosmosdb-connector.html) necessarie al cluster Azure Databricks. Riavviare il cluster per assicurarsi che le librerie vengano caricate.

![Databricks](./media/migrate-relational-to-cosmos-sql-api/databricks1.png)

Si presentano quindi due esempi per scala e Python. 

### <a name="scala"></a>Scala
Qui si ottengono i risultati della query SQL con l'output "FOR JSON" in un frame di dati:

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

![Databricks](./media/migrate-relational-to-cosmos-sql-api/databricks2.png)

A questo punto, ci si connette al database di Cosmos DB e alla raccolta:

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

Infine, si definisce lo schema e si usa from_json per applicare il frame di frame prima di salvarlo nella raccolta CosmosDB.

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

![Databricks](./media/migrate-relational-to-cosmos-sql-api/databricks3.png)


### <a name="python"></a>Python

Come approccio alternativo, potrebbe essere necessario eseguire trasformazioni JSON in Spark (se il database di origine non supporta "FOR JSON" o un'operazione simile) oppure si desidera utilizzare operazioni parallele per un set di dati di grandi dimensioni. Qui viene presentato un esempio PySpark. Per iniziare, configurare le connessioni al database di origine e di destinazione nella prima cella:

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

Viene quindi eseguita una query sul database di origine (in questo caso SQL Server) per i record Order e Order Detail, inserendo i risultati in frame di dati Spark. Si creerà anche un elenco contenente tutti gli ID degli ordini e un pool di thread per le operazioni parallele:

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

Quindi, creare una funzione per la scrittura degli ordini nella raccolta di API SQL di destinazione. Questa funzione filtra tutti i dettagli dell'ordine per l'ID dell'ordine specificato, li converte in una matrice JSON e inserisce la matrice in un documento JSON che verrà scritto nella raccolta di API SQL di destinazione per l'ordine seguente:

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

Infine, si chiamerà quanto sopra riportato utilizzando una funzione map sul pool di thread, per l'esecuzione in parallelo, passando l'elenco degli ID ordine creati in precedenza:

```python
#map order details to orders in parallel using the above function
pool.map(writeOrder, orderids)
```
In entrambi gli approcci, alla fine, si dovrebbe salvare correttamente OrderDetails incorporati all'interno di ogni documento di ordine nella raccolta Cosmos DB:

![Databricks](./media/migrate-relational-to-cosmos-sql-api/databricks4.png)

## <a name="next-steps"></a>Passaggi successivi
* Informazioni sulla [modellazione dei dati in Azure Cosmos DB](https://docs.microsoft.com/azure/cosmos-db/modeling-data)
* Informazioni [su come modellare e partizionare i dati in Azure Cosmos DB](https://docs.microsoft.com/azure/cosmos-db/how-to-model-partition-example)
