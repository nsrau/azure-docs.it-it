---
title: Operazioni DDL nell'API Cassandra di Azure Cosmos DB da Spark
description: Questo articolo descrive in dettaglio le operazioni DDL della tabella e del keyspace nell'API Cassandra di Azure Cosmos DB da Spark.
author: TheovanKraay
ms.author: thvankra
ms.reviewer: sngun
ms.service: cosmos-db
ms.subservice: cosmosdb-cassandra
ms.topic: how-to
ms.date: 10/07/2020
ms.openlocfilehash: 589114fa004c8b4479e1a14c5a99161dd972c5bf
ms.sourcegitcommit: b87c7796c66ded500df42f707bdccf468519943c
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/08/2020
ms.locfileid: "91841102"
---
# <a name="ddl-operations-in-azure-cosmos-db-cassandra-api-from-spark"></a>Operazioni DDL nell'API Cassandra di Azure Cosmos DB da Spark

Questo articolo descrive in dettaglio le operazioni DDL della tabella e del keyspace nell'API Cassandra di Azure Cosmos DB da Spark.

## <a name="cassandra-api-related-configuration"></a>Configurazione relativa all'API Cassandra 

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

## <a name="keyspace-ddl-operations"></a>Operazioni DDL del keyspace

### <a name="create-a-keyspace"></a>Creare un keyspace

```scala
//Cassandra connector instance
val cdbConnector = CassandraConnector(sc)

// Create keyspace
cdbConnector.withSessionDo(session => session.execute("CREATE KEYSPACE IF NOT EXISTS books_ks WITH REPLICATION = {'class': 'SimpleStrategy', 'replication_factor': 1 } "))
```

#### <a name="validate-in-cqlsh"></a>Convalidare in cqlsh

Dopo aver eseguito il comando seguente in cqlsh, viene visualizzato il keyspace creato in precedenza.

```bash
DESCRIBE keyspaces;
```

### <a name="drop-a-keyspace"></a>Eliminare un keyspace

```scala
val cdbConnector = CassandraConnector(sc)
cdbConnector.withSessionDo(session => session.execute("DROP KEYSPACE books_ks"))
```

#### <a name="validate-in-cqlsh"></a>Convalidare in cqlsh

```bash
DESCRIBE keyspaces;
```
## <a name="table-ddl-operations"></a>Operazioni DDL della tabella

**Considerazioni:**  

- È possibile assegnare la velocità effettiva a livello di tabella usando l'istruzione CREATE TABLE.  
- Una chiave di partizione può archiviare 20 GB di dati.  
- Un record può archiviare un massimo di 2 MB di dati.  
- Un intervallo di chiavi di partizione può archiviare più chiavi di partizione.

### <a name="create-a-table"></a>Creare una tabella

```scala
cdbConnector.withSessionDo(session => session.execute("CREATE TABLE IF NOT EXISTS books_ks1.books(book_id TEXT,book_author TEXT, book_name TEXT,book_pub_year INT,book_price FLOAT, PRIMARY KEY(book_id,book_pub_year)) WITH cosmosdb_provisioned_throughput=4000 , WITH default_time_to_live=630720000;"))
```

#### <a name="validate-in-cqlsh"></a>Convalidare in cqlsh

Dopo aver eseguito il comando seguente in cqlsh, viene visualizzata una tabella denominata "books": 

```bash
USE books_ks;
DESCRIBE books;
```

La velocità effettiva con provisioning e i valori di durata predefiniti non vengono visualizzati nell'output del comando precedente. È possibile ottenere questi valori dal portale.

### <a name="alter-table"></a>Modificare una tabella

È possibile modificare i valori seguenti usando il comando ALTER TABLE:

* velocità effettiva con provisioning 
* valore durata TTL
<br>Le modifiche delle colonne non sono attualmente supportate.

```scala
val cdbConnector = CassandraConnector(sc)
cdbConnector.withSessionDo(session => session.execute("ALTER TABLE books_ks.books WITH cosmosdb_provisioned_throughput=8000, WITH default_time_to_live=0;"))
```

### <a name="drop-table"></a>Eliminare una tabella

```scala
val cdbConnector = CassandraConnector(sc)
cdbConnector.withSessionDo(session => session.execute("DROP TABLE IF EXISTS books_ks.books;"))
```

#### <a name="validate-in-cqlsh"></a>Convalidare in cqlsh

Dopo aver eseguito il comando seguente in cqlsh, la tabella "books" non è più disponibile:

```bash
USE books_ks;
DESCRIBE tables;
```

## <a name="next-steps"></a>Passaggi successivi

Dopo aver creato il keyspace e la tabella, passare agli articoli seguenti per le operazioni CRUD e altro ancora:
 
* [Operazioni di creazione e inserimento](cassandra-spark-create-ops.md)  
* [Operazioni di lettura](cassandra-spark-read-ops.md)  
* [Operazioni di upsert](cassandra-spark-upsert-ops.md)  
* [Operazioni Delete](cassandra-spark-delete-ops.md)  
* [Operazioni di aggregazione](cassandra-spark-aggregation-ops.md)  
* [Operazioni di copia di tabelle](cassandra-spark-table-copy-ops.md)  
