---
title: Creare/inserire dati nell'API Cassandra di Azure Cosmos DB da Spark
description: Questo articolo illustra in dettaglio come inserire dati di esempio nelle tabelle dell'API Cassandra di Azure Cosmos DB
services: cosmos-db
author: anagha-microsoft
ms.service: cosmos-db
ms.component: cosmosdb-cassandra
ms.devlang: spark-scala
ms.topic: conceptual
ms.date: 09/24/2018
ms.author: ankhanol
ms.openlocfilehash: 74578dc7e69a1454e815679cf403839c3b7df4d7
ms.sourcegitcommit: ad08b2db50d63c8f550575d2e7bb9a0852efb12f
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 09/26/2018
ms.locfileid: "47220404"
---
# <a name="createinsert-data-into-azure-cosmos-db-cassandra-api-from-spark"></a>Creare/inserire dati nell'API Cassandra di Azure Cosmos DB da Spark
 
Questo articolo descrive come inserire dati di esempio in una tabella nell'API Cassandra di Azure Cosmos DB da Spark.

## <a name="cassandra-api-configuration"></a>Configurazione dell'API Cassandra

```scala
import org.apache.spark.sql.cassandra._
//Spark connector
import com.datastax.spark.connector._
import com.datastax.spark.connector.cql.CassandraConnector

//CosmosDB library for multiple retry
import com.microsoft.azure.cosmosdb.cassandra

//Connection-related
spark.conf.set("spark.cassandra.connection.host","YOUR_ACCOUNT_NAME.cassandra.cosmosdb.azure.com")
spark.conf.set("spark.cassandra.connection.port","10350")
spark.conf.set("spark.cassandra.connection.ssl.enabled","true")
spark.conf.set("spark.cassandra.auth.username","YOUR_ACCOUNT_NAME")
spark.conf.set("spark.cassandra.auth.password","YOUR_ACCOUNT_KEY")
spark.conf.set("spark.cassandra.connection.factory", "com.microsoft.azure.cosmosdb.cassandra.CosmosDbConnectionFactory")
//Throughput-related...adjust as needed
spark.conf.set("spark.cassandra.output.batch.size.rows", "1")
spark.conf.set("spark.cassandra.connection.connections_per_executor_max", "10")
spark.conf.set("spark.cassandra.output.concurrent.writes", "1000")
spark.conf.set("spark.cassandra.concurrent.reads", "512")
spark.conf.set("spark.cassandra.output.batch.grouping.buffer.size", "1000")
spark.conf.set("spark.cassandra.connection.keep_alive_ms", "600000000")
```
## <a name="dataframe-api"></a>API dataframe

### <a name="create-a-dataframe-with-sample-data"></a>Creare un frame di dati con dati di esempio

```scala
// Generate a dataframe containing five records
val booksDF = Seq(
   ("b00001", "Arthur Conan Doyle", "A study in scarlet", 1887),
   ("b00023", "Arthur Conan Doyle", "A sign of four", 1890),
   ("b01001", "Arthur Conan Doyle", "The adventures of Sherlock Holmes", 1892),
   ("b00501", "Arthur Conan Doyle", "The memoirs of Sherlock Holmes", 1893),
   ("b00300", "Arthur Conan Doyle", "The hounds of Baskerville", 1901)
).toDF("book_id", "book_author", "book_name", "book_pub_year")

//Review schema
booksDF.printSchema

//Print
booksDF.show
```

> [!NOTE]
> La funzionalità "Crea se non esiste", a livello di riga, non è ancora supportata.

### <a name="persist-to-azure-cosmos-db-cassandra-api"></a>Salvare i dati nell'API Cassandra di Azure Cosmos DB

Durante il salvataggio dei dati, è anche possibile specificare le impostazioni di durata e dei criteri di coerenza come illustrato nell'esempio seguente:

```scala
//Persist
booksDF.write
  .mode("append")
  .format("org.apache.spark.sql.cassandra")
  .options(Map( "table" -> "books", "keyspace" -> "books_ks", "output.consistency.level" -> "ALL", "ttl" -> "10000000"))
  .save()
```

> [!NOTE]
> La durata (TTL) a livello di colonna non è ancora supportata.

#### <a name="validate-in-cqlsh"></a>Convalidare in cqlsh

```sql
use books_ks;
select * from books;
```

## <a name="resilient-distributed-database-rdd-api"></a>API RDD (Resilient Distributed Database)

### <a name="create-a-rdd-with-sample-data"></a>Creare un database RDD con dati di esempio
```scala
//Delete records created in the previous section 
val cdbConnector = CassandraConnector(sc)
cdbConnector.withSessionDo(session => session.execute("delete from books_ks.books where book_id in ('b00300','b00001','b00023','b00501','b09999','b01001','b00999','b03999','b02999');"))

//Create RDD
val booksRDD = sc.parallelize(Seq(
   ("b00001", "Arthur Conan Doyle", "A study in scarlet", 1887),
   ("b00023", "Arthur Conan Doyle", "A sign of four", 1890),
   ("b01001", "Arthur Conan Doyle", "The adventures of Sherlock Holmes", 1892),
   ("b00501", "Arthur Conan Doyle", "The memoirs of Sherlock Holmes", 1893),
   ("b00300", "Arthur Conan Doyle", "The hounds of Baskerville", 1901)
))

//Review
booksRDD.take(2).foreach(println)
```

> [!NOTE]
> La funzionalità "Crea se non esiste" non è ancora supportata.

### <a name="persist-to-azure-cosmos-db-cassandra-api"></a>Salvare i dati nell'API Cassandra di Azure Cosmos DB

Durante il salvataggio dei dati nll'API Cassandra, è anche possibile specificare le impostazioni di durata e dei criteri di coerenza come illustrato nell'esempio seguente:

```scala
import com.datastax.spark.connector.writer._

//Persist
booksRDD.saveToCassandra("books_ks", "books", SomeColumns("book_id", "book_author", "book_name", "book_pub_year"),writeConf = WriteConf(ttl = TTLOption.constant(900000),consistencyLevel = ConsistencyLevel.ALL))
```

#### <a name="validate-in-cqlsh"></a>Convalidare in cqlsh

```sql
use books_ks;
select * from books;
```

## <a name="next-steps"></a>Passaggi successivi

Dopo aver inserito i dati nella tabella dell'API Cassandra di Azure Cosmos DB, passare agli articoli seguenti per eseguire altre operazioni sui dati archiviati nell'API Cassandra di Azure Cosmos DB:
 
* [Operazioni di lettura](cassandra-spark-read-ops.md)
* [Operazioni di upsert](cassandra-spark-upsert-ops.md)
* [Operazioni di eliminazione](cassandra-spark-delete-ops.md)
* [Operazioni di aggregazione](cassandra-spark-aggregation-ops.md)
* [Operazioni di copia di tabelle](cassandra-spark-table-copy-ops.md)

