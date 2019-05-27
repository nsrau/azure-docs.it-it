---
title: Connettere Apache Spark a Azure Cosmos DB
description: Informazioni sul connettore Spark per Azure Cosmos DB, che permette di connettere Apache Spark ad Azure Cosmos DB.
author: tknandu
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/21/2019
ms.author: ramkris
ms.openlocfilehash: bc0f2044f70c674177f9c9786f56f0441db2e282
ms.sourcegitcommit: 59fd8dc19fab17e846db5b9e262a25e1530e96f3
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/21/2019
ms.locfileid: "65978897"
---
# <a name="accelerate-big-data-analytics-by-using-the-apache-spark-to-azure-cosmos-db-connector"></a>Accelerare l'analisi di Big Data con il connettore Apache Spark per Azure Cosmos DB

È possibile eseguire [Spark](https://spark.apache.org/) processi con i dati archiviati in Azure Cosmos DB usando il connettore Spark per Cosmos DB. COSMOS può essere usato per l'elaborazione di flussi e batch e come un livello di gestione per l'accesso a bassa latenza.

È possibile usare il connettore con [Azure Databricks](https://azure.microsoft.com/services/databricks) oppure [Azure HDInsight](https://azure.microsoft.com/services/hdinsight/), che forniscono gestito i cluster Spark in Azure. Nella tabella seguente mostra Spark supportate le versioni.

| Componente | Version |
|---------|-------|
| Apache Spark | 2.1.x, 2.3.x, 2.2 e 2.4 |
| Scala | 2.11 |
| Versione del runtime di Azure Databricks | > 3.4 |

> [!WARNING]
> Questo connettore supporta l'API di base (SQL) di Azure Cosmos DB.
> Per Cosmos DB per API MongoDB, usare il [connettore Spark per MongoDB](https://docs.mongodb.com/spark-connector/master/).
> Per l'API Cassandra di Cosmos DB, usare il [connettore Spark per Cassandra](https://github.com/datastax/spark-cassandra-connector).
>

## <a name="quickstart"></a>Avvio rapido

* Seguire i passaggi descritti in [Introduzione a Java SDK](sql-api-async-java-get-started.md) per configurare un account Cosmos DB e inserire alcuni dati.
* Seguire i passaggi descritti in [Introduzione ad Azure Databricks](https://docs.azuredatabricks.net/getting-started/index.html) per configurare un'area di lavoro Azure Databricks e un cluster.
* È ora possibile creare nuovi notebook e importare la libreria del connettore Cosmos DB. Passare a [funziona con il connettore Cosmos DB](#bk_working_with_connector) per informazioni dettagliate su come configurare l'area di lavoro.
* La sezione seguente include frammenti di codice su come leggere e scrivere usando il connettore.

### <a name="reading-from-cosmos-db"></a>La lettura da Cosmos DB

Il frammento di codice seguente viene illustrato come creare un DataFrame di Spark per leggere da Cosmos DB in PySpark.

```python
# Read Configuration
readConfig = {
  "Endpoint" : "https://doctorwho.documents.azure.com:443/",
  "Masterkey" : "YOUR-KEY-HERE",
  "Database" : "DepartureDelays",
  "Collection" : "flights_pcoll",
  "query_custom" : "SELECT c.date, c.delay, c.distance, c.origin, c.destination FROM c WHERE c.origin = 'SEA'" // Optional
}

# Connect via azure-cosmosdb-spark to create Spark DataFrame
flights = spark.read.format("com.microsoft.azure.cosmosdb.spark").options(**readConfig).load()
flights.count()
```

E lo stesso frammento di codice in Scala:

```scala
// Import Necessary Libraries
import com.microsoft.azure.cosmosdb.spark.schema._
import com.microsoft.azure.cosmosdb.spark._
import com.microsoft.azure.cosmosdb.spark.config.Config

// Configure connection to your collection
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

### <a name="writing-to-cosmos-db"></a>La scrittura in Cosmos DB

Il frammento di codice seguente viene illustrato come scrivere un frame di dati in Cosmos DB in PySpark.

```python
# Write configuration
writeConfig = {
 "Endpoint" : "https://doctorwho.documents.azure.com:443/",
 "Masterkey" : "YOUR-KEY-HERE",
 "Database" : "DepartureDelays",
 "Collection" : "flights_fromsea",
 "Upsert" : "true"
}

# Write to Cosmos DB from the flights DataFrame
flights.write.format("com.microsoft.azure.cosmosdb.spark").options(**writeConfig).save()
```

E lo stesso frammento di codice in Scala:

```scala
// Configure connection to the sink collection
val writeConfig = Config(Map(
  "Endpoint" -> "https://doctorwho.documents.azure.com:443/",
  "Masterkey" -> "YOUR-KEY-HERE",
  "Database" -> "DepartureDelays",
  "Collection" -> "flights_fromsea",
  "Upsert" : "true"
))

// Upsert the dataframe to Cosmos DB
import org.apache.spark.sql.SaveMode
flights.write.mode(SaveMode.Overwrite).cosmosDB(writeConfig)
```

Per ulteriori frammenti di codice ed esempi end to end, vedere [Jupyter](https://github.com/Azure/azure-cosmosdb-spark/tree/master/samples/notebooks).

## <a name="bk_working_with_connector"></a> Uso del connettore

È possibile creare il connettore dall'origine in Github o scaricare i file uber JAR da Maven i collegamenti seguenti.

| Spark | Scala | Versione più recente |
|---|---|---|
| 2.4.0 | 2.11 | [azure-cosmosdb-spark_2.4.0_2.11_1.3.5](https://search.maven.org/artifact/com.microsoft.azure/azure-cosmosdb-spark_2.4.0_2.11/1.3.5/jar)
| 2.3.0 | 2.11 | [azure-cosmosdb-spark_2.3.0_2.11_1.3.3](https://search.maven.org/artifact/com.microsoft.azure/azure-cosmosdb-spark_2.3.0_2.11/1.3.3/jar)
| 2.2.0 | 2.11 | [azure-cosmosdb-spark_2.2.0_2.11_1.1.1](https://search.maven.org/#artifactdetails%7Ccom.microsoft.azure%7Cazure-cosmosdb-spark_2.2.0_2.11%7C1.1.1%7Cjar)
| 2.1.0 | 2.11 | [azure-cosmosdb-spark_2.1.0_2.11_1.2.2](https://search.maven.org/artifact/com.microsoft.azure/azure-cosmosdb-spark_2.1.0_2.11/1.2.2/jar)

### <a name="using-databricks-notebooks"></a>Uso di notebook di Databricks

Creare una libreria mediante l'area di lavoro di Databricks, seguendo le istruzioni nella Guida di Databricks Azure > [usare il connettore Spark per Azure Cosmos DB](https://docs.azuredatabricks.net/spark/latest/data-sources/azure/cosmosdb-connector.html)

> [!NOTE]
> Si noti che il **usare il connettore Spark per Azure Cosmos DB** pagina attualmente non è aggiornata. Invece di scaricare il file con estensione jar separato sei in sei diverse librerie, è possibile scaricare il file uber jar da maven in https://search.maven.org/artifact/com.microsoft.azure/azure-cosmosdb-spark_2.4.0_2.11/1.3.5/jar) e installare questa libreria/jar uno.
> 

### <a name="using-spark-cli"></a>Uso di spark-cli

Per lavorare con il connettore tramite la cli di spark (vale a dire `spark-shell`, `pyspark`, `spark-submit`), è possibile usare i `--packages` parametro con il connettore [coordinate maven](https://mvnrepository.com/artifact/com.microsoft.azure/azure-cosmosdb-spark_2.4.0_2.11).

```sh
spark-shell --master yarn --packages "com.microsoft.azure:azure-cosmosdb-spark_2.4.0_2.11:1.3.5"

```

### <a name="using-jupyter-notebooks"></a>Uso di Jupyter notebook

Se si usa i notebook di Jupyter all'interno di HDInsight, è possibile usare magic spark `%%configure` cella per specificare le coordinate maven del connettore.

```python
{ "name":"Spark-to-Cosmos_DB_Connector",
  "conf": {
    "spark.jars.packages": "com.microsoft.azure:azure-cosmosdb-spark_2.4.0_2.11:1.3.5",
    "spark.jars.excludes": "org.scala-lang:scala-reflect"
   }
   ...
}
```

> Si noti, l'inclusione del `spark.jars.excludes` è specifico per rimuovere i potenziali conflitti tra il connettore, Apache Spark e Livy.

### <a name="build-the-connector"></a>Creare il connettore

Attualmente, questo progetto del connettore Usa `maven` in modo da per compilare senza dipendenze, è possibile eseguire:

```sh
mvn clean package
```

## <a name="working-with-our-samples"></a>Uso di esempi

Il [repository GitHub di Cosmos DB Spark](https://github.com/Azure/azure-cosmosdb-spark) ha i seguenti esempi di notebook e gli script che è possibile provare.

* **Le prestazioni dei voli puntuali con Spark e Cosmos DB (Seattle)** [ipynb](https://github.com/Azure/azure-cosmosdb-spark/blob/master/samples/notebooks/On-Time%20Flight%20Performance%20with%20Spark%20and%20Cosmos%20DB%20-%20Seattle.ipynb) | [html](https://github.com/Azure/azure-cosmosdb-spark/blob/master/samples/notebooks/On-Time%20Flight%20Performance%20with%20Spark%20and%20Cosmos%20DB%20-%20Seattle.html): Connettere Spark a Cosmos DB usando il servizio di notebook Jupyter di HDInsight per presentare Spark SQL, GraphFrames e stima i ritardi dei voli usando le pipeline di Machine Learning.
* **[La connessione di Spark con Cosmos DB Change feed](https://github.com/Azure/azure-cosmosdb-spark/blob/master/samples/notebooks/Spark%2Band%2BCosmos%2BDB%2BChange%2BFeed.ipynb)**: Una presentazione rapida su come connettere Spark a Cosmos DB Change Feed.
* **L'origine con Apache Spark e Azure Cosmos DB Change Feed di Twitter**: [ipynb](https://github.com/Azure/azure-cosmosdb-spark/blob/master/samples/notebooks/Twitter%20with%20Spark%20and%20Azure%20Cosmos%20DB%20Change%20Feed.ipynb) | [html](https://github.com/Azure/azure-cosmosdb-spark/blob/master/samples/notebooks/Twitter%20with%20Spark%20and%20Azure%20Cosmos%20DB%20Change%20Feed.html)
* **Uso di Apache Spark per Cosmos DB grafici di query**: [ipynb](https://github.com/Azure/azure-cosmosdb-spark/blob/master/samples/notebooks/Using%20Apache%20Spark%20to%20query%20Cosmos%20DB%20Graphs.ipynb) | [html](https://github.com/Azure/azure-cosmosdb-spark/blob/master/samples/notebooks/Using%20Apache%20Spark%20to%20query%20Cosmos%20DB%20Graphs.html)
* **[La connessione di Azure Databricks in Azure Cosmos DB](https://docs.databricks.com/spark/latest/data-sources/azure/cosmosdb-connector.html)**  usando `azure-cosmosdb-spark`.  Collegato qui è anche una versione di Azure Databricks del [notebook di prestazioni dei voli puntuali](https://github.com/dennyglee/databricks/tree/master/notebooks/Users/denny%40databricks.com/azure-databricks).
* **[Architettura lambda con Azure Cosmos DB e HDInsight (Apache Spark)](https://github.com/Azure/azure-cosmosdb-spark/blob/master/samples/lambda/readme.md)**: È possibile ridurre l'overhead operativo di gestione di pipeline di big data tramite Spark e Cosmos DB.

## <a name="more-information"></a>Altre informazioni

Sono disponibili altre informazioni `azure-cosmosdb-spark` [wiki](https://github.com/Azure/azure-cosmosdb-spark/wiki) tra cui:

* [Guida utente connettore Spark per Azure Cosmos DB](https://github.com/Azure/azure-documentdb-spark/wiki/Azure-Cosmos-DB-Spark-Connector-User-Guide)
* [Esempi di aggregazioni](https://github.com/Azure/azure-documentdb-spark/wiki/Aggregations-Examples)

### <a name="configuration-and-setup"></a>Installazione e configurazione

* [Configurazione del connettore Spark](https://github.com/Azure/azure-cosmosdb-spark/wiki/Configuration-references)
* [Spark per Cosmos DB Connector Setup](https://github.com/Azure/azure-documentdb-spark/wiki/Spark-to-Cosmos-DB-Connector-Setup) (In corso)
* [Configurazione di Power BI Direct Query ad Azure Cosmos DB tramite Apache Spark (HDI)](https://github.com/Azure/azure-cosmosdb-spark/wiki/Configuring-Power-BI-Direct-Query-to-Azure-Cosmos-DB-via-Apache-Spark-(HDI))

### <a name="troubleshooting"></a>risoluzione dei problemi

* [Usando funzioni di aggregazione di Cosmos DB](https://github.com/Azure/azure-documentdb-spark/wiki/Troubleshooting:-Using-Cosmos-DB-Aggregates)
* [Problemi noti](https://github.com/Azure/azure-cosmosdb-spark/wiki/Known-Issues)

### <a name="performance"></a>Prestazioni

* [Suggerimenti per incrementare le prestazioni](https://github.com/Azure/azure-cosmosdb-spark/wiki/Performance-tips)
* [Esecuzioni dei Test query](https://github.com/Azure/azure-documentdb-spark/wiki/Query-Test-Runs)
* [La scrittura le esecuzioni dei Test](https://github.com/Azure/azure-cosmosdb-spark/wiki/Writing-Test-Runs)

### <a name="change-feed"></a>Feed di modifiche

* [Stream Processing modifiche usando Apache Spark e Azure Cosmos DB Change Feed](https://github.com/Azure/azure-cosmosdb-spark/wiki/Stream-Processing-Changes-using-Azure-Cosmos-DB-Change-Feed-and-Apache-Spark)
* [Demo di Feed di modifiche](https://github.com/Azure/azure-cosmosdb-spark/wiki/Change-Feed-demos)
* [Demo di Stream strutturato](https://github.com/Azure/azure-cosmosdb-spark/wiki/Structured-Stream-demos)

### <a name="monitoring"></a>Monitoraggio

* [Monitoraggio dei processi di Spark con application insights](https://github.com/Azure/azure-cosmosdb-spark/tree/2.3/samples/monitoring)

## <a name="next-steps"></a>Passaggi successivi

Se ancora non è stato fatto, scaricare il connettore Spark per Azure Cosmos DB dal repository GitHub [azure-cosmosdb-spark](https://github.com/Azure/azure-cosmosdb-spark). Esplorare le risorse aggiuntive seguenti nel repository:

* [Aggregations examples (Esempi di aggregazioni)](https://github.com/Azure/azure-cosmosdb-spark/wiki/Aggregations-Examples)
* [Sample scripts and notebooks (Notebook e script di esempio)](https://github.com/Azure/azure-cosmosdb-spark/tree/master/samples)

È anche possibile vedere [Apache Spark SQL, DataFrames, and Datasets Guide](https://spark.apache.org/docs/latest/sql-programming-guide.html) (Guida ad Apache Spark SQL, frame di dati e set di dati) e l'articolo su [Apache Spark in Azure HDInsight](../hdinsight/spark/apache-spark-jupyter-spark-sql.md).
