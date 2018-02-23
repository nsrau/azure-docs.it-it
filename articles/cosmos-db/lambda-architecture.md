---
title: Architettura lambda con Azure Cosmos DB e HDInsight (Apache Spark) | Microsoft Docs
description: Questo articolo descrive come implementare un'architettura lambda usando Azure Cosmos DB, HDInsight e Spark
keywords: architettura-lambda
services: cosmos-db
documentationcenter: 
author: dennyglee
manager: jhubbard
editor: 
ms.assetid: 273aeae9-e31c-4a43-b216-5751c46f212e
ms.service: cosmos-db
ms.workload: data-services
ms.topic: article
ms.date: 01/19/2018
ms.author: denlee
ms.openlocfilehash: f88f3fb05495b0f3330d5a4cde7718fe89b2f694
ms.sourcegitcommit: 1fbaa2ccda2fb826c74755d42a31835d9d30e05f
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/22/2018
---
# <a name="azure-cosmos-db-implement-a-lambda-architecture-on-the-azure-platform"></a>Azure Cosmos DB: Implementare un'architettura lambda nella piattaforma Azure 

Le architetture lambda consentono di elaborare in modo efficiente i set di dati di grandi dimensioni. Le architetture lambda usano l'elaborazione batch, l'elaborazione di flussi e un livello di gestione per ridurre al minimo la latenza di query su Big Data. 

Per implementare un'architettura lambda in Azure, è possibile combinare le tecnologie seguenti per accelerare le analisi di Big Data in tempo reale:
* [Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db/), il primo servizio di database multimodello distribuito a livello globale del settore 
* [Apache Spark per Azure HDInsight](https://azure.microsoft.com/services/hdinsight/apache-spark/), un framework di elaborazione che esegue applicazioni di analisi dei dati su vasta scala
* [Feed di modifiche](change-feed.md) di Azure Cosmos DB, che trasmette i nuovi dati al livello batch per consentire l'elaborazione tramite HDInsight
* [Connettore Spark per Azure Cosmos DB](spark-connector.md)

Questo articolo descrive le nozioni di base di un'architettura lambda basata sulla progettazione multilivello originale e i vantaggi di un'architettura lambda "riprogettata" che semplifica le operazioni.  

Per una panoramica dell'architettura lambda e delle risorse disponibili nell'esempio di architettura lambda, guardare il video seguente:

> [!VIDEO https:///channel9.msdn.com/Events/Connect/2017/T135/player]
>

## <a name="what-is-a-lambda-architecture"></a>Cos'è un'architettura lambda?
Un'architettura lambda è un'architettura di elaborazione dei dati generica, scalabile e con tolleranza di errore per gli scenari batch e con bassa latenza descritti da [Nathan Marz](https://twitter.com/nathanmarz).

![Diagramma che mostra un'architettura lambda](./media/lambda-architecture/lambda-architecture-intro.png)

Fonte: http://lambda-architecture.net/

I principi di base di un'architettura lambda sono descritti nel diagramma precedente, in base a quanto illustrato in [https://lambda-architecture.net](http://lambda-architecture.net/).

 1. Viene eseguito il push di tutti i **dati** in *entrambi* i livelli, *batch* e di *elaborazione rapida*.
 2. Il **livello batch** ha un set di dati master (set di dati non elaborati non modificabile, che consente solo l'accodamento) e pre-calcola le viste batch.
 3. Il **livello di gestione** ha viste batch per query veloci. 
 4. Il **livello di elaborazione rapida** compensa il tempo di elaborazione (nel livello di gestione) e usa solo i dati recenti.
 5. È possibile rispondere a tutte le query unendo i risultati delle viste batch e delle viste in tempo reale o effettuando il ping singolarmente.

Dopo aver letto le informazioni, si sarà in grado di implementare questa architettura usando solo gli elementi seguenti:

* Raccolta/e di Azure Cosmos DB
* Cluster HDInsight (Apache Spark 2.1)
* Connettore Spark [1.0](https://github.com/Azure/azure-cosmosdb-spark/tree/master/releases/azure-cosmosdb-spark_2.1.0_2.11-1.0.0)

## <a name="speed-layer"></a>Livello di elaborazione rapida

Dal punto di vista operativo, la gestione di due flussi di dati assicurando lo stato corretto dei dati può essere un'attività complessa. Per semplificare le operazioni, usare il [supporto per i feed di modifiche di Azure Cosmos DB](change-feed.md) per mantenere lo stato per il *livello batch* visualizzando nel contempo il log delle modifiche di Azure Cosmos DB tramite l'*API del feed di modifiche* per il *livello di elaborazione rapida*.  
![Diagramma che mette in evidenza i nuovi dati, il livello di elaborazione rapida e la parte del set di dati master dell'architettura lambda](./media/lambda-architecture/lambda-architecture-change-feed.png)

Aspetti importanti dei livelli:

 1. Il push di tutti i **dati** viene eseguito *solo* in Azure Cosmos DB, per evitare problemi di multicasting.
 2. Il **livello batch** ha un set di dati master (set di dati non elaborati non modificabile, che consente solo l'accodamento) e pre-calcola le viste batch.
 3. Il **livello di gestione** è descritto nella sezione successiva.
 4. Il **livello di elaborazione rapida** usa HDInsight (Apache Spark) per leggere il feed di modifiche di Azure Cosmos DB. Ciò consente di rendere persistenti i dati, oltre che di eseguire query ed elaborarli contemporaneamente.
 5. È possibile rispondere a tutte le query unendo i risultati delle viste batch e delle viste in tempo reale o effettuando il ping singolarmente.
 
### <a name="code-example-spark-structured-streaming-to-an-azure-cosmos-db-change-feed"></a>Esempio di codice: streaming strutturato Spark verso un feed di modifiche di Azure Cosmos DB
Per eseguire un rapido prototipo del feed di modifiche di Azure Cosmos DB come parte del **livello di elaborazione rapida**, è possibile testarlo usando i dati di Twitter come parte dell'esempio [Stream Processing Changes using Azure Cosmos DB Change Feed and Apache Spark](https://github.com/Azure/azure-cosmosdb-spark/wiki/Stream-Processing-Changes-using-Azure-Cosmos-DB-Change-Feed-and-Apache-Spark) (Modifiche all'elaborazione di flussi con il feed di modifiche di Azure Cosmos DB e Apache Spark). Per creare l'output di Twitter, vedere l'esempio di codice in [Stream feed from Twitter to Cosmos DB](https://github.com/tknandu/TwitterCosmosDBFeed) (Trasmettere un feed da Twitter a Cosmos DB). Nell'esempio precedente si caricano i dati di Twitter in Azure Cosmos DB ed è quindi possibile configurare il cluster HDInsight (Apache Spark) per connettersi al feed di modifiche. Per altre informazioni su come eseguire questa configurazione, vedere [Apache Spark to Azure Cosmos DB Connector Setup](https://github.com/Azure/azure-cosmosdb-spark/wiki/Spark-to-Cosmos-DB-Connector-Setup) (Configurazione del connettore Apache Spark per Azure Cosmos DB).  

Il frammento di codice seguente mostra come configurare `spark-shell` per eseguire un processo di streaming strutturato per connettersi a un feed di modifiche di Azure Cosmos DB, che esamina il flusso di dati di Twitter in tempo reale, per eseguire un conteggio degli intervalli.

```
// Import Libraries
import com.microsoft.azure.cosmosdb.spark._
import com.microsoft.azure.cosmosdb.spark.schema._
import com.microsoft.azure.cosmosdb.spark.config.Config
import org.codehaus.jackson.map.ObjectMapper
import com.microsoft.azure.cosmosdb.spark.streaming._
import java.time._


// Configure connection to Azure Cosmos DB Change Feed
val sourceConfigMap = Map(
"Endpoint" -> "[COSMOSDB ENDPOINT]",
"Masterkey" -> "[MASTER KEY]",
"Database" -> "[DATABASE]",
"Collection" -> "[COLLECTION]",
"ConnectionMode" -> "Gateway",
"ChangeFeedCheckpointLocation" -> "checkpointlocation",
"changefeedqueryname" -> "Streaming Query from Cosmos DB Change Feed Interval Count")

// Start reading change feed as a stream
var streamData = spark.readStream.format(classOf[CosmosDBSourceProvider].getName).options(sourceConfigMap).load()

// Start streaming query to console sink
val query = streamData.withColumn("countcol", streamData.col("id").substr(0, 0)).groupBy("countcol").count().writeStream.outputMode("complete").format("console").start()
```

Per esempi di codice completi, vedere gli esempi in [azure-cosmosdb-spark/lambda/samples](https://github.com/Azure/azure-cosmosdb-spark/tree/master/samples/lambda), tra cui:
* [Streaming Query from Cosmos DB Change Feed.scala](https://github.com/Azure/azure-cosmosdb-spark/blob/master/samples/lambda/Streaming%20Query%20from%20Cosmos%20DB%20Change%20Feed.scala) (Query di streaming dal feed di modifiche di Cosmos DB - Scala)
* [Streaming Tags Query from Cosmos DB Change Feed.scala](https://github.com/Azure/azure-cosmosdb-spark/blob/master/samples/lambda/Streaming%20Tags%20Query%20from%20Cosmos%20DB%20Change%20Feed%20.scala) (Query di streaming su tag dal feed di modifiche di Cosmos DB - Scala)

L'output è una console `spark-shell`, che esegue continuamente un processo di streaming strutturato che effettua un conteggio degli intervalli nei dati di Twitter dal feed di modifiche di Azure Cosmos DB. L'immagine seguente mostra l'output del processo di streaming e il conteggio degli intervalli.

![Output di streaming che mostra il numero di intervalli nei dati di Twitter dal feed di modifiche di Azure Cosmos DB](./media/lambda-architecture/lambda-architecture-speed-layer-twitter-count.png) 

Per altre informazioni sul feed di modifiche di Azure Cosmos DB, vedere:

* [Uso del supporto del feed delle modifiche in Azure Cosmos DB](change-feed.md)
* [Introducing the Azure CosmosDB Change Feed Processor Library](https://azure.microsoft.com/blog/introducing-the-azure-cosmosdb-change-feed-processor-library/) (Presentazione della libreria del processore dei feed di modifiche di Azure CosmosDB)
* [Stream Processing Changes: Azure CosmosDB change feed + Apache Spark](https://azure.microsoft.com/blog/stream-processing-changes-azure-cosmosdb-change-feed-apache-spark/) (Modifiche all'elaborazione di flussi: feed di modifiche di Azure Cosmos DB + Apache Spark)

## <a name="batch-and-serving-layers"></a>Livelli batch e di gestione
Poiché i nuovi dati vengono caricati in Azure Cosmos DB (dove il feed di modifiche viene usato per il livello di elaborazione rapida), è qui che si trova il **set di dati master** (un set di dati non elaborati non modificabile, che consente solo l'accodamento). Da questo punto in avanti, usare HDInsight (Apache Spark) per eseguire le funzioni di pre-calcolo dal **livello batch** al **livello di gestione**, come illustrato nell'immagine seguente:

![Diagramma che mette in evidenza il livello batch e il livello di gestione dell'architettura lambda](./media/lambda-architecture/lambda-architecture-batch-serve.png)

Aspetti importanti dei livelli:

 1. Il push di tutti i **dati** viene eseguito solo in Azure Cosmos DB, per evitare problemi di multicasting.
 2. Il **livello batch** ha un set di dati master (set di dati non elaborati non modificabile, che consente solo l'accodamento) archiviato in Azure Cosmos DB. Usando HDInsight Spark, è possibile pre-calcolare le aggregazioni da archiviare nelle viste batch calcolate.
 3. Il **livello di gestione** è un database di Azure Cosmos DB con raccolte per il set di dati master e la vista batch calcolata.
 4. Il **livello di elaborazione rapida** viene illustrato più avanti in questo articolo.
 5. È possibile rispondere a tutte le query unendo i risultati delle viste batch e delle viste in tempo reale o effettuando il ping singolarmente.

### <a name="code-example-pre-computing-batch-views"></a>Esempio di codice: pre-calcolo delle viste batch
Per illustrare come eseguire le viste pre-calcolate nel **set di dati master** da Apache Spark ad Azure Cosmos DB, usare i frammenti di codice seguenti disponibili nei notebook [Lambda Architecture Rearchitected - Batch Layer](https://github.com/Azure/azure-cosmosdb-spark/blob/master/samples/lambda/Lambda%20Architecture%20Re-architected%20-%20Batch%20Layer.ipynb) (Architettura lambda riprogettata - Livello batch) e [Lambda Architecture Rearchitected - Batch to Serving Layer](https://github.com/Azure/azure-cosmosdb-spark/blob/master/samples/lambda/Lambda%20Architecture%20Re-architected%20-%20Batch%20to%20Serving%20Layer.ipynb) (Architettura lambda riprogettata - Da livello batch a livello di gestione). In questo scenario usare i dati di Twitter archiviati in Azure Cosmos DB.

Iniziare creando la connessione di configurazione ai dati di Twitter in Azure Cosmos DB usando il codice PySpark riportato di seguito.

```
# Configuration to connect to Azure Cosmos DB
tweetsConfig = {
  "Endpoint" : "[Endpoint URL]",
  "Masterkey" : "[Master Key]",
  "Database" : "[Database]",
  "Collection" : "[Collection]", 
  "preferredRegions" : "[Preferred Regions]",
  "SamplingRatio" : "1.0",
  "schema_samplesize" : "200000",
  "query_custom" : "[Cosmos DB SQL Query]"
}

# Create DataFrame
tweets = spark.read.format("com.microsoft.azure.cosmosdb.spark").options(**tweetsConfig).load()

# Create Temp View (to run Spark SQL statements)
tweets.createOrReplaceTempView("tweets")
```

Eseguire quindi l'istruzione Spark SQL seguente per determinare i primi 10 hashtag del set di tweet. Per questa query Spark SQL l'esecuzione avviene in un notebook Jupyter senza il grafico a barre dell'output disponibile direttamente dopo il frammento di codice.

```
%%sql
select hashtags.text, count(distinct id) as tweets
from (
  select 
    explode(hashtags) as hashtags,
    id
  from tweets
) a
group by hashtags.text
order by tweets desc
limit 10
```

![Grafico che mostra il numero di tweet per hashtag](./media/lambda-architecture/lambda-architecture-batch-hashtags-bar-chart.png)

Ora che la query è stata creata, salvarla di nuovo in una raccolta usando il connettore Spark per salvare i dati di output in una raccolta diversa.  In questo esempio usare Scala per presentare la connessione. Analogamente all'esempio precedente, creare la connessione di configurazione per salvare il frame di dati di Apache Spark in una raccolta di Azure Cosmos DB diversa.

```
val writeConfigMap = Map(
    "Endpoint" -> "[Endpoint URL]",
    "Masterkey" -> "[Master Key]",
    "Database" -> "[Database]",
    "Collection" -> "[New Collection]", 
    "preferredRegions" -> "[Preferred Regions]",
    "SamplingRatio" -> "1.0",
    "schema_samplesize" -> "200000"
)

// Configuration to write
val writeConfig = Config(writeConfigMap)

```

Dopo aver specificato `SaveMode` (che indica se usare il metodo `Overwrite` o `Append` per i documenti), creare un frame di dati `tweets_bytags` simile alla query Spark SQL nell'esempio precedente.  Dopo aver creato il frame di dati `tweets_bytags`, è possibile salvarlo con il metodo `write` usando l'oggetto `writeConfig` specificato in precedenza.

```
// Import SaveMode so you can Overwrite, Append, ErrorIfExists, Ignore
import org.apache.spark.sql.{Row, SaveMode, SparkSession}

// Create new DataFrame of tweets tags
val tweets_bytags = spark.sql("select hashtags.text as hashtags, count(distinct id) as tweets from ( select explode(hashtags) as hashtags, id from tweets ) a group by hashtags.text order by tweets desc")

// Save to Cosmos DB (using Append in this case)
tweets_bytags.write.mode(SaveMode.Overwrite).cosmosDB(writeConfig)
```

L'ultima istruzione ha ora salvato il frame di dati Spark in una nuova raccolta di Azure Cosmos DB. Dal punto di vista di un'architettura lambda, si tratta della **vista batch** all'interno del **livello di gestione**.
 
#### <a name="resources"></a>Risorse

Per esempi di codice completi, vedere gli esempi in [azure-cosmosdb-spark/lambda/samples](vhttps://github.com/Azure/azure-cosmosdb-spark/tree/master/samples/lambda), tra cui:
* Lambda Architecture Rearchitected - Batch Layer (Architettura lambda riprogettata - Livello batch) [HTML](https://github.com/Azure/azure-cosmosdb-spark/blob/master/samples/lambda/Lambda%20Architecture%20Re-architected%20-%20Batch%20Layer.html) | [ipynb](https://github.com/Azure/azure-cosmosdb-spark/blob/master/samples/lambda/Lambda%20Architecture%20Re-architected%20-%20Batch%20Layer.ipynb)
* Lambda Architecture Rearchitected - Batch to Serving Layer (Architettura lambda riprogettata - Da livello batch a livello di gestione)[HTML](https://github.com/Azure/azure-cosmosdb-spark/blob/master/samples/lambda/Lambda%20Architecture%20Re-architected%20-%20Batch%20to%20Serving%20Layer.html) | [ipynb](https://github.com/Azure/azure-cosmosdb-spark/blob/master/samples/lambda/Lambda%20Architecture%20Re-architected%20-%20Batch%20to%20Serving%20Layer.ipynb)

## <a name="speed-layer"></a>Livello di elaborazione rapida
Come illustrato in precedenza, l'uso della libreria di feed di modifiche di Azure Cosmos DB consente di semplificare le operazioni tra i livelli batch e di elaborazione rapida. In questa architettura usare Apache Spark (tramite HDInsight) per eseguire le query di *streaming strutturato* sui dati. È anche possibile salvare temporaneamente i risultati delle query di streaming strutturato in modo che altri sistemi possano accedere ai dati.

![Diagramma che mette in evidenza il livello di elaborazione rapida dell'architettura lambda](./media/lambda-architecture/lambda-architecture-speed.png)

A tale scopo, creare una raccolta di Azure Cosmos DB separata per salvare i risultati delle query di streaming strutturato.  In questo modo, altri sistemi oltre ad Apache Spark potranno accedere alle informazioni. Analogamente a quanto avviene con la funzionalità di durata (TTL) di Cosmos DB, è possibile configurare i documenti in modo che vengano eliminati automaticamente dopo un determinato periodo di tempo.  Per altre informazioni sulla funzionalità di durata (TTL) di Azure Cosmos DB, vedere [Impostare automaticamente come scaduti i dati nelle raccolte di Azure Cosmos DB usando la durata (TTL)](time-to-live.md)

```
// Import Libraries
import com.microsoft.azure.cosmosdb.spark._
import com.microsoft.azure.cosmosdb.spark.schema._
import com.microsoft.azure.cosmosdb.spark.config.Config
import org.codehaus.jackson.map.ObjectMapper
import com.microsoft.azure.cosmosdb.spark.streaming._
import java.time._


// Configure connection to Azure Cosmos DB Change Feed
val sourceCollectionName = "[SOURCE COLLECTION NAME]"
val sinkCollectionName = "[SINK COLLECTION NAME]"

val configMap = Map(
"Endpoint" -> "[COSMOSDB ENDPOINT]",
"Masterkey" -> "[COSMOSDB MASTER KEY]",
"Database" -> "[DATABASE NAME]",
"Collection" -> sourceCollectionName,
"ChangeFeedCheckpointLocation" -> "changefeedcheckpointlocation")

val sourceConfigMap = configMap.+(("changefeedqueryname", "Structured Stream replication streaming test"))

// Start to read the stream
var streamData = spark.readStream.format(classOf[CosmosDBSourceProvider].getName).options(sourceConfigMap).load()
val sinkConfigMap = configMap.-("collection").+(("collection", sinkCollectionName))

// Start the stream writer to new collection
val streamingQueryWriter = streamData.writeStream.format(classOf[CosmosDBSinkProvider].getName).outputMode("append").options(sinkConfigMap).option("checkpointLocation", "streamingcheckpointlocation")
var streamingQuery = streamingQueryWriter.start()

```

## <a name="lambda-architecture-rearchitected"></a>Architettura lambda riprogettata
Come indicato nelle sezioni precedenti, è possibile semplificare l'architettura lambda originale usando i componenti seguenti:
* Azure Cosmos DB
* Libreria di feed di modifiche di Azure Cosmos DB per evitare il multicast dei dati tra il livello batch e il livello di elaborazione rapida
* Apache Spark in HDInsight
* Connettore Spark per Azure Cosmos DB

![Diagramma che illustra la riprogettazione dell'architettura lambda usando Azure Cosmos DB, Spark e l'API del feed di modifiche di Azure Cosmos DB](./media/lambda-architecture/lambda-architecture-re-architected.png)

Con questa struttura, sono necessari solo due servizi gestiti, Azure Cosmos DB e HDInsight. Questi servizi, insieme, si occupano dei livelli batch, di gestione e di elaborazione rapida dell'architettura lambda. In questo modo, si semplificano non solo le operazioni, ma anche il flusso di dati. 
 1. Viene eseguito il push di tutti i dati in Azure Cosmos DB per l'elaborazione.
 2. Il livello batch ha un set di dati master (set di dati non elaborati non modificabile, che consente solo l'accodamento) e pre-calcola le viste batch.
 3. Il livello di gestione ha viste batch dei dati per query veloci.
 4. Il livello di elaborazione rapida compensa il tempo di elaborazione (nel livello di gestione) e usa solo i dati recenti.
 5. È possibile rispondere a tutte le query unendo i risultati delle viste batch e delle viste in tempo reale.

### <a name="resources"></a>Risorse

 * **Nuovi dati**: il [feed di streaming da Twitter a CosmosDB](https://github.com/tknandu/TwitterCosmosDBFeed), ovvero il meccanismo di push dei nuovi dati in Azure Cosmos DB.
 * **Livello batch:** il livello batch è composto dal *set di dati master* (un set di dati non elaborati non modificabile, che consente solo l'accodamento) e la possibilità di pre-calcolare le viste batch dei dati di cui viene eseguito il push nel **livello di gestione**.
    * Il notebook **Lambda Architecture Rearchitected - Batch Layer** (Architettura lambda riprogettata - Livello batch) [ipynb](https://github.com/Azure/azure-cosmosdb-spark/blob/master/samples/lambda/Lambda%20Architecture%20Re-architected%20-%20Batch%20Layer.ipynb) | [html](https://github.com/Azure/azure-cosmosdb-spark/blob/master/samples/lambda/Lambda%20Architecture%20Re-architected%20-%20Batch%20Layer.html) esegue query sul *set di dati master* delle viste batch.
 * **Livello di gestione:** il **livello di gestione** è costituito dai dati pre-calcolati risultanti nelle viste batch (ad esempio aggregazioni, filtri dei dati specifici e così via) per le query rapide.
    * Il notebook **Lambda Architecture Rearchitected - Batch to Serving Layer** (Architettura lambda riprogettata - Da livello batch a livello di gestione) [ipynb](https://github.com/Azure/azure-cosmosdb-spark/blob/master/samples/lambda/Lambda%20Architecture%20Re-architected%20-%20Batch%20to%20Serving%20Layer.ipynb) | [html](https://github.com/Azure/azure-cosmosdb-spark/blob/master/samples/lambda/Lambda%20Architecture%20Re-architected%20-%20Batch%20to%20Serving%20Layer.html) esegue il push dei dati batch nel livello di gestione. Ciò significa che Spark esegue una query su una raccolta batch di tweet, la elabora e la archivia in un'altra raccolta (un batch calcolato).
* **Livello di elaborazione rapida:** il **livello di elaborazione rapida** è costituito da Spark che usa il feed di modifiche di Azure Cosmos DB per leggere e agire immediatamente. I dati possono anche essere salvati nella *vista in tempo reale calcolata*, in modo che altri sistemi possano eseguire query sui dati in tempo reale elaborati invece di dover eseguire essi stessi una query in tempo reale.
    * Lo script Scala [Streaming Query from Cosmos DB Change Feed](https://github.com/Azure/azure-cosmosdb-spark/blob/master/samples/lambda/Streaming%20Query%20from%20Cosmos%20DB%20Change%20Feed.scala) (Query di streaming dal feed di modifiche di Cosmos DB) esegue una query di streaming dal feed di modifiche di Azure Cosmos DB per calcolare un numero di intervalli da spark-shell.
    * Lo script Scala [Streaming Tags Query from Cosmos DB Change Feed](https://github.com/Azure/azure-cosmosdb-spark/blob/master/samples/lambda/Streaming%20Tags%20Query%20from%20Cosmos%20DB%20Change%20Feed%20.scala) (Query di streaming su tag dal feed di modifiche di Cosmos DB - Scala) esegue una query di streaming dal feed di modifiche di Azure Cosmos DB per calcolare un numero di intervalli di tag da spark-shell.
  
## <a name="next-steps"></a>Passaggi successivi
Se ancora non lo si è fatto, scaricare il connettore Spark per Azure Cosmos DB dal repository GitHub [azure-cosmosdb-spark](https://github.com/Azure/azure-cosmosdb-spark) ed esplorare le risorse aggiuntive nel repository:
* [Lambda architecture (Architettura lambda)](https://github.com/Azure/azure-cosmosdb-spark/tree/master/samples/lambda)
* [Distributed aggregations examples (Esempi di aggregazioni distribuite)](https://github.com/Azure/azure-documentdb-spark/wiki/Aggregations-Examples)
* [Sample scripts and notebooks (Notebook e script di esempio)](https://github.com/Azure/azure-cosmosdb-spark/tree/master/samples)
* [Structured streaming demos (Demo di flussi strutturati)](https://github.com/Azure/azure-cosmosdb-spark/wiki/Structured-Stream-demos)
* [Change feed demos (Demo di feed di modifiche)](https://github.com/Azure/azure-cosmosdb-spark/wiki/Change-Feed-demos)
* [Stream processing changes using Azure Cosmos DB Change Feed and Apache Spark (Modifiche all'elaborazione di flussi con il feed di modifiche di Azure Cosmos DB e Apache Spark)](https://github.com/Azure/azure-cosmosdb-spark/wiki/Stream-Processing-Changes-using-Azure-Cosmos-DB-Change-Feed-and-Apache-Spark)

È possibile anche esaminare la [guida ad Apache Spark SQL, DataFrame e set di dati](http://spark.apache.org/docs/latest/sql-programming-guide.html) e l'articolo su [Apache Spark in Azure HDInsight](../hdinsight/spark/apache-spark-jupyter-spark-sql.md).
