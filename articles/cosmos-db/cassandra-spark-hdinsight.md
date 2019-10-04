---
title: Accedere all'API Cassandra di Azure Cosmos DB da Spark in YARN con HDInsight
description: Questo articolo illustra come usare l'API Cassandra di Azure Cosmos DB da Spark in YARN con HDInsight
author: kanshiG
ms.author: govindk
ms.reviewer: sngun
ms.service: cosmos-db
ms.subservice: cosmosdb-cassandra
ms.topic: conceptual
ms.date: 09/24/2018
ms.openlocfilehash: f728baedf9e325f224ce52e64325064f553d2671
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/13/2019
ms.locfileid: "60893703"
---
# <a name="access-azure-cosmos-db-cassandra-api-from-spark-on-yarn-with-hdinsight"></a>Accedere all'API Cassandra di Azure Cosmos DB da Spark in YARN con HDInsight

Questo articolo illustra come accedere all'API Cassandra di Azure Cosmos DB da Spark in YARN con HDInsight Spark dalla shell di Spark. HDInsight è il servizio PaaS Hortonworks Hadoop di Microsoft in Azure che sfrutta l'archiviazione di oggetti per HDFS ed è disponibile in diverse versioni, tra cui [Spark](../hdinsight/spark/apache-spark-overview.md).  Il contenuto di questo documento, anche se si riferisce a HDInsight Spark, è applicabile a tutte le distribuzioni di Hadoop.  

## <a name="prerequisites"></a>Prerequisiti

* [Effettuare il provisioning dell'API Cassandra di Azure Cosmos DB](create-cassandra-dotnet.md#create-a-database-account)

* [Esaminare le informazioni di base della connessione all'API Cassandra di Azure Cosmos DB](cassandra-spark-generic.md)

* [Effettuare il provisioning di un cluster HDInsight Spark](../hdinsight/spark/apache-spark-jupyter-spark-sql.md)

* [Esaminare gli esempi di codice per usare l'API Cassandra](cassandra-spark-generic.md#next-steps)

* [Usare cqlsh per la convalida, se si preferisce](cassandra-spark-generic.md##connecting-to-azure-cosmos-db-cassandra-api-from-spark)

* **Configurazione dell'API Cassandra in Spark2**: il connettore Spark per Cassandra richiede che i dettagli della connessione a Cassandra vengano inizializzati nel contesto di Spark. Quando si avvia un notebook di Jupyter, la sessione e il contesto di Spark sono già inizializzati e non è consigliabile arrestare e reinizializzare il contesto di Spark finché non viene completato con ogni set di configurazione durante l'avvio del notebook di Jupyter predefinito di HDInsight. Una soluzione alternativa consiste nell'aggiungere i dettagli dell'istanza di Cassandra direttamente alla configurazione del servizio Ambari Spark2. Si tratta di un'attività occasionale per ogni cluster ed è necessario riavviare il servizio Spark2.
 
  1. Passare al servizio Ambari Spark2 e selezionare le configurazioni

  2. Passare quindi alle impostazione predefinite di Spark2 personalizzate, aggiungere una nuova proprietà con il codice seguente e riavviare il servizio Spark2:

  ```scala
  spark.cassandra.connection.host=YOUR_COSMOSDB_ACCOUNT_NAME.cassandra.cosmosdb.azure.com<br>
  spark.cassandra.connection.port=10350<br>
  spark.cassandra.connection.ssl.enabled=true<br>
  spark.cassandra.auth.username=YOUR_COSMOSDB_ACCOUNT_NAME<br>
  spark.cassandra.auth.password=YOUR_COSMOSDB_KEY<br>
  ```

## <a name="access-azure-cosmos-db-cassandra-api-from-spark-shell"></a>Accedere all'API Cassandra di Azure Cosmos DB dalla shell di Spark

La shell di Spark viene usata a fini di test/esplorazione.

* Avviare la shell di Spark con le dipendenze di Maven necessarie compatibili con la versione di Spark del cluster.

  ```scala
  spark-shell --packages "com.datastax.spark:spark-cassandra-connector_2.11:2.3.0,com.microsoft.azure.cosmosdb:azure-cosmos-cassandra-spark-helper:1.0.0"
  ```

* Eseguire alcune operazioni DDL e DML

  ```scala
  import org.apache.spark.rdd.RDD
  import org.apache.spark.{SparkConf, SparkContext}

  import spark.implicits._
  import org.apache.spark.sql.functions._
  import org.apache.spark.sql.Column
  import org.apache.spark.sql.types.{StructType, StructField, StringType, IntegerType,LongType,FloatType,DoubleType, TimestampType}
  import org.apache.spark.sql.cassandra._

  //Spark connector
  import com.datastax.spark.connector._
  import com.datastax.spark.connector.cql.CassandraConnector

  //CosmosDB library for multiple retry
  import com.microsoft.azure.cosmosdb.cassandra

  // Specify connection factory for Cassandra
  spark.conf.set("spark.cassandra.connection.factory", "com.microsoft.azure.cosmosdb.cassandra.CosmosDbConnectionFactory")

  // Parallelism and throughput configs
  spark.conf.set("spark.cassandra.output.batch.size.rows", "1")
  spark.conf.set("spark.cassandra.connection.connections_per_executor_max", "10")
  spark.conf.set("spark.cassandra.output.concurrent.writes", "100")
  spark.conf.set("spark.cassandra.concurrent.reads", "512")
  spark.conf.set("spark.cassandra.output.batch.grouping.buffer.size", "1000")
  spark.conf.set("spark.cassandra.connection.keep_alive_ms", "60000000") //Increase this number as needed
  ```

* Eseguire operazioni CRUD

  ```scala
  //1) Create table if it does not exist
  val cdbConnector = CassandraConnector(sc)
  cdbConnector.withSessionDo(session => session.execute("CREATE TABLE IF NOT EXISTS books_ks.books(book_id TEXT PRIMARY KEY,book_author TEXT, book_name TEXT,book_pub_year INT,book_price FLOAT) WITH cosmosdb_provisioned_throughput=4000;"))

  //2) Delete data from potential prior runs
  cdbConnector.withSessionDo(session => session.execute("DELETE FROM books_ks.books WHERE book_id IN ('b00300','b00001','b00023','b00501','b09999','b01001','b00999','b03999','b02999','b000009');"))

  //3) Generate a few rows
  val booksDF = Seq(
   ("b00001", "Arthur Conan Doyle", "A study in scarlet", 1887,11.33),
   ("b00023", "Arthur Conan Doyle", "A sign of four", 1890,22.45),
   ("b01001", "Arthur Conan Doyle", "The adventures of Sherlock Holmes", 1892,19.83),
   ("b00501", "Arthur Conan Doyle", "The memoirs of Sherlock Holmes", 1893,14.22),
   ("b00300", "Arthur Conan Doyle", "The hounds of Baskerville", 1901,12.25)
  ).toDF("book_id", "book_author", "book_name", "book_pub_year","book_price")

  //4) Persist
  booksDF.write.mode("append").format("org.apache.spark.sql.cassandra").options(Map( "table" -> "books", "keyspace" -> "books_ks", "output.consistency.level" -> "ALL", "ttl" -> "10000000")).save()

  //5) Read the data in the table
  spark.read.format("org.apache.spark.sql.cassandra").options(Map( "table" -> "books", "keyspace" -> "books_ks")).load.show
  ```

## <a name="access-azure-cosmos-db-cassandra-api-from-jupyter-notebooks"></a>Accedere all'API Cassandra di Azure Cosmos DB dai notebook di Jupyter

HDInsight Spark include i servizi Zeppelin e Jupyter Notebook. Sono entrambi ambienti di notebook basati sul Web che supportano Scala e Python. I notebook sono ideali per le analisi esplorative interattive e la collaborazione, ma non sono destinati a processi operativi/produttivi.

I notebook di Jupyter seguenti possono essere caricati nel cluster HDInsight Spark e forniscono esempi concreti dell'uso dell'API Cassandra di Azure Cosmos DB. Assicurarsi di esaminare il primo notebook `1.0-ReadMe.ipynb` per verificare la configurazione del servizio Spark per la connessione all'API Cassandra di Azure Cosmos DB.

Scaricare questi notebook da [azure-cosmos-db-cassandra-api-spark-notebooks-jupyter](https://github.com/Azure-Samples/azure-cosmos-db-cassandra-api-spark-notebooks-jupyter/blob/master/scala/) nel proprio computer.
  
### <a name="how-to-upload"></a>Modalità di caricamento:
Quando si avvia Jupyter, passare a Scala. Creare prima una directory e quindi caricare i notebook nella directory. Il pulsante per il caricamento si trova nell'angolo in alto a destra.  

### <a name="how-to-run"></a>Modalità di esecuzione:
Eseguire i notebook e ogni cella dei notebook in sequenza.  Fare clic sul pulsante di esecuzione nella parte superiore di ogni notebook per eseguire tutte le celle o premere MAIUSC+INVIO per eseguire le singole celle.

## <a name="access-with-azure-cosmos-db-cassandra-api-from-your-spark-scala-program"></a>Accedere con l'API Cassandra di Azure Cosmos DB dal programma Spark Scala

Per i processi di produzione automatizzati, i programmi Spark vengono inviati al cluster tramite [spark-submit](https://spark.apache.org/docs/latest/submitting-applications.html).

## <a name="next-steps"></a>Passaggi successivi

* [Come creare un programma Spark Scala in un IDE e inviarlo al cluster HDInsight Spark tramite Livy per l'esecuzione](../hdinsight/spark/apache-spark-create-standalone-application.md)

* [Come connettersi all'API Cassandra di Azure Cosmos DB da un programma Spark Scala](https://github.com/Azure-Samples/azure-cosmos-db-cassandra-api-spark-connector-sample/blob/master/src/main/scala/com/microsoft/azure/cosmosdb/cassandra/SampleCosmosDBApp.scala)

* [Elenco completo di esempi di codice per usare l'API Cassandra](cassandra-spark-generic.md)
