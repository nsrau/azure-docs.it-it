---
title: Eseguire la migrazione di dati relazionali uno-a-pochi in Azure Cosmos DB SQL API
description: Informazioni su come gestire la migrazione dei dati complessi per relazioni uno-a-pochi nell'API SQL
author: TheovanKraay
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 12/12/2019
ms.author: thvankra
ms.openlocfilehash: 467e9627a2623779bd808ca5aebdf76d8a5eda42
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "75896636"
---
# <a name="migrate-one-to-few-relational-data-into-azure-cosmos-db-sql-api-account"></a>Eseguire la migrazione di dati relazionali uno-a-pochi nell'account API SQL del database di Azure CosmosMigrate one-to-few relational data into Azure Cosmos DB SQL API account

Per eseguire la migrazione da un database relazionale all'API SQL del database Cosmos di Azure, può essere necessario apportare modifiche al modello di dati per l'ottimizzazione.

Una trasformazione comune è la denormalizzazione dei dati incorporando elementi secondari correlati all'interno di un documento JSON. Ecco alcune opzioni per questo usando Azure Data Factory o Azure Databricks.Here we look at a few options for this using Azure Data Factory or Azure Databricks. Per indicazioni generali sulla modellazione dei dati per Cosmos DB, vedere [Modellazione dei dati in Azure Cosmos DB](modeling-data.md).  

## <a name="example-scenario"></a>Scenario di esempio

Si supponga di disporre delle due tabelle seguenti nel database SQL, Orders e OrderDetails.


![Order Details](./media/migrate-relational-to-cosmos-sql-api/orders.png)

Vogliamo combinare questa relazione uno-a-pochi in un unico documento JSON durante la migrazione. A tale scopo, è possibile creare una query T-SQL utilizzando "FOR JSON" come di seguito:To do this, we can create a T-SQL query using "FOR JSON" as below:

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

I risultati di questa query apparirebbero come di seguito: 

![Order Details](./media/migrate-relational-to-cosmos-sql-api/for-json-query-result.png#lightbox)


In teoria, si vuole usare una singola attività di copia di Azure Data Factory (ADF) per eseguire query sui dati SQL come origine e scrivere l'output direttamente nel sink del database di Azure Cosmos come oggetti JSON appropriati. Attualmente, non è possibile eseguire la trasformazione JSON necessaria in un'attività di copia. Se si tenta di copiare i risultati della query precedente in un contenitore API SQL del database Cosmos di Azure, verrà visualizzato il campo OrderDetails come proprietà stringa del documento, anziché la matrice JSON prevista.

È possibile aggirare questa limitazione corrente in uno dei seguenti modi:

* **Usare Azure Data Factory con due attività di copia:Use Azure Data Factory with two copy activities:** 
  1. Ottenere dati in formato JSON da SQL a un file di testo in un percorso di archiviazione BLOB intermedio e 
  2. Caricare i dati dal file di testo JSON in un contenitore in Azure Cosmos DB.

* **Usare Azure Databricks per leggere da SQL e scrivere in Azure Cosmos DB:** verranno presentate due opzioni.


Esaminiamo questi approcci in modo più dettagliato:Let's look at these approaches in more detail:

## <a name="azure-data-factory"></a>Data factory di Azure

Anche se non è possibile incorporare OrderDetails come matrice JSON nel documento Cosmos DB di destinazione, è possibile aggirare il problema utilizzando due attività di copia separate.

### <a name="copy-activity-1-sqljsontoblobtext"></a>#1 attività di copia: SqlJsonToBlobTextCopy Activity #1: SqlJsonToBlobText

Per i dati di origine, utilizziamo una query SQL per ottenere il set di risultati come una singola colonna con un oggetto JSON (che rappresenta l'ordine) per riga usando le funzionalità OPENJSON di SQL Server e FOR JSON PATH:

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


Per il sink dell'attività di copia SqlJsonToBlobText, si sceglie "Testo delimitato" e lo si punta a una@concatcartella specifica in Archiviazione BLOB di Azure con un nome di file univoco generato dinamicamente (ad esempio, ' (pipeline(). RunId,'.json').
Dal momento che il nostro file di testo non è realmente "delimitato" e non vogliamo che venga analizzato in colonne separate con virgole e si desidera mantenere le virgolette doppie ("), abbiamo impostato "Delimitatore di colonna" su una tabulazione ("'t") - o un altro carattere che non si verifica nei dati - e "Carattere virgolette" a "Nessun carattere di virgolette".

![Copia ADF](./media/migrate-relational-to-cosmos-sql-api/adf2.png)

### <a name="copy-activity-2-blobjsontocosmos"></a>Attività di copia: BlobJsonToCosmosCopy Activity #2: BlobJsonToCosmos

Successivamente, si modifica la pipeline ADF aggiungendo la seconda attività di copia che cerca in Archiviazione BLOB di Azure per il file di testo creato dalla prima attività. Elabora come origine "JSON" da inserire nel sink Cosmos DB come un documento per ogni riga JSON trovato nel file di testo.

![Copia ADF](./media/migrate-relational-to-cosmos-sql-api/adf3.png)

Facoltativamente, viene aggiunta anche un'attività "Elimina" alla pipeline in modo da eliminare tutti i file precedenti rimanenti nella cartella /Orders/ prima di ogni esecuzione. La nostra pipeline ADF ora ha un aspetto simile al seguente:Our ADF pipeline now looks something like this:

![Copia ADF](./media/migrate-relational-to-cosmos-sql-api/adf4.png)

Dopo aver attivato la pipeline precedente, viene visualizzato un file creato nel percorso intermedio di Archiviazione BLOB di Azure contenente un oggetto JSON per riga:After we trigger the pipeline above, we see a file created in our intermediary Azure Blob Storage location containing one JSON-object per row:

![Copia ADF](./media/migrate-relational-to-cosmos-sql-api/adf5.png)

Vediamo anche Ordini documenti con correttamente incorporato OrderDetails inserito nella nostra collezione Cosmos DB:

![Copia ADF](./media/migrate-relational-to-cosmos-sql-api/adf6.png)


## <a name="azure-databricks"></a>Azure Databricks

È anche possibile usare Spark in Azure Databricks per copiare i dati dall'origine del database SQL alla destinazione Azure Cosmos DB senza creare i file di testo/JSON intermedi nell'archiviazione BLOB di Azure.We can also use Spark in [Azure Databricks](https://azure.microsoft.com/services/databricks/) to copy the data from our SQL Database source to the Azure Cosmos DB destination without creating the intermediary text/JSON files in Azure Blob Storage. 

> [!NOTE]
> Per semplicità e chiarezza, i frammenti di codice seguenti includono password di database fittizie in modo esplicito inline, ma è consigliabile usare sempre i segreti di Azure Databricks.For clarity and simplicity, the code snippets below include dummy database passwords explicitly inline, but you should always use Azure Databricks secrets.
>

Innanzitutto, creiamo e associamo il [connettore SQL](https://docs.databricks.com/data/data-sources/sql-databases-azure.html) e le librerie di database Cosmos di Azure necessarie al cluster Azure Databricks.First, we create and attach the required SQL connector and [Azure Cosmos DB connector](https://docs.databricks.com/data/data-sources/azure/cosmosdb-connector.html) libraries to our Azure Databricks cluster. Riavviare il cluster per assicurarsi che le librerie siano caricate.

![Databricks](./media/migrate-relational-to-cosmos-sql-api/databricks1.png)

Successivamente, presentiamo due esempi, per Scala e Python. 

### <a name="scala"></a>Scala
Qui, otteniamo i risultati della query SQL con l'output "FOR JSON" in un frame di dati:

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

Successivamente, ci colleghiamo al nostro database Cosmos DB e alla nostra raccolta:

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

Infine, definiamo il nostro schema e usiamo from_json per applicare il Frame di dati prima di salvarlo nella raccolta CosmosDB.

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

Come approccio alternativo, potrebbe essere necessario eseguire trasformazioni JSON in Spark (se il database di origine non supporta "FOR JSON" o un'operazione simile) oppure è possibile usare operazioni parallele per un set di dati molto grande. Qui presentiamo un esempio PySpark. Iniziare configurando le connessioni al database di origine e di destinazione nella prima cella:

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

Quindi, verrà eseguito una query del Database di origine (in questo caso SQL Server) per i record di dettaglio dell'ordine e dell'ordine, inserendo i risultati in set di dati Spark. Verrà inoltre creato un elenco contenente tutti gli ID ordine e un pool di thread per le operazioni parallele:We will also create a list containing all the order IDs, and a Thread pool for parallel operations:

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

Creare quindi una funzione per la scrittura di Orders nella raccolta di API SQL di destinazione. Questa funzione filtra tutti i dettagli dell'ordine per l'ID ordine specificato, li convertirà in una matrice JSON e inserirà la matrice in un documento JSON che scriverà nella raccolta di API SQL di destinazione per tale ordine:This function will filter all order details for the given order ID, convert them into a JSON array, and insert the array into a JSON document that we will write into the target SQL API Collection for that order:

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

Infine, chiameremo quanto sopra utilizzando una funzione di mappa nel pool di thread, da eseguire in parallelo, passando l'elenco degli ID ordine che abbiamo creato in precedenza:

```python
#map order details to orders in parallel using the above function
pool.map(writeOrder, orderids)
```
In entrambi gli approcci, alla fine, dovremmo ottenere correttamente salvato embedded OrderDetails all'interno di ogni documento Order nella raccolta Cosmos DB:

![Databricks](./media/migrate-relational-to-cosmos-sql-api/databricks4.png)

## <a name="next-steps"></a>Passaggi successivi
* Informazioni sulla [modellazione dei dati in Azure Cosmos DBLearn about data modeling in Azure Cosmos DB](https://docs.microsoft.com/azure/cosmos-db/modeling-data)
* Informazioni su [come modellare e partizionare](https://docs.microsoft.com/azure/cosmos-db/how-to-model-partition-example) i dati in Azure Cosmos DB
