---
title: Connettere Apache Spark a Azure Cosmos DB
description: Informazioni sul connettore Spark per Azure Cosmos DB, che permette di connettere Apache Spark ad Azure Cosmos DB.
author: tknandu
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/21/2019
ms.author: ramkris
ms.openlocfilehash: bbc97489f0c7045040dd0189b97946c2bd8fb3c0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79481637"
---
# <a name="accelerate-big-data-analytics-by-using-the-apache-spark-to-azure-cosmos-db-connector"></a>Accelerare l'analisi di Big Data con il connettore Apache Spark per Azure Cosmos DB

È possibile eseguire [processi Spark](https://spark.apache.org/) con dati archiviati in Azure Cosmos DB usando il connettore Cosmos DB Spark.You can run Spark jobs with data stored in Azure Cosmos DB using the Cosmos DB Spark connector. Cosmos può essere utilizzato per l'elaborazione in batch e flusso e come livello di servizio per l'accesso a bassa latenza.

È possibile usare il connettore con [Azure Databricks](https://azure.microsoft.com/services/databricks) o Azure HDInsight , che forniscono cluster Spark gestiti in Azure.You can use the connector with Azure Databricks or [Azure HDInsight](https://azure.microsoft.com/services/hdinsight/), which provide managed Spark clusters on Azure. Nella tabella seguente vengono illustrate le versioni di Spark supportate.

| Componente | Versione |
|---------|-------|
| Apache Spark | 2.4.x, 2.3.x, 2.2.x e 2.1.x |
| Scala | 2.11 |
| Versione del runtime di Azure Databricks | > 3.4 |

> [!WARNING]
> Questo connettore supporta l'API di base (SQL) di Azure Cosmos DB.
> Per Cosmos DB per l'API MongoDB, utilizzare il [connettore MongoDB Spark](https://docs.mongodb.com/spark-connector/master/).
> Per Cosmos DB Cassandra API, utilizzare il [connettore Cassandra Spark](https://github.com/datastax/spark-cassandra-connector).
>

## <a name="quickstart"></a>Guida introduttiva

* Seguire i passaggi descritti in [Introduzione a Java SDK](sql-api-async-java-get-started.md) per configurare un account Cosmos DB e popolare alcuni dati.
* Seguire i passaggi descritti in Azure Databricks per iniziare a configurare un'area di lavoro e un cluster di Azure Databricks.Follow the steps at [Azure Databricks getting started](/azure/azure-databricks/quickstart-create-databricks-workspace-portal) to set up an Azure Databricks workspace and cluster.
* È ora possibile creare nuovi blocchi appunti e importare la libreria di connettori Cosmos DB. Passare a [Utilizzo del connettore Cosmos DB](#bk_working_with_connector) per informazioni dettagliate su come configurare l'area di lavoro.
* La sezione seguente contiene frammenti su come leggere e scrivere utilizzando il connettore.

### <a name="batch-reads-from-cosmos-db"></a>Letture in batch da Cosmos DB

Il frammento di codice seguente mostra come creare un dataframe Spark da leggere da Cosmos DB in PySpark.The following snippet shows how to create a Spark DataFrame to read from Cosmos DB in PySpark.

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

E lo stesso frammento di codice in Scala:

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

### <a name="batch-writes-to-cosmos-db"></a>Scritture in batch in Cosmos DB

Il frammento di codice seguente mostra come scrivere un frame di dati in Cosmos DB in PySpark.The following snippet shows how to write a data frame to Cosmos DB in PySpark.

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

E lo stesso frammento di codice in Scala:

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

Il frammento di codice seguente mostra come connettersi e leggere da Azure Cosmos DB Change Feed.The following snippet shows how to connect to and read from Azure Cosmos DB Change Feed.

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
E lo stesso frammento di codice in Scala:

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

### <a name="streaming-writes-to-cosmos-db"></a>Streaming di scritture su Cosmos DB

Il frammento di codice seguente mostra come scrivere un frame di dati in Cosmos DB in PySpark.The following snippet shows how to write a data frame to Cosmos DB in PySpark.

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

E lo stesso frammento di codice in Scala:

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
Altri frammenti e esempi end-to-end, vedere [Jupyter](https://github.com/Azure/azure-cosmosdb-spark/tree/master/samples/notebooks).

## <a name="working-with-the-connector"></a><a name="bk_working_with_connector"></a>Utilizzo del connettore

È possibile creare il connettore dalla sorgente in GitHub, o scaricare i vasetti uber da Maven nei link qui sotto.

| Spark | Scala | Versione più recente |
|---|---|---|
| 2.4.0 | 2.11 | [azure-cosmosdb-spark_2.4.0_2.11_1.4.0](https://search.maven.org/artifact/com.microsoft.azure/azure-cosmosdb-spark_2.4.0_2.11/1.4.0/jar)
| 2.3.0 | 2.11 | [azure-cosmosdb-spark_2.3.0_2.11_1.3.3](https://search.maven.org/artifact/com.microsoft.azure/azure-cosmosdb-spark_2.3.0_2.11/1.3.3/jar)
| 2.2.0 | 2.11 | [azure-cosmosdb-spark_2.2.0_2.11_1.1.1](https://search.maven.org/#artifactdetails%7Ccom.microsoft.azure%7Cazure-cosmosdb-spark_2.2.0_2.11%7C1.1.1%7Cjar)
| 2.1.0 | 2.11 | [azure-cosmosdb-spark_2.1.0_2.11_1.2.2](https://search.maven.org/artifact/com.microsoft.azure/azure-cosmosdb-spark_2.1.0_2.11/1.2.2/jar)

### <a name="using-databricks-notebooks"></a>Utilizzo dei blocchi appunti di Databricks

Creare una libreria usando l'area di lavoro Databricks seguendo le indicazioni riportate nella Guida di Azure Databricks > [Usare il connettore Di Azure Cosmos DB SparkCreate](https://docs.azuredatabricks.net/spark/latest/data-sources/azure/cosmosdb-connector.html) a library using your Databricks workspace by following the guidance in the Azure Databricks Guide > Use the Azure Cosmos DB Spark connector

> [!NOTE]
> Si noti che la pagina **Usa Azure Cosmos DB Spark Connector** non è attualmente aggiornata. Invece di scaricare i sei vasetti separati in sei diverse librerie, è possibile scaricare il barattolo uber da maven at https://search.maven.org/artifact/com.microsoft.azure/azure-cosmosdb-spark_2.4.0_2.11/1.4.0/jar) e installare questo barattolo / libreria.
> 

### <a name="using-spark-cli"></a>Utilizzo di spark-cli

Per utilizzare il connettore utilizzando spark-cli( `pyspark` `spark-submit`, ovvero `spark-shell`, `--packages` , ), è possibile utilizzare il parametro con [le coordinate maven](https://mvnrepository.com/artifact/com.microsoft.azure/azure-cosmosdb-spark_2.4.0_2.11)del connettore.

```sh
spark-shell --master yarn --packages "com.microsoft.azure:azure-cosmosdb-spark_2.4.0_2.11:1.4.0"

```

### <a name="using-jupyter-notebooks"></a>Uso dei notebook Jupyter

Se si usano blocchi appunti Jupyter all'interno di HDInsight, è possibile usare la cella spark-magic `%%configure` per specificare le coordinate maven del connettore.

```python
{ "name":"Spark-to-Cosmos_DB_Connector",
  "conf": {
    "spark.jars.packages": "com.microsoft.azure:azure-cosmosdb-spark_2.4.0_2.11:1.4.0",
    "spark.jars.excludes": "org.scala-lang:scala-reflect"
   }
   ...
}
```

> Si noti che `spark.jars.excludes` l'inclusione di è specifica per rimuovere potenziali conflitti tra il connettore, Apache Spark e Livy.

### <a name="build-the-connector"></a>Creare il connettore

Attualmente, questo progetto `maven` di connettore utilizza in modo da compilare senza dipendenze, è possibile eseguire:Currently, this connector project uses so to build without dependencies, you can run:

```sh
mvn clean package
```

## <a name="working-with-our-samples"></a>Lavorare con i nostri campioni

Il [repository Cosmos DB Spark GitHub](https://github.com/Azure/azure-cosmosdb-spark) include i blocchi appunti e gli script di esempio seguenti che è possibile provare.

* Prestazioni di **volo puntuale con Spark e Cosmos DB (Seattle)** [ipynb](https://github.com/Azure/azure-cosmosdb-spark/blob/master/samples/notebooks/On-Time%20Flight%20Performance%20with%20Spark%20and%20Cosmos%20DB%20-%20Seattle.ipynb) | [html](https://github.com/Azure/azure-cosmosdb-spark/blob/master/samples/notebooks/On-Time%20Flight%20Performance%20with%20Spark%20and%20Cosmos%20DB%20-%20Seattle.html): Connect Spark to Cosmos DB utilizzando il servizio notebook HDInsight Jupyter per mostrare Spark SQL, GraphFrames e prevedere i ritardi dei voli utilizzando le pipeline ML.
* **Origine Twitter con Apache Spark e Azure Cosmos DB Change Feed**: [ipynb](https://github.com/Azure/azure-cosmosdb-spark/blob/master/samples/notebooks/Twitter%20with%20Spark%20and%20Azure%20Cosmos%20DB%20Change%20Feed.ipynb) | [html](https://github.com/Azure/azure-cosmosdb-spark/blob/master/samples/notebooks/Twitter%20with%20Spark%20and%20Azure%20Cosmos%20DB%20Change%20Feed.html)
* **Utilizzo di Apache Spark per eseguire query su Cosmos DB Graphs**: [ipynb](https://github.com/Azure/azure-cosmosdb-spark/blob/master/samples/notebooks/Using%20Apache%20Spark%20to%20query%20Cosmos%20DB%20Graphs.ipynb) | [html](https://github.com/Azure/azure-cosmosdb-spark/blob/master/samples/notebooks/Using%20Apache%20Spark%20to%20query%20Cosmos%20DB%20Graphs.html)
* **[Connessione di Azure Databricks a Azure Cosmos DB](https://docs.databricks.com/spark/latest/data-sources/azure/cosmosdb-connector.html)** tramite `azure-cosmosdb-spark`.  Collegato qui è anche una versione di Azure Databricks del [notebook On-Time Flight Performance](https://github.com/dennyglee/databricks/tree/master/notebooks/Users/denny%40databricks.com/azure-databricks).
* **[Architettura lambda con Azure Cosmos DB e HDInsight (Apache Spark):](https://github.com/Azure/azure-cosmosdb-spark/blob/master/samples/lambda/readme.md)** è possibile ridurre l'overhead operativo dovuto alla gestione delle pipeline di Big Data usando Cosmos DB e Spark.Lambda Architecture with Azure Cosmos DB and HDInsight (Apache Spark): You can reduce the operational overhead of maintaining big data pipelines using Cosmos DB and Spark.

## <a name="more-information"></a>Altre informazioni

Abbiamo maggiori informazioni `azure-cosmosdb-spark` nel [wiki,](https://github.com/Azure/azure-cosmosdb-spark/wiki) tra cui:

* [Azure Cosmos DB Spark Connector User Guide](https://github.com/Azure/azure-documentdb-spark/wiki/Azure-Cosmos-DB-Spark-Connector-User-Guide)
* [Esempi di aggregazioni](https://github.com/Azure/azure-documentdb-spark/wiki/Aggregations-Examples)

### <a name="configuration-and-setup"></a>Installazione e configurazione

* [Configurazione del connettore Spark](https://github.com/Azure/azure-cosmosdb-spark/wiki/Configuration-references)
* [Impostazione del connettore Spark to Cosmos DB](https://github.com/Azure/azure-documentdb-spark/wiki/Spark-to-Cosmos-DB-Connector-Setup) (in corso)
* [Configurazione di Power BI Direct Query in Azure Cosmos DB tramite Apache Spark (HDI)](https://github.com/Azure/azure-cosmosdb-spark/wiki/Configuring-Power-BI-Direct-Query-to-Azure-Cosmos-DB-via-Apache-Spark-(HDI))

### <a name="troubleshooting"></a>Risoluzione dei problemi

* [Utilizzo di Cosmos DB Aggregates](https://github.com/Azure/azure-documentdb-spark/wiki/Troubleshooting:-Using-Cosmos-DB-Aggregates)
* [Problemi noti](https://github.com/Azure/azure-cosmosdb-spark/wiki/Known-Issues)

### <a name="performance"></a>Prestazioni

* [Suggerimenti sulle prestazioni](https://github.com/Azure/azure-cosmosdb-spark/wiki/Performance-tips)
* [Esecuzioni dei test di queryQuery Test Runs](https://github.com/Azure/azure-documentdb-spark/wiki/Query-Test-Runs)
* [Scrittura di esecuzioni di testWriting Test Runs](https://github.com/Azure/azure-cosmosdb-spark/wiki/Writing-Test-Runs)

### <a name="change-feed"></a>Feed di modifiche

* [Flusso delle modifiche all'elaborazione usando Azure Cosmos DB Change Feed e Apache Spark](https://github.com/Azure/azure-cosmosdb-spark/wiki/Stream-Processing-Changes-using-Azure-Cosmos-DB-Change-Feed-and-Apache-Spark)
* [Modifica demo feed](https://github.com/Azure/azure-cosmosdb-spark/wiki/Change-Feed-demos)
* [Demo di flusso strutturato](https://github.com/Azure/azure-cosmosdb-spark/wiki/Structured-Stream-demos)

### <a name="monitoring"></a>Monitoraggio

* [Monitoraggio dei processi di Spark con Application Insights](https://github.com/Azure/azure-cosmosdb-spark/tree/2.3/samples/monitoring)

## <a name="next-steps"></a>Passaggi successivi

Se ancora non è stato fatto, scaricare il connettore Spark per Azure Cosmos DB dal repository GitHub [azure-cosmosdb-spark](https://github.com/Azure/azure-cosmosdb-spark). Esplorare le risorse aggiuntive seguenti nel repository:

* [Aggregations examples (Esempi di aggregazioni)](https://github.com/Azure/azure-cosmosdb-spark/wiki/Aggregations-Examples)
* [Script e blocchi appunti di esempio](https://github.com/Azure/azure-cosmosdb-spark/tree/master/samples)

È anche possibile vedere [Apache Spark SQL, DataFrames, and Datasets Guide](https://spark.apache.org/docs/latest/sql-programming-guide.html) (Guida ad Apache Spark SQL, frame di dati e set di dati) e l'articolo su [Apache Spark in Azure HDInsight](../hdinsight/spark/apache-spark-jupyter-spark-sql.md).
