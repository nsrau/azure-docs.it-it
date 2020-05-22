---
title: Eseguire query sull'archivio analitico di Azure Cosmos DB con Apache Spark per Azure Synapse Analytics
description: Come eseguire query sull'archivio analitico di Azure Cosmos DB con Apache Spark per Azure Synapse Analytics
services: synapse-analytics
author: ArnoMicrosoft
ms.service: synapse-analytics
ms.topic: quickstart
ms.subservice: ''
ms.date: 05/06/2020
ms.author: acomet
ms.reviewer: jrasnick
ms.openlocfilehash: 02d4b6a636bff5ef11686abba6efb52f45f04779
ms.sourcegitcommit: bb0afd0df5563cc53f76a642fd8fc709e366568b
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 05/19/2020
ms.locfileid: "83599253"
---
# <a name="query-azure-cosmos-db-analytical-store-with-apache-spark-for-azure-synapse-analytics"></a>Eseguire query sull'archivio analitico di Azure Cosmos DB con Apache Spark per Azure Synapse Analytics

Questo articolo include alcuni esempi su come interagire con l'archivio analitico dai movimenti di Synapse. I movimenti sono visibili quando si fa clic con il pulsante destro del mouse su un contenitore. Con i movimenti è possibile generare rapidamente il codice e modificarlo in base alle esigenze. Sono inoltre perfetti per l'individuazione dei dati con un singolo clic.

## <a name="load-to-dataframe"></a>Caricamento nel dataframe

In questo passaggio verranno letti i dati dall'archivio analitico di Azure Cosmos DB in un dataframe Spark. Verranno visualizzate 10 righe dal dataframe denominato ***df***. Una volta che i dati sono nel dataframe, è possibile eseguire un'analisi aggiuntiva.

Questa operazione non ha alcun impatto sull'archivio transazionale.

```python
# To select a preferred list of regions in a multi-region Azure Cosmos DB account, add .option("spark.cosmos.preferredRegions", "<Region1>,<Region2>")

df = spark.read.format("cosmos.olap")\
    .option("spark.synapse.linkedService", "INFERRED")\
    .option("spark.cosmos.container", "INFERRED")\
    .load()

df.show(10)
```

Il gesto del codice equivalente in **Scala** sarà il codice seguente:
```java
// To select a preferred list of regions in a multi-region Azure Cosmos DB account, add option("spark.cosmos.preferredRegions", "<Region1>,<Region2>")

val df_olap = spark.read.format("cosmos.olap").
    option("spark.synapse.linkedService", "pySparkSamplesDb").
    option("spark.cosmos.container", "trafficSourceColl").
    load()
```

## <a name="create-spark-table"></a>Creazione di una tabella Spark

In questo movimento verrà creata una tabella Spark che punta al contenitore selezionato. Questa operazione non comporta alcuno spostamento dei dati. Se si decide di eliminare la tabella, il contenitore sottostante e l'archivio analitico corrispondente non saranno interessati. 

Questo scenario è utile per riutilizzare le tabelle tramite strumenti di terze parti e fornire accessibilità ai dati per il runtime.

```sql
%%sql
-- To select a preferred list of regions in a multi-region Azure Cosmos DB account, add spark.cosmos.preferredRegions '<Region1>,<Region2>' in the config options

create table call_center using cosmos.olap options (
    spark.synapse.linkedService 'INFERRED',
    spark.cosmos.container 'INFERRED'
)
```

## <a name="write-dataframe-to-container"></a>Scrittura di dataframe in un contenitore

In questo gesto verrà scritto un dataframe in un contenitore. Questa operazione influirà sulle prestazioni transazionali e utilizzerà le unità richiesta. L'utilizzo delle prestazioni transazionali di Azure Cosmos DB è ideale per le transazioni di scrittura. Assicurarsi di sostituire **YOURDATAFRAME** con il dataframe in cui si vuole eseguire il writeback.

```python
# Write a Spark DataFrame into an Azure Cosmos DB container
# To select a preferred list of regions in a multi-region Azure Cosmos DB account, add .option("spark.cosmos.preferredRegions", "<Region1>,<Region2>")


YOURDATAFRAME.write.format("cosmos.oltp")\
    .option("spark.synapse.linkedService", "INFERRED")\
    .option("spark.cosmos.container", "INFERRED")\
    .option("spark.cosmos.write.upsertEnabled", "true")\
    .mode('append')\
    .save()
```

Il gesto del codice equivalente in **Scala** sarà il codice seguente:
```java
// To select a preferred list of regions in a multi-region Azure Cosmos DB account, add option("spark.cosmos.preferredRegions", "<Region1>,<Region2>")

import org.apache.spark.sql.SaveMode

df.write.format("cosmos.oltp").
    option("spark.synapse.linkedService", "pySparkSamplesDb").
    option("spark.cosmos.container", "trafficSourceColl"). 
    option("spark.cosmos.write.upsertEnabled", "true").
    mode(SaveMode.Overwrite).
    save()
```

## <a name="load-streaming-dataframe-from-container"></a>Caricamento di dataframe in streaming da un contenitore
In questo gesto si userà la funzionalità di streaming di Spark per caricare i dati da un contenitore in un dataframe. I dati verranno archiviati nell'account data lake primario (e nel file system) connesso all'area di lavoro. 

Se la cartella */localReadCheckpointFolder* non è stata creata, verrà creata automaticamente. Questa operazione influirà sulle prestazioni transazionali di Azure Cosmos DB.

```python
# To select a preferred list of regions in a multi-region Azure Cosmos DB account, add .option("spark.cosmos.preferredRegions", "<Region1>,<Region2>")

dfStream = spark.readStream\
    .format("cosmos.oltp")\
    .option("spark.synapse.linkedService", "INFERRED")\
    .option("spark.cosmos.container", "INFERRED")\
    .option("spark.cosmos.changeFeed.readEnabled", "true")\
    .option("spark.cosmos.changeFeed.startFromTheBeginning", "true")\
    .option("spark.cosmos.changeFeed.checkpointLocation", "/localReadCheckpointFolder")\
    .option("spark.cosmos.changeFeed.queryName", "streamQuery")\
    .load()
```

Il gesto del codice equivalente in **Scala** sarà il codice seguente:
```java
// To select a preferred list of regions in a multi-region Azure Cosmos DB account, add .option("spark.cosmos.preferredRegions", "<Region1>,<Region2>")

val dfStream = spark.readStream.
    format("cosmos.oltp").
    option("spark.synapse.linkedService", "pySparkSamplesDb").
    option("spark.cosmos.container", "trafficSourceColl").
    option("spark.cosmos.changeFeed.readEnabled", "true").
    option("spark.cosmos.changeFeed.startFromTheBeginning", "true").
    option("spark.cosmos.changeFeed.checkpointLocation", "/localReadCheckpointFolder").
    option("spark.cosmos.changeFeed.queryName", "streamTestRevin2").
    load()
```

## <a name="write-streaming-dataframe-to-container"></a>Scrittura di un dataframe in streaming in un contenitore
In questo gesto si scriverà un dataframe in streaming nel contenitore di Azure Cosmos DB selezionato. Se la cartella */localReadCheckpointFolder* non è stata creata, verrà creata automaticamente. Questa operazione influirà sulle prestazioni transazionali di Azure Cosmos DB.

```python
# To select a preferred list of regions in a multi-region Azure Cosmos DB account, add .option("spark.cosmos.preferredRegions", "<Region1>,<Region2>")

streamQuery = dfStream\
        .writeStream\
        .format("cosmos.oltp")\
        .outputMode("append")\
        .option("checkpointLocation", "/localWriteCheckpointFolder")\
        .option("spark.synapse.linkedService", "INFERRED")\
        .option("spark.cosmos.container", "trafficSourceColl_sink")\
        .option("spark.cosmos.connection.mode", "gateway")\
        .start()

streamQuery.awaitTermination()
```

Il gesto del codice equivalente in **Scala** sarà il codice seguente:
```java
// To select a preferred list of regions in a multi-region Azure Cosmos DB account, add .option("spark.cosmos.preferredRegions", "<Region1>,<Region2>")

val query = dfStream.
            writeStream.
            format("cosmos.oltp").
            outputMode("append").
            option("checkpointLocation", "/localWriteCheckpointFolder").
            option("spark.synapse.linkedService", "pySparkSamplesDb").
            option("spark.cosmos.container", "test2").
            option("spark.cosmos.connection.mode", "gateway").
            start()

query.awaitTermination()
```
## <a name="next-steps"></a>Passaggi successivi

* [Informazioni sulle funzionalità supportate tra Synapse e Azure Cosmos DB](./concept-synapse-link-cosmos-db-support.md)
* [Connettersi a Collegamento a Synapse per Azure Cosmos DB](../quickstart-connect-synapse-link-cosmos-db.md)
