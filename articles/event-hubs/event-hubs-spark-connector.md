---
title: Integrazione di Apache Spark con Hub eventi di Azure | Microsoft Docs
description: Integrazione con Apache Spark per abilitare lo streaming strutturato con Hub eventi
services: event-hubs
documentationcenter: na
author: ShubhaVijayasarathy
manager: timlt
editor: ''
ms.service: event-hubs
ms.devlang: java
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/05/2018
ms.author: shvija
ms.openlocfilehash: 2dcf390b80c119ab21948b982c0812395e45bc01
ms.sourcegitcommit: a0be2dc237d30b7f79914e8adfb85299571374ec
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 03/12/2018
---
# <a name="integrating-apache-spark-with-azure-event-hubs"></a>Integrazione di Apache Spark con Hub eventi di Azure

Hub eventi di Azure si integra perfettamente con [Apache Spark](https://spark.apache.org/) per semplificare la creazione di applicazioni di streaming distribuite _end-to-end_. Questa integrazione supporta [Spark Core](http://spark.apache.org/docs/latest/rdd-programming-guide.html), [Spark Streaming](http://spark.apache.org/docs/latest/streaming-programming-guide.html) e lo [streaming strutturato](https://spark.apache.org/docs/latest/structured-streaming-programming-guide.html). Il connettore Hub eventi per Apache Spark è disponibile in [GitHub](https://github.com/Azure/azure-event-hubs-spark). La libreria è disponibile anche per l'uso in progetti Maven dal [repository centrale di Maven](http://search.maven.org/#artifactdetails%7Ccom.microsoft.azure%7Cazure-eventhubs-spark_2.11%7C2.1.6%7C)

Questo articolo illustra come creare un'applicazione continua in [Azure Databricks](https://azure.microsoft.com/services/databricks/). Sebbene in questo articolo venga usato [Azure Databricks](https://azure.microsoft.com/services/databricks/), con [HDInsight](https://docs.microsoft.com/azure/hdinsight/spark/apache-spark-overview) sono disponibili anche i cluster Spark.

L'esempio seguente usa due notebook di Scala, uno per lo streaming di eventi da un Hub eventi e l'altro per l'invio di eventi all'Hub eventi.

## <a name="prerequisites"></a>prerequisiti

* Una sottoscrizione di Azure. Se non se ne ha una, [creare un account gratuito](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)
* Un'istanza di Hub eventi. Se non se ne ha una, [crearla](https://docs.microsoft.com/azure/event-hubs/event-hubs-create)
* Un'istanza di [Azure Databricks](https://azure.microsoft.com/services/databricks/). Se non se ne ha una, [crearla](https://docs.microsoft.com/azure/azure-databricks/quickstart-create-databricks-workspace-portal)
* [Creare una libreria con la coordinata Maven](https://docs.databricks.com/user-guide/libraries.html#upload-a-maven-package-or-spark-package): `com.microsoft.azure:azure‐eventhubs‐spark_2.11:2.3.0`

Usare il frammento di codice seguente nel notebook per effettuare lo streaming di eventi dall'Hub eventi.

```scala
import org.apache.spark.eventhubs._

// To connect to an Event Hub, EntityPath is required as part of the connection string.
// Here, we assume that the connection string from the Azure portal does not have the EntityPath part.
val connectionString = ConnectionStringBuilder("{EVENT HUB CONNECTION STRING FROM AZURE PORTAL}")
  .setEventHubName("{EVENT HUB NAME}")
  .build 
val ehConf = EventHubsConf(connectionString)
  .setStartingPosition(EventPosition.fromEndOfStream)

// Create a stream that reads data from the specified Event Hub.
val reader = spark.readStream
  .format("eventhubs")
  .options(ehConf.toMap)
  .load()

// Select the body column and cast it to a string.
val eventhubs = reader
  .select("CAST (body AS STRING)")
  .as[String]
```
Il frammento di codice seguente viene usato per l'invio di eventi all'Hub eventi con l'API batch di Spark. È anche possibile scrivere una query di streaming per inviare gli eventi all'Hub eventi.

```scala
import org.apache.spark.eventhubs._
import org.apache.spark.sql.functions._

// To connect to an Event Hub, EntityPath is required as part of the connection string.
// Here, we assume that the connection string from the Azure portal does not have the EntityPath part.
val connectionString = ConnectionStringBuilder("{EVENT HUB CONNECTION STRING FROM AZURE PORTAL}")
  .setEventHubName("{EVENT HUB NAME}")
  .build

val eventHubsConf = EventHubsConf(connectionString)

// Create a column representing the partitionKey.
val partitionKeyColumn = (col("id") % 5).cast("string").as("partitionKey")
// Create random strings as the body of the message.
val bodyColumn = concat(lit("random nunmber: "), rand()).as("body")

// Write 200 rows to the specified Event Hub.
val df = spark.range(200).select(partitionKeyColumn, bodyColumn)
df.write
  .format("eventhubs")
  .options(eventHubsConf.toMap)
  .save() 

```

Questo articolo offre una rapida panoramica del funzionamento del connettore Hub eventi per la creazione di soluzioni di streaming in tempo reale con tolleranza di errore. Per altre informazioni sullo streaming strutturato e sul connettore integrato Hub eventi, vedere i passaggi successivi.

## <a name="next-steps"></a>Passaggi successivi

* [Guida all'integrazione dello streaming strutturato con Hub eventi di Azure](https://github.com/Azure/azure-event-hubs-spark/blob/master/docs/structured-streaming-eventhubs-integration.md)
* [Guida all'integrazione di Spark Streaming con Hub eventi](https://github.com/Azure/azure-event-hubs-spark/blob/master/docs/spark-streaming-eventhubs-integration.md)
