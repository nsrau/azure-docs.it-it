---
title: Integrazione con Apache Spark - Hub eventi di Azure | Microsoft Docs
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
ms.custom: seodec18
ms.date: 12/06/2018
ms.author: shvija
ms.openlocfilehash: 605669a740663040ab7a167bf266fe1940123afc
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60343393"
---
# <a name="integrating-apache-spark-with-azure-event-hubs"></a>Integrazione di Apache Spark con Hub eventi di Azure

Hub eventi di Azure si integra perfettamente con [Apache Spark](https://spark.apache.org/) per consentire la creazione di applicazioni di streaming distribuite. Questa integrazione supporta [Spark Core](https://spark.apache.org/docs/latest/rdd-programming-guide.html), [Spark Streaming](https://spark.apache.org/docs/latest/streaming-programming-guide.html) e il [flusso strutturato](https://spark.apache.org/docs/latest/structured-streaming-programming-guide.html). Il connettore Hub eventi per Apache Spark è disponibile in [GitHub](https://github.com/Azure/azure-event-hubs-spark). La libreria è disponibile anche per l'uso in progetti Maven dal [repository centrale di Maven](https://search.maven.org/#artifactdetails%7Ccom.microsoft.azure%7Cazure-eventhubs-spark_2.11%7C2.1.6%7C).

Questo articolo descrive come creare un'applicazione continua in [Azure Databricks](https://azure.microsoft.com/services/databricks/). Sebbene in questo articolo venga usato Azure Databricks, i cluster Spark sono disponibili anche con [HDInsight](../hdinsight/spark/apache-spark-overview.md).

L'esempio in questo articolo usa due notebook di Scala: uno per lo streaming di eventi da un hub eventi e l'altro per l'invio di eventi all'hub.

## <a name="prerequisites"></a>Prerequisiti

* Una sottoscrizione di Azure. Se non se ne possiede una, [creare un account gratuito](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).
* Un'istanza di Hub eventi. Se non se ne possiede una, [crearla](event-hubs-create.md).
* Un'istanza di [Azure Databricks](https://azure.microsoft.com/services/databricks/). Se non se ne possiede una, [crearla](../azure-databricks/quickstart-create-databricks-workspace-portal.md).
* [Creare una libreria con le coordinata Maven](https://docs.databricks.com/user-guide/libraries.html#upload-a-maven-package-or-spark-package): `com.microsoft.azure:azure‐eventhubs‐spark_2.11:2.3.1`.

Eseguire lo streaming di eventi dall'hub eventi usando il codice seguente:

```scala
import org.apache.spark.eventhubs._

// To connect to an event hub, EntityPath is required as part of the connection string.
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
val eventhubs = reader.select($"body" cast "string")

eventhubs.writeStream
  .format("console")
  .outputMode("append")
  .start()
  .awaitTermination()
```
Il codice seguente invia gli eventi all'hub eventi con le API batch di Spark. È anche possibile scrivere una query di streaming per inviare gli eventi all'hub eventi:

```scala
import org.apache.spark.eventhubs._
import org.apache.spark.sql.functions._

// To connect to an event hub, EntityPath is required as part of the connection string.
// Here, we assume that the connection string from the Azure portal does not have the EntityPath part.
val connectionString = ConnectionStringBuilder("{EVENT HUB CONNECTION STRING FROM AZURE PORTAL}")
  .setEventHubName("{EVENT HUB NAME}")
  .build
val ehConf = EventHubsConf(connectionString)

// Create random strings as the body of the message.
val bodyColumn = concat(lit("random nunmber: "), rand()).as("body")

// Write 200 rows to the specified event hub.
val df = spark.range(200).select(bodyColumn)
df.write
  .format("eventhubs")
  .options(ehConf.toMap)
  .save() 
```

## <a name="next-steps"></a>Passaggi successivi

Questo articolo ha illustrato come configurare un flusso scalabile e a tolleranza di errore usando il connettore Hub eventi per Apache Spark. Per altre informazioni sull'uso di Hub eventi con lo streaming strutturato e Spark Streaming, vedere questi collegamenti:

* [Guida all'integrazione dello streaming strutturato con Hub eventi di Azure](https://github.com/Azure/azure-event-hubs-spark/blob/master/docs/structured-streaming-eventhubs-integration.md)
* [Guida all'integrazione di Spark Streaming con Hub eventi](https://github.com/Azure/azure-event-hubs-spark/blob/master/docs/spark-streaming-eventhubs-integration.md)
