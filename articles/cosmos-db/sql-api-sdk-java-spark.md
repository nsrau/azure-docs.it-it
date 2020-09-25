---
title: Cosmos DB Apache Spark Connector per le risorse e le note sulla versione dell'API SQL
description: Informazioni sul connettore Azure Cosmos DB Apache Spark per l'API SQL, incluse le date di rilascio, le date di ritiro e le modifiche apportate tra le singole versioni di Azure Cosmos DB SQL Async Java SDK.
author: anfeldma-ms
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: java
ms.topic: reference
ms.date: 08/12/2020
ms.author: anfeldma
ms.custom: devx-track-java
ms.openlocfilehash: afee95f6a8776c3506e10c29cfd8e776734a915a
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/25/2020
ms.locfileid: "91326678"
---
# <a name="azure-cosmos-db-apache-spark-connector-for-core-sql-api-release-notes-and-resources"></a>Azure Cosmos DB Apache Spark Connector per l'API di base (SQL): Note sulla versione e risorse
> [!div class="op_single_selector"]
> * [.NET SDK v3](sql-api-sdk-dotnet-standard.md)
> * [.NET SDK v2](sql-api-sdk-dotnet.md)
> * [.NET Core SDK v2](sql-api-sdk-dotnet-core.md)
> * [SDK di feed di modifiche .NET v2](sql-api-sdk-dotnet-changefeed.md)
> * [Node.js](sql-api-sdk-node.md)
> * [Java SDK v4](sql-api-sdk-java-v4.md)
> * [Async Java SDK v2](sql-api-sdk-async-java.md)
> * [Sync Java SDK v2](sql-api-sdk-java.md)
> * [Spring data V2](sql-api-sdk-java-spring-v2.md)
> * [Spring data V3](sql-api-sdk-java-spring-v3.md)
> * [Connettore Spark](sql-api-sdk-java-spark.md)
> * [Python](sql-api-sdk-python.md)
> * [REST](/rest/api/cosmos-db/)
> * [Provider di risorse REST](/rest/api/cosmos-db-resource-provider/)
> * [SQL](sql-api-query-reference.md)
> * [Executor in blocco-.NET v2](sql-api-sdk-bulk-executor-dot-net.md)
> * [Esecuzione bulk - Java](sql-api-sdk-bulk-executor-java.md)

È possibile accelerare Big Data Analytics usando il connettore di Apache Spark Azure Cosmos DB per core (SQL). Il connettore Spark consente di eseguire processi [Spark](https://spark.apache.org/) sui dati archiviati in Azure Cosmos DB. L'elaborazione di batch e flussi è supportata.

È possibile usare il connettore con [Azure Databricks](https://azure.microsoft.com/services/databricks) o [Azure HDInsight](https://azure.microsoft.com/services/hdinsight/), che forniscono cluster Spark gestiti in Azure. Nella tabella seguente vengono illustrate le versioni supportate:

| Componente | Versione |
|---------|-------|
| Apache Spark | 2,4.*x*, 2,3. *x*, 2,2. *x*e 2,1. *x* |
| Scala | 2,11 |
| Azure Databricks (versione Runtime) | Successiva alla 3,4 |

> [!WARNING]
> Questo connettore supporta l'API principale (SQL) di Azure Cosmos DB.
> Per l'API Cosmos DB per MongoDB, usare il [connettore MongoDB per Spark](https://docs.mongodb.com/spark-connector/master/).
> Per il API Cassandra Cosmos DB, usare il [connettore Cassandra Spark](https://github.com/datastax/spark-cassandra-connector).
>

## <a name="resources"></a>Risorse

| Risorsa | Collegamento |
|---|---|
| **Download dell'SDK** | [Scaricare la versione più recente di. jar](https://aka.ms/CosmosDB_OLTP_Spark_2.4_LKG), [Maven](https://search.maven.org/search?q=a:azure-cosmosdb-spark_2.4.0_2.11) |
|**Documentazione sull'API** | [Riferimento al connettore Spark]() |
|**Contribuire all'SDK** | [Connettore Azure Cosmos DB per Apache Spark su GitHub](https://github.com/Azure/azure-cosmosdb-spark) | 
|**Attività iniziali** | [Accelerare l'analisi di Big Data con il connettore Apache Spark per Azure Cosmos DB](https://docs.microsoft.com/azure/cosmos-db/spark-connector#bk_working_with_connector) <br> [Usare Apache Spark Structured Streaming con Apache Kafka e Azure Cosmos DB](https://docs.microsoft.com/azure/hdinsight/apache-kafka-spark-structured-streaming-cosmosdb?toc=/azure/cosmos-db/toc.json&bc=/azure/cosmos-db/breadcrumb/toc.json) | 

## <a name="release-history"></a>Cronologia delle versioni

### <a name="330"></a>3.3.0
#### <a name="new-features"></a>Nuove funzionalità
- Aggiunge una nuova opzione di configurazione, `changefeedstartfromdatetime` , che può essere usata per specificare l'ora di inizio per l'elaborazione del offre. Per ulteriori informazioni, vedere [Opzioni di configurazione](https://github.com/Azure/azure-cosmosdb-spark/wiki/Configuration-references).

### <a name="320"></a>3.2.0
#### <a name="key-bug-fixes"></a>Correzioni di bug chiave
- Corregge una regressione che ha causato un utilizzo eccessivo della memoria negli esecutori per set di risultati di grandi dimensioni (ad esempio, con milioni di righe), causando infine l'errore `java.lang.OutOfMemoryError: GC overhead limit exceeded` .

### <a name="311"></a>3.1.1
#### <a name="key-bug-fixes"></a>Correzioni di bug chiave
* Corregge un caso di Edge del checkpoint di streaming in cui `ID` contiene il carattere barra verticale (|) con la `ChangeFeedMaxPagesPerBatch` configurazione applicata.

### <a name="310"></a>3.1.0
#### <a name="new-features"></a>Nuove funzionalità
* Aggiunge il supporto per gli aggiornamenti in blocco quando si utilizzano chiavi di partizione nidificate.
* Aggiunge il supporto per i tipi di dati Decimal e float durante le Scritture Azure Cosmos DB.
* Aggiunge il supporto per i tipi timestamp quando usa Long (Unix Epoch) come valore.

### <a name="308"></a>3.0.8
#### <a name="key-bug-fixes"></a>Correzioni di bug chiave
* Corregge l'eccezione typecast che si verifica quando `WriteThroughputBudget` viene utilizzata la configurazione.

### <a name="307"></a>3.0.7
#### <a name="new-features"></a>Nuove funzionalità
* Aggiunge informazioni sull'errore per gli errori bulk per l'eccezione e il log.

### <a name="306"></a>3.0.6
#### <a name="key-bug-fixes"></a>Correzioni di bug chiave
* Corregge i problemi di checkpoint di streaming.

### <a name="305"></a>3.0.5
#### <a name="key-bug-fixes"></a>Correzioni di bug chiave
* Per ridurre il rumore, corregge il livello di registrazione di un messaggio lasciato involontariamente con un errore di livello.

### <a name="304"></a>3.0.4
#### <a name="key-bug-fixes"></a>Correzioni di bug chiave
* Corregge un bug nel flusso strutturato durante le divisioni di partizione. Il bug potrebbe causare la mancata presenza di record del feed di modifiche o eccezioni null per le Scritture del checkpoint.

### <a name="303"></a>3.0.3
#### <a name="key-bug-fixes"></a>Correzioni di bug chiave
* Corregge un bug che determina l'Ignorazione di uno schema personalizzato fornito per readStream.

### <a name="302"></a>3.0.2
#### <a name="key-bug-fixes"></a>Correzioni di bug chiave
* Corregge una regressione (il file JAR non ombreggiato include tutte le dipendenze ombreggiate) che aumenta il tempo di compilazione del 50%.

### <a name="301"></a>3.0.1
#### <a name="key-bug-fixes"></a>Correzioni di bug chiave
* Corregge un problema di dipendenza che causa l'esito negativo del trasporto diretto su TCP con RequestTimeoutException.

### <a name="300"></a>3.0.0
#### <a name="new-features"></a>Nuove funzionalità
* Migliora la gestione della connessione e il pool di connessioni per ridurre il numero di chiamate di metadati.

## <a name="faq"></a>Domande frequenti
[!INCLUDE [cosmos-db-sdk-faq](../../includes/cosmos-db-sdk-faq.md)]

## <a name="next-steps"></a>Passaggi successivi

Altre informazioni su [Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db/).

Per altre informazioni, vedere [Apache Spark](https://spark.apache.org/).
