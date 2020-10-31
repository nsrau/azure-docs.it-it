---
title: Leggere i dati della tabella API Apache Cassandra con Spark
titleSufix: Azure Cosmos DB
description: Questo articolo descrive come leggere i dati delle tabelle API Apache Cassandra in Azure Cosmos DB.
author: TheovanKraay
ms.author: thvankra
ms.reviewer: sngun
ms.service: cosmos-db
ms.subservice: cosmosdb-cassandra
ms.topic: how-to
ms.date: 06/02/2020
ms.custom: seodec18
ms.openlocfilehash: ceede96cbf3be12a6129e27d34e318e4c4163458
ms.sourcegitcommit: 3bdeb546890a740384a8ef383cf915e84bd7e91e
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/30/2020
ms.locfileid: "93073497"
---
# <a name="read-data-from-azure-cosmos-db-cassandra-api-tables-using-spark"></a>Leggere i dati dalle tabelle di API Apache Cassandra di Azure Cosmos DB con Spark
[!INCLUDE[appliesto-cassandra-api](includes/appliesto-cassandra-api.md)]

 Questo articolo descrive come leggere i dati archiviati nell'API Cassandra di Azure Cosmos DB da Spark.

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

### <a name="read-table-using-sessionreadformat-command"></a>Leggere le tabelle usando il comando session.read.format

```scala
val readBooksDF = sqlContext
  .read
  .format("org.apache.spark.sql.cassandra")
  .options(Map( "table" -> "books", "keyspace" -> "books_ks"))
  .load

readBooksDF.explain
readBooksDF.show
```
### <a name="read-table-using-sparkreadcassandraformat"></a>Leggere le tabelle usando spark.read.cassandraFormat 

```scala
val readBooksDF = spark.read.cassandraFormat("books", "books_ks", "").load()
```

### <a name="read-specific-columns-in-table"></a>Leggere colonne specifiche in una tabella

```scala
val readBooksDF = spark
  .read
  .format("org.apache.spark.sql.cassandra")
  .options(Map( "table" -> "books", "keyspace" -> "books_ks"))
  .load
  .select("book_name","book_author", "book_pub_year")

readBooksDF.printSchema
readBooksDF.explain
readBooksDF.show
```

### <a name="apply-filters"></a>Applicare filtri

È possibile eseguire il push dei predicati nel database per consentire query Spark ottimizzate in modo ottimale. Un predicato è una condizione per una query che restituisce true o false, in genere situato nella clausola WHERE. Un predicato push filtra i dati nella query del database, riducendo il numero di voci recuperate dal database e migliorando le prestazioni di esecuzione delle query. Per impostazione predefinita, l'API del set di dati Spark eseguirà automaticamente il push delle clausole WHERE valide nel database. 

```scala
val df = spark.read.cassandraFormat("books", "books_ks").load
df.explain
val dfWithPushdown = df.filter(df("book_pub_year") > 1891)
dfWithPushdown.explain

readBooksDF.printSchema
readBooksDF.explain
readBooksDF.show
```

La sezione PushedFilters del piano fisico include il filtro di push di GreaterThan. 

:::image type="content" source="./media/cassandra-spark-read-ops/pushdown-predicates.png" alt-text="partizioni":::

## <a name="rdd-api"></a>API RDD

### <a name="read-table"></a>Leggere una tabella
```scala
val bookRDD = sc.cassandraTable("books_ks", "books")
bookRDD.take(5).foreach(println)
```

### <a name="read-specific-columns-in-table"></a>Leggere colonne specifiche in una tabella

```scala
val booksRDD = sc.cassandraTable("books_ks", "books").select("book_id","book_name").cache
booksRDD.take(5).foreach(println)
```

## <a name="sql-views"></a>Viste SQL 

### <a name="create-a-temporary-view-from-a-dataframe"></a>Creare una vista temporanea da un dataframe

```scala
spark
  .read
  .format("org.apache.spark.sql.cassandra")
  .options(Map( "table" -> "books", "keyspace" -> "books_ks"))
  .load.createOrReplaceTempView("books_vw")
```

### <a name="run-queries-against-the-view"></a>Eseguire query sulla vista

```sql
select * from books_vw where book_pub_year > 1891
```

## <a name="next-steps"></a>Passaggi successivi

Di seguito sono indicati altri articoli sull'uso dell'API Cassandra di Azure Cosmos DB da Spark:
 
 * [Operazioni di upsert](cassandra-spark-upsert-ops.md)
 * [Operazioni Delete](cassandra-spark-delete-ops.md)
 * [Operazioni di aggregazione](cassandra-spark-aggregation-ops.md)
 * [Operazioni di copia di tabelle](cassandra-spark-table-copy-ops.md)

