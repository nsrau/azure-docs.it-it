---
title: Operazioni di aggregazione sulle tabelle dell'API Cassandra di Azure Cosmos DB da Spark
description: Questo articolo illustra le operazioni di aggregazione di base per le tabelle dell'API Cassandra di Azure Cosmos DB da Spark
author: kanshiG
ms.author: govindk
ms.reviewer: sngun
ms.service: cosmos-db
ms.subservice: cosmosdb-cassandra
ms.topic: conceptual
ms.date: 09/24/2018
ms.openlocfilehash: 4fbb86f4fbda9b8e521f7465bb8bb3d18602ca13
ms.sourcegitcommit: a60a55278f645f5d6cda95bcf9895441ade04629
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/03/2019
ms.locfileid: "58877466"
---
# <a name="aggregate-operations-on-azure-cosmos-db-cassandra-api-tables-from-spark"></a>Operazioni di aggregazione sulle tabelle dell'API Cassandra di Azure Cosmos DB da Spark 

Questo articolo descrive le operazioni di aggregazione di base per le tabelle dell'API Cassandra di Azure Cosmos DB da Spark. 

> [!NOTE]
> Il filtro sul lato server e l'aggregazione sul lato server attualmente non sono supportati nell'API Cassandra di Azure Cosmos DB.

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
## <a name="sample-data-generator"></a>Generatore di dati campione

```scala
// Generate a simple dataset containing five values
val booksDF = Seq(
   ("b00001", "Arthur Conan Doyle", "A study in scarlet", 1887,11.33),
   ("b00023", "Arthur Conan Doyle", "A sign of four", 1890,22.45),
   ("b01001", "Arthur Conan Doyle", "The adventures of Sherlock Holmes", 1892,19.83),
   ("b00501", "Arthur Conan Doyle", "The memoirs of Sherlock Holmes", 1893,14.22),
   ("b00300", "Arthur Conan Doyle", "The hounds of Baskerville", 1901,12.25)
).toDF("book_id", "book_author", "book_name", "book_pub_year","book_price")

booksDF.write
  .mode("append")
  .format("org.apache.spark.sql.cassandra")
  .options(Map( "table" -> "books", "keyspace" -> "books_ks", "output.consistency.level" -> "ALL", "ttl" -> "10000000"))
  .save()
```

## <a name="count-operation"></a>Operazione di conteggio


### <a name="rdd-api"></a>API RDD

```scala
sc.cassandraTable("books_ks", "books").count
```

**Output:**
```
res48: Long = 5
```

### <a name="dataframe-api"></a>API dataframe

Il conteggio sui dataframe sono è attualmente supportato.  L'esempio seguente illustra come eseguire un conteggio di dataframe dopo il salvataggio permanente del dataframe in memoria come soluzione alternativa.

Scegliere un'[opzione di archiviazione]( https://spark.apache.org/docs/2.2.0/rdd-programming-guide.html#which-storage-level-to-choose) tra le opzioni disponibili seguenti, per evitare di incorrere in problemi di "memoria insufficiente":

* MEMORY_ONLY: questa è l'opzione di archiviazione predefinita. Archivia i set di dati RDD come oggetti Java deserializzati nella JVM. Se il set di dati RDD non entra nella memoria, alcune partizioni non vengono messe in cache e vengono ricalcolate in tempo reale ogni volta che sono necessarie.

* MEMORY_AND_DISK: Archivia i set di dati RDD come oggetti Java deserializzati nella JVM. Se il set di dati RDD non entra nella memoria, archiviare le partizioni che non entrano nel disco e, quando necessario, leggerle dal percorso in cui sono archiviate.

* MEMORY_ONLY_SER (Java/Scala): archivia i set di dati RDD come oggetti Java serializzati, con una matrice da un byte per ogni partizione. Questa opzione risparmia spazio rispetto agli oggetti deserializzati, soprattutto quando si usa un serializzatore veloce, ma la lettura richiede più lavoro della CPU.

* MEMORY_AND_DISK_SER (Java/Scala): questa opzione di archiviazione è simile a MEMORY_ONLY_SER. L'unica differenza è che elimina le partizioni che non rientrano nella memoria del disco invece di ricalcolarle quando servono.

* DISK_ONLY: archivia le partizioni RDD solo sul disco.

* MEMORY_ONLY_2, MEMORY_AND_DISK_2…: uguale ai livelli precedenti, ma replica ogni partizione in due nodi del cluster.

* OFF_HEAP (sperimentale): simile a MEMORY_ONLY_SER, ma archivia i dati nella memoria non heap e richiede che la memoria non heap sia attivata anticipatamente. 

```scala
//Workaround
import org.apache.spark.storage.StorageLevel

//Read from source
val readBooksDF = spark
  .read
  .cassandraFormat("books", "books_ks", "")
  .load()

//Explain plan
readBooksDF.explain

//Materialize the dataframe
readBooksDF.persist(StorageLevel.MEMORY_ONLY)

//Subsequent execution against this DF hits the cache 
readBooksDF.count

//Persist as temporary view
readBooksDF.createOrReplaceTempView("books_vw")
```

### <a name="sql"></a>SQL

```sql
select * from books_vw;
select count(*) from books_vw where book_pub_year > 1900;
select count(book_id) from books_vw;
select book_author, count(*) as count from books_vw group by book_author;
select count(*) from books_vw;
```

## <a name="average-operation"></a>Operazione per la determinazione della media

### <a name="rdd-api"></a>API RDD

```scala
sc.cassandraTable("books_ks", "books").select("book_price").as((c: Double) => c).mean
```

**Output:**
```
res24: Double = 16.016000175476073
```

### <a name="dataframe-api"></a>API dataframe

```scala
spark
  .read
  .cassandraFormat("books", "books_ks", "")
  .load()
  .select("book_price")
  .agg(avg("book_price"))
  .show
```

**Output:**
```
+------------------+
|   avg(book_price)|
+------------------+
|16.016000175476073|
+------------------+
```

### <a name="sql"></a>SQL

```sql
select avg(book_price) from books_vw;
```
**Output:**
```
16.016000175476073
```

## <a name="min-operation"></a>Operazione per la determinazione del minimo

### <a name="rdd-api"></a>API RDD

```scala
sc.cassandraTable("books_ks", "books").select("book_price").as((c: Float) => c).min
```

**Output:**
```
res31: Float = 11.33
```

### <a name="dataframe-api"></a>API dataframe

```scala
spark
  .read
  .cassandraFormat("books", "books_ks", "")
  .load()
  .select("book_id","book_price")
  .agg(min("book_price"))
  .show
```

**Output:**
```
+---------------+
|min(book_price)|
+---------------+
|          11.33|
+---------------+
```

### <a name="sql"></a>SQL

```sql
select min(book_price) from books_vw;
```

**Output:**
```
11.33
```

## <a name="max-operation"></a>Operazione per la determinazione del massimo

### <a name="rdd-api"></a>API RDD

```scala
sc.cassandraTable("books_ks", "books").select("book_price").as((c: Float) => c).max
```

### <a name="dataframe-api"></a>API dataframe

```scala 
spark
  .read
  .cassandraFormat("books", "books_ks", "")
  .load()
  .select("book_price")
  .agg(max("book_price"))
  .show
```

**Output:**
```
+---------------+
|max(book_price)|
+---------------+
|          22.45|
+---------------+
```

### <a name="sql"></a>SQL

```sql
select max(book_price) from books_vw;
```
**Output:**
```
22.45
```

## <a name="sum-operation"></a>Operazione per la determinazione della somma

### <a name="rdd-api"></a>API RDD

```scala
sc.cassandraTable("books_ks", "books").select("book_price").as((c: Float) => c).sum
```

**Output:**
```
res46: Double = 80.08000087738037
```

### <a name="dataframe-api"></a>API dataframe

```scala
spark
  .read
  .cassandraFormat("books", "books_ks", "")
  .load()
  .select("book_price")
  .agg(sum("book_price"))
  .show
```
**Output:**
```
+-----------------+
|  sum(book_price)|
+-----------------+
|80.08000087738037|
+-----------------+
```

### <a name="sql"></a>SQL

```sql
select sum(book_price) from books_vw;
```

**Output:**
```
80.08000087738037
```

## <a name="top-or-comparable-operation"></a>Operazione per la determinazione dei primi elementi o di elementi confrontabili

### <a name="rdd-api"></a>API RDD

```scala
val readCalcTopRDD = sc.cassandraTable("books_ks", "books").select("book_name","book_price").sortBy(_.getFloat(1), false)
readCalcTopRDD.zipWithIndex.filter(_._2 < 3).collect.foreach(println)
//delivers the first top n items without collecting the rdd to the driver.
```
**Output:**
```
(CassandraRow{book_name: A sign of four, book_price: 22.45},0)
(CassandraRow{book_name: The adventures of Sherlock Holmes, book_price: 19.83},1)
(CassandraRow{book_name: The memoirs of Sherlock Holmes, book_price: 14.22},2)
readCalcTopRDD: org.apache.spark.rdd.RDD[com.datastax.spark.connector.CassandraRow] = MapPartitionsRDD[430] at sortBy at command-2371828989676374:1
```
### <a name="dataframe-api"></a>API dataframe

```scala
import org.apache.spark.sql.functions._

val readBooksDF = spark.read.format("org.apache.spark.sql.cassandra")
  .options(Map( "table" -> "books", "keyspace" -> "books_ks"))
  .load
  .select("book_name","book_price")
  .orderBy(desc("book_price"))
  .limit(3)

//Explain plan
readBooksDF.explain

//Top
readBooksDF.show
```

**Output:**
```
== Physical Plan ==
TakeOrderedAndProject(limit=3, orderBy=[book_price#1840 DESC NULLS LAST], output=[book_name#1839,book_price#1840])
+- *(1) Scan org.apache.spark.sql.cassandra.CassandraSourceRelation@29cd5f58 [book_name#1839,book_price#1840] PushedFilters: [], ReadSchema: struct<book_name:string,book_price:float>
+--------------------+----------+
|           book_name|book_price|
+--------------------+----------+
|      A sign of four|     22.45|
|The adventures of...|     19.83|
|The memoirs of Sh...|     14.22|
+--------------------+----------+

import org.apache.spark.sql.functions._
readBooksDF: org.apache.spark.sql.Dataset[org.apache.spark.sql.Row] = [book_name: string, book_price: float]
```

### <a name="sql"></a>SQL

```sql
select book_name,book_price from books_vw order by book_price desc limit 3;
```

## <a name="next-steps"></a>Passaggi successivi

Per eseguire operazioni di copia di tabelle, vedere:

* [Operazioni di copia di tabelle](cassandra-spark-table-copy-ops.md)
