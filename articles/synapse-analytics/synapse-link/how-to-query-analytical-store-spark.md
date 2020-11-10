---
title: Interagire con Azure Cosmos DB usando Apache Spark nel collegamento ad Azure Synapse (anteprima)
description: Interagire con Azure Cosmos DB usando Apache Spark nel collegamento ad Azure Synapse
services: synapse-analytics
author: ArnoMicrosoft
ms.service: synapse-analytics
ms.topic: quickstart
ms.subservice: synapse-link
ms.date: 09/15/2020
ms.author: acomet
ms.reviewer: jrasnick
ms.openlocfilehash: 2e06f0918ce23beded7475f644e7cc6019facacc
ms.sourcegitcommit: 96918333d87f4029d4d6af7ac44635c833abb3da
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/04/2020
ms.locfileid: "93322584"
---
# <a name="interact-with-azure-cosmos-db-using-apache-spark-in-azure-synapse-link-preview"></a>Interagire con Azure Cosmos DB usando Apache Spark nel collegamento ad Azure Synapse (anteprima)

In questo articolo si apprenderà a interagire con Azure Cosmos DB usando Synapse Apache Spark. Grazie al supporto completo per Scala, Python, SparkSQL e C#, Apache Spark per Synapse è fondamentale per gli scenari di analisi, ingegneria dei dati, data science e di esplorazione dei dati nel collegamento ad [Azure Synapse per Azure Cosmos DB](../../cosmos-db/synapse-link.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json).

Le funzionalità seguenti sono supportate durante l'interazione con Azure Cosmos DB:
* Apache Spark per Synapse consente di analizzare i dati nei contenitori Azure Cosmos DB abilitati con il collegamento ad Azure Synapse quasi in tempo reale senza influire sulle prestazioni dei carichi di lavoro transazionali. Per eseguire una query sull'[archivio analitico](../../cosmos-db/analytical-store-introduction.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json) di Azure Cosmos DB da Spark sono disponibili le due opzioni seguenti:
    + Creazione di un frame di dati Spark
    + Creazione di una tabella Spark
* Apache Spark per Synapse consente inoltre di inserire dati in Azure Cosmos DB. È importante notare che i dati vengono sempre inseriti in contenitori di Azure Cosmos DB tramite l'archivio transazionale. Quando il collegamento a Synapse è abilitato, eventuali nuovi inserimenti, aggiornamenti ed eliminazioni vengono sincronizzati automaticamente con l'archivio analitico.
* Apache Spark per Synapse supporta anche lo streaming strutturato Spark con Azure Cosmos DB come origine, oltre a un sink. 

Le sezioni seguenti illustrano la sintassi delle funzionalità indicate precedentemente. I movimenti nell'area di lavoro di Azure Synapse Analytics sono progettati per offrire una semplice esperienza predefinita per iniziare. I movimenti sono visibili quando si fa clic con il pulsante destro del mouse su un contenitore Azure Cosmos DB nella scheda **Dati** dell'area di lavoro di Synapse. Con i movimenti è possibile generare rapidamente il codice e modificarlo in base alle esigenze. Sono inoltre perfetti per l'individuazione dei dati con un singolo clic.

## <a name="query-azure-cosmos-db-analytical-store"></a>Eseguire query sull'archivio analitico di Azure Cosmos DB

Prima di acquisire informazioni sulle due opzioni possibili per eseguire query sull'archivio analitico di Azure Cosmos DB, ovvero mediante il caricamento nel dataframe Spark e la creazione di una tabella Spark, è opportuno esplorare le differenze dell'esperienza, in modo da poter scegliere l'opzione adatta alle proprie esigenze.

La differenza nell'esperienza si verifica se le modifiche ai dati nel contenitore di Azure Cosmos DB devono essere riflesse automaticamente nell'analisi eseguita in Spark. Quando un dataframe Spark viene registrato o viene creata una tabella Spark rispetto all'archivio analitico di un contenitore, i metadati relativi allo snapshot corrente dei dati nell'archivio analitico vengono recuperati in Spark per una distribuzione efficiente dell'analisi successiva. È importante tenere presente che, poiché Spark segue i criteri di valutazione lazy, a meno che non venga richiamata un'azione nel dataframe Spark o venga eseguita una query SparkSQL sulla tabella Spark, i dati effettivi non vengono recuperati dall'archivio analitico del contenitore sottostante.

In caso di **caricamento nel dataframe Spark** , i metadati recuperati vengono memorizzati nella cache per tutta la durata della sessione Spark e quindi le azioni successive richiamate nel dataframe vengono valutate rispetto allo snapshot dell'archivio analitico al momento della creazione del dataframe.

D'altra parte, in aso di **creazione di una tabella Spark** , i metadati dello stato dell'archivio analitico non vengono memorizzati nella cache in Spark e vengono ricaricati a ogni esecuzione della query SparkSQL sulla tabella Spark.

Pertanto, è possibile scegliere tra il caricamento nel dataframe Spark e la creazione di una tabella Spark a seconda che si desideri che l'analisi Spark venga valutata in base a uno snapshot fisso dell'archivio analitico o allo snapshot più recente dell'archivio analitico.

> [!NOTE]
> Per eseguire una query sull'API di Azure Cosmos DB degli account del DB Mongo, è possibile ottenere altre informazioni sulla [rappresentazione dello schema con fedeltà completa](../../cosmos-db/analytical-store-introduction.md#analytical-schema) nell'archivio analitico e sui nomi di proprietà estese da usare.

### <a name="load-to-spark-dataframe"></a>Creazione di un frame di dati Spark

In questo esempio verrà creato un dataframe Spark che punta all'archivio analitico di Azure Cosmos DB. È quindi possibile eseguire un'analisi aggiuntiva richiamando le azioni Spark sul dataframe. Questa operazione non ha alcun impatto sull'archivio transazionale.

La sintassi in **Python** è la seguente:
```python
# To select a preferred list of regions in a multi-region Azure Cosmos DB account, add .option("spark.cosmos.preferredRegions", "<Region1>,<Region2>")

df = spark.read.format("cosmos.olap")\
    .option("spark.synapse.linkedService", "<enter linked service name>")\
    .option("spark.cosmos.container", "<enter container name>")\
    .load()
```

La sintassi equivalente in **Scala** sarà la seguente:
```java
// To select a preferred list of regions in a multi-region Azure Cosmos DB account, add option("spark.cosmos.preferredRegions", "<Region1>,<Region2>")

val df_olap = spark.read.format("cosmos.olap").
    option("spark.synapse.linkedService", "<enter linked service name>").
    option("spark.cosmos.container", "<enter container name>").
    load()
```

### <a name="create-spark-table"></a>Creazione di una tabella Spark

In questo esempio verrà creata una tabella Spark che punta all'archivio analitico di Azure Cosmos DB. È quindi possibile eseguire un'analisi aggiuntiva richiamando le query SparkSQL sulla tabella. Questa operazione non ha alcun effetto sull'archivio transazionale, né comporta alcuno spostamento dei dati. Se si decide di eliminare la tabella Spark, il contenitore Azure Cosmos DB sottostante e l'archivio analitico corrispondente non saranno interessati. 

Questo scenario è utile per riusare le tabelle Spark tramite strumenti di terze parti e fornire accessibilità ai dati sottostanti per il runtime.

La sintassi per creare una tabella Spark è la seguente:
```sql
%%sql
-- To select a preferred list of regions in a multi-region Azure Cosmos DB account, add spark.cosmos.preferredRegions '<Region1>,<Region2>' in the config options

create table call_center using cosmos.olap options (
    spark.synapse.linkedService '<enter linked service name>',
    spark.cosmos.container '<enter container name>'
)
```

> [!NOTE]
> Se si hanno scenari in cui lo schema del contenitore Azure Cosmos DB sottostante cambia nel tempo e se si vuole che lo schema aggiornato rifletta automaticamente le query sulla tabella Spark, è possibile ottenere questo risultato impostando l' `spark.cosmos.autoSchemaMerge`opzione su `true` nelle opzioni della tabella Spark.


## <a name="write-spark-dataframe-to-azure-cosmos-db-container"></a>Scrivere un dataframe Spark sul contenitore Azure Cosmos DB

In questo esempio si scriverà un dataframe Spark su un contenitore Azure Cosmos DB. Questa operazione influirà sulle prestazioni dei carichi di lavoro transazionali e userà le unità richieste sottoposte a provisioning sul contenitore Azure Cosmos DB o sul database condiviso.

La sintassi in **Python** è la seguente:
```python
# Write a Spark DataFrame into an Azure Cosmos DB container
# To select a preferred list of regions in a multi-region Azure Cosmos DB account, add .option("spark.cosmos.preferredRegions", "<Region1>,<Region2>")

YOURDATAFRAME.write.format("cosmos.oltp")\
    .option("spark.synapse.linkedService", "<enter linked service name>")\
    .option("spark.cosmos.container", "<enter container name>")\
    .option("spark.cosmos.write.upsertEnabled", "true")\
    .mode('append')\
    .save()
```

La sintassi equivalente in **Scala** sarà la seguente:
```java
// To select a preferred list of regions in a multi-region Azure Cosmos DB account, add option("spark.cosmos.preferredRegions", "<Region1>,<Region2>")

import org.apache.spark.sql.SaveMode

df.write.format("cosmos.oltp").
    option("spark.synapse.linkedService", "<enter linked service name>").
    option("spark.cosmos.container", "<enter container name>"). 
    option("spark.cosmos.write.upsertEnabled", "true").
    mode(SaveMode.Overwrite).
    save()
```

## <a name="load-streaming-dataframe-from-container"></a>Caricamento di dataframe in streaming da un contenitore
In questo gesto si userà la funzionalità di streaming di Spark per caricare i dati da un contenitore in un dataframe. I dati verranno archiviati nell'account Data Lake primario (e nel file system) connesso all'area di lavoro. 
> [!NOTE]
> Altre informazioni su come fare riferimento alle librerie esterne in Synapse Apache Spark, sono disponibili [qui](#external-library-management). Ad esempio, se si sta cercando di inserire un dataframe Spark su un contenitore di API di Cosmos DB per Mongo DB, è possibile usare il connettore Mongo DB per Spark [qui](https://docs.mongodb.com/spark-connector/master/).

## <a name="load-streaming-dataframe-from-azure-cosmos-db-container"></a>Caricamento di dataframe in streaming da un contenitore Azure Cosmos DB
In questo esempio si userà la funzionalità di streaming strutturato di Spark per caricare i dati da un contenitore Azure Cosmos DB su un dataframe di streaming Spark usando la funzionalità del feed di modifiche in Azure Cosmos DB. I dati del checkpoint usati da Spark verranno archiviati nell'account data lake primario (e nel file system) connesso all'area di lavoro.

Se la cartella */localReadCheckpointFolder* non è stata creata (nell'esempio sottostante), verrà creata automaticamente. Questa operazione influirà sulle prestazioni dei carichi di lavoro transazionali e userà le unità richieste sottoposte a provisioning sul contenitore Azure Cosmos DB o sul database condiviso.

La sintassi in **Python** è la seguente:
```python
# To select a preferred list of regions in a multi-region Azure Cosmos DB account, add .option("spark.cosmos.preferredRegions", "<Region1>,<Region2>")

dfStream = spark.readStream\
    .format("cosmos.oltp")\
    .option("spark.synapse.linkedService", "<enter linked service name>")\
    .option("spark.cosmos.container", "<enter container name>")\
    .option("spark.cosmos.changeFeed.readEnabled", "true")\
    .option("spark.cosmos.changeFeed.startFromTheBeginning", "true")\
    .option("spark.cosmos.changeFeed.checkpointLocation", "/localReadCheckpointFolder")\
    .option("spark.cosmos.changeFeed.queryName", "streamQuery")\
    .load()
```

La sintassi equivalente in **Scala** sarà la seguente:
```java
// To select a preferred list of regions in a multi-region Azure Cosmos DB account, add .option("spark.cosmos.preferredRegions", "<Region1>,<Region2>")

val dfStream = spark.readStream.
    format("cosmos.oltp").
    option("spark.synapse.linkedService", "<enter linked service name>").
    option("spark.cosmos.container", "<enter container name>").
    option("spark.cosmos.changeFeed.readEnabled", "true").
    option("spark.cosmos.changeFeed.startFromTheBeginning", "true").
    option("spark.cosmos.changeFeed.checkpointLocation", "/localReadCheckpointFolder").
    option("spark.cosmos.changeFeed.queryName", "streamQuery").
    load()
```

## <a name="write-streaming-dataframe-to-azure-cosmos-db-container"></a>Scrittura di dataframe in streaming su un contenitore Azure Cosmos DB
In questo esempio si scriverà un dataframe in streaming su un contenitore Azure Cosmos DB. Questa operazione influirà sulle prestazioni dei carichi di lavoro transazionali e userà le unità richieste sottoposte a provisioning sul contenitore Azure Cosmos DB o sul database condiviso. Se la cartella */localWriteCheckpointFolder* non è stata creata (nell'esempio sottostante), verrà creata automaticamente. 

La sintassi in **Python** è la seguente:
```python
# To select a preferred list of regions in a multi-region Azure Cosmos DB account, add .option("spark.cosmos.preferredRegions", "<Region1>,<Region2>")

streamQuery = dfStream\
        .writeStream\
        .format("cosmos.oltp")\
        .outputMode("append")\
        .option("checkpointLocation", "/localWriteCheckpointFolder")\
        .option("spark.synapse.linkedService", "<enter linked service name>")\
        .option("spark.cosmos.container", "<enter container name>")\
        .option("spark.cosmos.connection.mode", "gateway")\
        .start()

streamQuery.awaitTermination()
```

La sintassi equivalente in **Scala** sarà la seguente:
```java
// To select a preferred list of regions in a multi-region Azure Cosmos DB account, add .option("spark.cosmos.preferredRegions", "<Region1>,<Region2>")

val query = dfStream.
            writeStream.
            format("cosmos.oltp").
            outputMode("append").
            option("checkpointLocation", "/localWriteCheckpointFolder").
            option("spark.synapse.linkedService", "<enter linked service name>").
            option("spark.cosmos.container", "<enter container name>").
            option("spark.cosmos.connection.mode", "gateway").
            start()

query.awaitTermination()
```

## <a name="external-library-management"></a>Gestione della libreria esterna

In questo esempio si apprenderà come fare riferimento alle librerie esterne da file JAR quando si usano notebook Spark in aree di lavoro Synpase Apache Spark. È possibile inserire i file JAR in un contenitore nell'account data lake primario connesso all'area di lavoro e quindi aggiungere l'istruzione `%configure` seguente nel notebook Spark:

```cmd
%%configure -f
{
    "jars": [
        "abfss://<storage container name>@<data lake account name>.dfs.core.windows.net/<path to jar>"
    ]
}
```
Se si vogliono inviare definizioni di processi Spark in modalità remota a un pool di Apache Spark serverless, è possibile apprendere come fare riferimento alle librerie esterne seguendo questa [esercitazione ](../spark/apache-spark-job-definitions.md).

## <a name="next-steps"></a>Passaggi successivi

* [Esempi per iniziare a usare Collegamento ad Azure Synapse in GitHub](https://aka.ms/cosmosdb-synapselink-samples)
* [Altre informazioni sulle funzionalità supportate da Collegamento ad Azure Synapse per Azure Cosmos DB](./concept-synapse-link-cosmos-db-support.md)
* [Connettersi a Collegamento a Synapse per Azure Cosmos DB](../quickstart-connect-synapse-link-cosmos-db.md)
