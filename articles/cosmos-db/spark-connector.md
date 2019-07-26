---
title: Connettere Apache Spark a Azure Cosmos DB
description: Informazioni sul connettore Spark per Azure Cosmos DB, che permette di connettere Apache Spark ad Azure Cosmos DB.
author: tknandu
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/21/2019
ms.author: ramkris
ms.openlocfilehash: 2a0e88a439400bc36873ed7160b8eb039a16ebfb
ms.sourcegitcommit: 4b647be06d677151eb9db7dccc2bd7a8379e5871
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/19/2019
ms.locfileid: "68356184"
---
# <a name="accelerate-big-data-analytics-by-using-the-apache-spark-to-azure-cosmos-db-connector"></a>Accelerare l'analisi di Big Data con il connettore Apache Spark per Azure Cosmos DB

È possibile eseguire processi [Spark](https://spark.apache.org/) con i dati archiviati in Azure Cosmos DB usando il connettore Spark Cosmos DB. Cosmos può essere usato per l'elaborazione di batch e flussi e come livello di servizio per l'accesso a bassa latenza.

È possibile usare il connettore con [Azure Databricks](https://azure.microsoft.com/services/databricks) o [Azure HDInsight](https://azure.microsoft.com/services/hdinsight/), che forniscono cluster Spark gestiti in Azure. La tabella seguente illustra le versioni di Spark supportate.

| Componente | Version |
|---------|-------|
| Apache Spark | 2.4. x, 2.3. x, 2.2. x e 2.1. x |
| Scala | 2.11 |
| Versione del runtime di Azure Databricks | > 3.4 |

> [!WARNING]
> Questo connettore supporta l'API principale (SQL) di Azure Cosmos DB.
> Per Cosmos DB per l'API MongoDB, usare il [connettore Spark MongoDB](https://docs.mongodb.com/spark-connector/master/).
> Per Cosmos DB API Cassandra, usare il [connettore Cassandra Spark](https://github.com/datastax/spark-cassandra-connector).
>

## <a name="quickstart"></a>Avvio rapido

* Per configurare un account di Cosmos DB e popolare alcuni dati, seguire la procedura descritta in [Introduzione a Java SDK](sql-api-async-java-get-started.md) .
* Seguire la procedura descritta in [Azure Databricks Introduzione](https://docs.azuredatabricks.net/getting-started/index.html) alla configurazione di un'area di lavoro Azure Databricks e di un cluster.
* È ora possibile creare nuovi notebook e importare la libreria del connettore Cosmos DB. Per informazioni dettagliate su come configurare l'area di lavoro, passare all' [uso del connettore Cosmos DB](#bk_working_with_connector) .
* La sezione seguente contiene frammenti di codice su come leggere e scrivere usando il connettore.

### <a name="batch-reads-from-cosmos-db"></a>Letture batch da Cosmos DB

Il frammento di codice seguente illustra come creare un dataframe Spark per leggere da Cosmos DB in PySpark.

```python
# Read Configuration
readConfig = {
    "Endpoint": "https://doctorwho.documents.azure.com:443/",
    "Masterkey": "YOUR-KEY-HERE",
    "Database": "DepartureDelays",
    "Collection": "flights_pcoll",
    "query_custom": "SELECT c.date, c.delay, c.distance, c.origin, c.destination FROM c WHERE c.origin = 'SEA'" // Optional
}

# Connect via azure-cosmosdb-spark to create Spark DataFrame
flights = spark.read.format(
    "com.microsoft.azure.cosmosdb.spark").options(**readConfig).load()
flights.count()
```

E lo stesso frammento di codice in scala:

```scala
// Import Necessary Libraries
import com.microsoft.azure.cosmosdb.spark.schema._
import com.microsoft.azure.cosmosdb.spark._
import com.microsoft.azure.cosmosdb.spark.config.Config

// Read Configuration
val readConfig = Config(Map(
  "Endpoint" -> "https://doctorwho.documents.azure.com:443/",
  "Masterkey" -> "YOUR-KEY-HERE",
  "Database" -> "DepartureDelays",
  "Collection" -> "flights_pcoll",
  "query_custom" -> "SELECT c.date, c.delay, c.distance, c.origin, c.destination FROM c WHERE c.origin = 'SEA'" // Optional
))

// Connect via azure-cosmosdb-spark to create Spark DataFrame
val flights = spark.read.cosmosDB(readConfig)
flights.count()
```

### <a name="batch-writes-to-cosmos-db"></a>Scritture batch in Cosmos DB

Il frammento di codice seguente mostra come scrivere un frame di dati per Cosmos DB in PySpark.

```python
# Write configuration
writeConfig = {
    "Endpoint": "https://doctorwho.documents.azure.com:443/",
    "Masterkey": "YOUR-KEY-HERE",
    "Database": "DepartureDelays",
    "Collection": "flights_fromsea",
    "Upsert": "true"
}

# Write to Cosmos DB from the flights DataFrame
flights.write.format("com.microsoft.azure.cosmosdb.spark").options(
    **writeConfig).save()
```

E lo stesso frammento di codice in scala:

```scala
// Write configuration

val writeConfig = Config(Map(
  "Endpoint" -> "https://doctorwho.documents.azure.com:443/",
  "Masterkey" -> "YOUR-KEY-HERE",
  "Database" -> "DepartureDelays",
  "Collection" -> "flights_fromsea",
  "Upsert" : "true"
))

// Write to Cosmos DB from the flights DataFrame
import org.apache.spark.sql.SaveMode
flights.write.mode(SaveMode.Overwrite).cosmosDB(writeConfig)
```

### <a name="streaming-reads-from-cosmos-db"></a>Letture in streaming da Cosmos DB

Il frammento di codice seguente illustra come connettersi e leggere da Azure Cosmos DB feed di modifiche.

```python
# Read Configuration
readConfig = {
    "Endpoint": "https://doctorwho.documents.azure.com:443/",
    "Masterkey": "YOUR-KEY-HERE",
    "Database": "DepartureDelays",
    "Collection": "flights_pcoll",
    "ReadChangeFeed": "true",
    "ChangeFeedQueryName": "Departure-Delays",
    "ChangeFeedStartFromTheBeginning": "false",
    "InferStreamSchema": "true",
    "ChangeFeedCheckpointLocation": "dbfs:/Departure-Delays"
}


# Open a read stream to the Cosmos DB Change Feed via azure-cosmosdb-spark to create Spark DataFrame
changes = (spark
           .readStream
           .format("com.microsoft.azure.cosmosdb.spark.streaming.CosmosDBSourceProvider")
           .options(**readConfig)
           .load())
```
E lo stesso frammento di codice in scala:

```scala
// Import Necessary Libraries
import com.microsoft.azure.cosmosdb.spark.schema._
import com.microsoft.azure.cosmosdb.spark._
import com.microsoft.azure.cosmosdb.spark.config.Config

// Read Configuration
val readConfig = Config(Map(
  "Endpoint" -> "https://doctorwho.documents.azure.com:443/",
  "Masterkey" -> "YOUR-KEY-HERE",
  "Database" -> "DepartureDelays",
  "Collection" -> "flights_pcoll",
  "ReadChangeFeed" -> "true",
  "ChangeFeedQueryName" -> "Departure-Delays",
  "ChangeFeedStartFromTheBeginning" -> "false",
  "InferStreamSchema" -> "true",
  "ChangeFeedCheckpointLocation" -> "dbfs:/Departure-Delays"
))

// Open a read stream to the Cosmos DB Change Feed via azure-cosmosdb-spark to create Spark DataFrame
val df = spark.readStream.format(classOf[CosmosDBSourceProvider].getName).options(readConfig).load()
```

### <a name="streaming-writes-to-cosmos-db"></a>Streaming di scritture in Cosmos DB

Il frammento di codice seguente mostra come scrivere un frame di dati per Cosmos DB in PySpark.

```python
# Write configuration
writeConfig = {
    "Endpoint": "https://doctorwho.documents.azure.com:443/",
    "Masterkey": "YOUR-KEY-HERE",
    "Database": "DepartureDelays",
    "Collection": "flights_fromsea",
    "Upsert": "true",
    "WritingBatchSize": "500",
    "CheckpointLocation": "/checkpointlocation_write1"
}

# Write to Cosmos DB from the flights DataFrame
changeFeed = (changes
              .writeStream
              .format("com.microsoft.azure.cosmosdb.spark.streaming.CosmosDBSinkProvider")
              .outputMode("append")
              .options(**writeconfig)
              .start())
```

E lo stesso frammento di codice in scala:

```scala
// Write configuration

val writeConfig = Config(Map(
  "Endpoint" -> "https://doctorwho.documents.azure.com:443/",
  "Masterkey" -> "YOUR-KEY-HERE",
  "Database" -> "DepartureDelays",
  "Collection" -> "flights_fromsea",
  "Upsert" -> "true",
  "WritingBatchSize" -> "500",
  "CheckpointLocation" -> "/checkpointlocation_write1"
))

// Write to Cosmos DB from the flights DataFrame
df
.writeStream
.format(classOf[CosmosDBSinkProvider].getName)
.options(writeConfig)
.start()
```
Per altri frammenti e per gli esempi end-to-end, vedere [Jupyter](https://github.com/Azure/azure-cosmosdb-spark/tree/master/samples/notebooks).

## <a name="bk_working_with_connector"></a>Uso del connettore

È possibile compilare il connettore dall'origine in GitHub oppure scaricare i file jar uber da Maven nei collegamenti seguenti.

| Spark | Scala | Versione più recente |
|---|---|---|
| 2.4.0 | 2.11 | [azure-cosmosdb-spark_2.4.0_2.11_1.4.0](https://search.maven.org/artifact/com.microsoft.azure/azure-cosmosdb-spark_2.4.0_2.11/1.4.0/jar)
| 2.3.0 | 2.11 | [azure-cosmosdb-spark_2.3.0_2.11_1.3.3](https://search.maven.org/artifact/com.microsoft.azure/azure-cosmosdb-spark_2.3.0_2.11/1.3.3/jar)
| 2.2.0 | 2.11 | [azure-cosmosdb-spark_2.2.0_2.11_1.1.1](https://search.maven.org/#artifactdetails%7Ccom.microsoft.azure%7Cazure-cosmosdb-spark_2.2.0_2.11%7C1.1.1%7Cjar)
| 2.1.0 | 2.11 | [azure-cosmosdb-spark_2.1.0_2.11_1.2.2](https://search.maven.org/artifact/com.microsoft.azure/azure-cosmosdb-spark_2.1.0_2.11/1.2.2/jar)

### <a name="using-databricks-notebooks"></a>Uso di notebook di databricks

Creare una libreria usando l'area di lavoro di databricks seguendo le istruzioni riportate nella Guida Azure Databricks > [usare il connettore Spark Azure Cosmos DB](https://docs.azuredatabricks.net/spark/latest/data-sources/azure/cosmosdb-connector.html)

> [!NOTE]
> Si noti che la pagina **Usa il connettore Spark Azure Cosmos DB** non è attualmente aggiornata. Anziché scaricare i sei contenitori separati in sei librerie diverse, è possibile scaricare il file jar uber da Maven in https://search.maven.org/artifact/com.microsoft.azure/azure-cosmosdb-spark_2.4.0_2.11/1.4.0/jar) e installare questo file jar/Library.
> 

### <a name="using-spark-cli"></a>Uso di Spark-CLI

Per usare il connettore usando l'interfaccia della riga di comando di Spark ( `spark-shell`ovvero `pyspark` `spark-submit`,,), è possibile usare `--packages` il parametro con le [Coordinate Maven](https://mvnrepository.com/artifact/com.microsoft.azure/azure-cosmosdb-spark_2.4.0_2.11)del connettore.

```sh
spark-shell --master yarn --packages "com.microsoft.azure:azure-cosmosdb-spark_2.4.0_2.11:1.4.0"

```

### <a name="using-jupyter-notebooks"></a>Uso di notebook Jupyter

Se si usano notebook di Jupyter in HDInsight, è possibile usare la cella Spark- `%%configure` Magic per specificare le coordinate di Maven del connettore.

```python
{ "name":"Spark-to-Cosmos_DB_Connector",
  "conf": {
    "spark.jars.packages": "com.microsoft.azure:azure-cosmosdb-spark_2.4.0_2.11:1.4.0",
    "spark.jars.excludes": "org.scala-lang:scala-reflect"
   }
   ...
}
```

> Si noti che l'inclusione `spark.jars.excludes` del è specifico per rimuovere i potenziali conflitti tra il connettore, Apache Spark e Livio.

### <a name="build-the-connector"></a>Compilare il connettore

Attualmente, questo progetto connettore USA `maven` così per compilare senza dipendenze, è possibile eseguire:

```sh
mvn clean package
```

## <a name="working-with-our-samples"></a>Uso dei nostri esempi

Il [repository GitHub Cosmos DB Spark](https://github.com/Azure/azure-cosmosdb-spark) include i notebook di esempio e gli script seguenti che è possibile provare.

* **Prestazioni in fase di esecuzione del volo con Spark e Cosmos DB (Seattle)** [ipynb](https://github.com/Azure/azure-cosmosdb-spark/blob/master/samples/notebooks/On-Time%20Flight%20Performance%20with%20Spark%20and%20Cosmos%20DB%20-%20Seattle.ipynb)  |  [HTML](https://github.com/Azure/azure-cosmosdb-spark/blob/master/samples/notebooks/On-Time%20Flight%20Performance%20with%20Spark%20and%20Cosmos%20DB%20-%20Seattle.html): Connetti Spark a Cosmos DB usando il servizio HDInsight Jupyter notebook per presentare Spark SQL, GraphFrames e stimare i ritardi dei voli usando le pipeline di ML.
* **Origine Twitter con Apache Spark e Azure Cosmos DB feed di modifiche**: [ipynb](https://github.com/Azure/azure-cosmosdb-spark/blob/master/samples/notebooks/Twitter%20with%20Spark%20and%20Azure%20Cosmos%20DB%20Change%20Feed.ipynb) | [HTML](https://github.com/Azure/azure-cosmosdb-spark/blob/master/samples/notebooks/Twitter%20with%20Spark%20and%20Azure%20Cosmos%20DB%20Change%20Feed.html)
* **Uso di Apache Spark per eseguire query sui grafici Cosmos DB**: [ipynb](https://github.com/Azure/azure-cosmosdb-spark/blob/master/samples/notebooks/Using%20Apache%20Spark%20to%20query%20Cosmos%20DB%20Graphs.ipynb) | [HTML](https://github.com/Azure/azure-cosmosdb-spark/blob/master/samples/notebooks/Using%20Apache%20Spark%20to%20query%20Cosmos%20DB%20Graphs.html)
* **[Connessione Azure Databricks Azure Cosmos DB](https://docs.databricks.com/spark/latest/data-sources/azure/cosmosdb-connector.html)** tramite `azure-cosmosdb-spark`.  Linked Here è anche una versione Azure Databricks del [notebook sulle prestazioni del volo in tempo](https://github.com/dennyglee/databricks/tree/master/notebooks/Users/denny%40databricks.com/azure-databricks).
* **[Architettura lambda con Azure Cosmos DB e HDInsight (Apache Spark)](https://github.com/Azure/azure-cosmosdb-spark/blob/master/samples/lambda/readme.md)** : È possibile ridurre il sovraccarico operativo della gestione di pipeline di Big Data usando Cosmos DB e Spark.

## <a name="more-information"></a>Altre informazioni

Sono disponibili altre informazioni nel `azure-cosmosdb-spark` [wiki](https://github.com/Azure/azure-cosmosdb-spark/wiki) , tra cui:

* [Manuale dell'utente di Azure Cosmos DB Connector Spark](https://github.com/Azure/azure-documentdb-spark/wiki/Azure-Cosmos-DB-Spark-Connector-User-Guide)
* [Esempi di aggregazioni](https://github.com/Azure/azure-documentdb-spark/wiki/Aggregations-Examples)

### <a name="configuration-and-setup"></a>Installazione e configurazione

* [Configurazione del connettore Spark](https://github.com/Azure/azure-cosmosdb-spark/wiki/Configuration-references)
* [Installazione di Spark per Cosmos DB Connector](https://github.com/Azure/azure-documentdb-spark/wiki/Spark-to-Cosmos-DB-Connector-Setup) (In corso)
* [Configurazione di Power BI query diretta per Azure Cosmos DB tramite Apache Spark (HDI)](https://github.com/Azure/azure-cosmosdb-spark/wiki/Configuring-Power-BI-Direct-Query-to-Azure-Cosmos-DB-via-Apache-Spark-(HDI))

### <a name="troubleshooting"></a>risoluzione dei problemi

* [Utilizzo di aggregazioni Cosmos DB](https://github.com/Azure/azure-documentdb-spark/wiki/Troubleshooting:-Using-Cosmos-DB-Aggregates)
* [Problemi noti](https://github.com/Azure/azure-cosmosdb-spark/wiki/Known-Issues)

### <a name="performance"></a>Prestazioni

* [Suggerimenti per incrementare le prestazioni](https://github.com/Azure/azure-cosmosdb-spark/wiki/Performance-tips)
* [Esecuzione di test di query](https://github.com/Azure/azure-documentdb-spark/wiki/Query-Test-Runs)
* [Scrittura di esecuzioni di test](https://github.com/Azure/azure-cosmosdb-spark/wiki/Writing-Test-Runs)

### <a name="change-feed"></a>Feed di modifiche

* [Modifiche all'elaborazione di flussi con Azure Cosmos DB feed di modifiche e Apache Spark](https://github.com/Azure/azure-cosmosdb-spark/wiki/Stream-Processing-Changes-using-Azure-Cosmos-DB-Change-Feed-and-Apache-Spark)
* [Demo del feed delle modifiche](https://github.com/Azure/azure-cosmosdb-spark/wiki/Change-Feed-demos)
* [Demo di flussi strutturati](https://github.com/Azure/azure-cosmosdb-spark/wiki/Structured-Stream-demos)

### <a name="monitoring"></a>Monitoraggio

* [Monitoraggio dei processi Spark con Application Insights](https://github.com/Azure/azure-cosmosdb-spark/tree/2.3/samples/monitoring)

## <a name="next-steps"></a>Passaggi successivi

Se ancora non è stato fatto, scaricare il connettore Spark per Azure Cosmos DB dal repository GitHub [azure-cosmosdb-spark](https://github.com/Azure/azure-cosmosdb-spark). Esplorare le risorse aggiuntive seguenti nel repository:

* [Aggregations examples (Esempi di aggregazioni)](https://github.com/Azure/azure-cosmosdb-spark/wiki/Aggregations-Examples)
* [Sample scripts and notebooks (Notebook e script di esempio)](https://github.com/Azure/azure-cosmosdb-spark/tree/master/samples)

È anche possibile vedere [Apache Spark SQL, DataFrames, and Datasets Guide](https://spark.apache.org/docs/latest/sql-programming-guide.html) (Guida ad Apache Spark SQL, frame di dati e set di dati) e l'articolo su [Apache Spark in Azure HDInsight](../hdinsight/spark/apache-spark-jupyter-spark-sql.md).
