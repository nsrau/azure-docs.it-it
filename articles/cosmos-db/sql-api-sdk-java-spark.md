---
title: Azure Cosmos DB Apache Spark Connector per le risorse e le note sulla versione dell'API SQL
description: Informazioni sul connettore Azure Cosmos DB Apache Spark per l'API SQL, incluse le date di rilascio, le date di ritiro e le modifiche apportate tra le singole versioni di Azure Cosmos DB SQL Async Java SDK.
author: anfeldma-ms
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: java
ms.topic: reference
ms.date: 08/05/2020
ms.author: anfeldma
ms.custom: devx-track-java
ms.openlocfilehash: 72b3b190492be5cec9986729875c5b09e2559ae5
ms.sourcegitcommit: 7fe8df79526a0067be4651ce6fa96fa9d4f21355
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/06/2020
ms.locfileid: "87854249"
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
> * [Spring Data](sql-api-sdk-java-spring.md)
> * [Connettore Spark](sql-api-sdk-java-spark.md)
> * [Python](sql-api-sdk-python.md)
> * [REST](/rest/api/cosmos-db/)
> * [Provider di risorse REST](/rest/api/cosmos-db-resource-provider/)
> * [SQL](sql-api-query-reference.md)
> * [Executor in blocco-.NET v2](sql-api-sdk-bulk-executor-dot-net.md)
> * [Esecuzione bulk - Java](sql-api-sdk-bulk-executor-java.md)

Accelerare Big Data Analytics usando il connettore di Apache Spark Azure Cosmos DB per core (SQL). Il connettore Spark consente di eseguire processi [Spark](https://spark.apache.org/) sui dati archiviati in Azure Cosmos DB. L'elaborazione di batch e flussi è supportata.

È possibile usare il connettore con [Azure Databricks](https://azure.microsoft.com/services/databricks) o [Azure HDInsight](https://azure.microsoft.com/services/hdinsight/), che forniscono cluster Spark gestiti in Azure. La tabella seguente illustra le versioni di Spark supportate.

| Componente | Versione |
|---------|-------|
| Apache Spark | 2.4. x, 2.3. x, 2.2. x e 2.1. x |
| Scala | 2.11 |
| Versione del runtime di Azure Databricks | > 3.4 |

> [!WARNING]
> Questo connettore supporta l'API principale (SQL) di Azure Cosmos DB.
> Per Cosmos DB per l'API MongoDB, usare il [connettore Spark MongoDB](https://docs.mongodb.com/spark-connector/master/).
> Per Cosmos DB API Cassandra, usare il [connettore Cassandra Spark](https://github.com/datastax/spark-cassandra-connector).
>

## <a name="helpful-content"></a>Contenuto utile

| Contenuto | Collegamento |
|---|---|
| **Download dell'SDK** | [Scarica da Apache Spark](https://aka.ms/CosmosDB_OLTP_Spark_2.4_LKG) |
|**Documentazione sull'API** | [Riferimento al connettore Spark]() |
|**Contribuire all'SDK** | [Connettore Azure Cosmos DB per Apache Spark su GitHub](https://github.com/Azure/azure-cosmosdb-spark) | 
|**Operazioni preliminari** | [Accelerare l'analisi di Big Data con il connettore Apache Spark per Azure Cosmos DB](https://docs.microsoft.com/azure/cosmos-db/spark-connector#bk_working_with_connector) <br> [Usare Apache Spark Structured Streaming con Apache Kafka e Azure Cosmos DB](https://docs.microsoft.com/azure/hdinsight/apache-kafka-spark-structured-streaming-cosmosdb?toc=/azure/cosmos-db/toc.json&bc=/azure/cosmos-db/breadcrumb/toc.json) | 

## <a name="release-history"></a>Cronologia delle versioni

### <a name="311"></a>3.1.1
#### <a name="new-features"></a>Nuove funzionalità
#### <a name="key-bug-fixes"></a>Correzioni di bug chiave
* Corregge un caso Edge del checkpoint di streaming in cui "ID" contiene il carattere "|" con la configurazione "ChangeFeedMaxPagesPerBatch" applicata

### <a name="310"></a>3.1.0
#### <a name="new-features"></a>Nuove funzionalità
* Aggiunta del supporto per gli aggiornamenti in blocco quando si utilizzano chiavi di partizione annidate
* Aggiunge il supporto per i tipi di dati Decimal e float durante le Scritture Cosmos DB.
* Aggiunge il supporto per i tipi timestamp quando usa Long (Unix Epoch) come valore
#### <a name="key-bug-fixes"></a>Correzioni di bug chiave

### <a name="308"></a>3.0.8
#### <a name="new-features"></a>Nuove funzionalità
#### <a name="key-bug-fixes"></a>Correzioni di bug chiave
* Corregge l'eccezione cast di tipo quando si utilizza la configurazione "WriteThroughputBudget".

### <a name="307"></a>3.0.7
#### <a name="new-features"></a>Nuove funzionalità
* Aggiunge informazioni sull'errore per gli errori bulk per l'eccezione e il log.
#### <a name="key-bug-fixes"></a>Correzioni di bug chiave

### <a name="306"></a>3.0.6
#### <a name="new-features"></a>Nuove funzionalità
#### <a name="key-bug-fixes"></a>Correzioni di bug chiave
* Corregge i problemi di checkpoint di streaming.

### <a name="305"></a>3.0.5
#### <a name="new-features"></a>Nuove funzionalità
#### <a name="key-bug-fixes"></a>Correzioni di bug chiave
* Corregge il livello di registrazione di un messaggio lasciato involontariamente con un errore di livello per ridurre il rumore

### <a name="304"></a>3.0.4
#### <a name="new-features"></a>Nuove funzionalità
#### <a name="key-bug-fixes"></a>Correzioni di bug chiave
* Corregge un bug nel flusso strutturato durante le divisioni della partizione, causando probabilmente la mancata presenza di alcuni record del feed di modifiche o la visualizzazione di eccezioni null

### <a name="303"></a>3.0.3
#### <a name="new-features"></a>Nuove funzionalità
#### <a name="key-bug-fixes"></a>Correzioni di bug chiave
* Corregge un bug in cui viene ignorato uno schema personalizzato fornito per readStream

### <a name="302"></a>3.0.2
#### <a name="new-features"></a>Nuove funzionalità
#### <a name="key-bug-fixes"></a>Correzioni di bug chiave
* Correzione della regressione (il file JAR non ombreggiato include tutte le dipendenze ombreggiate) che hanno aumentato il tempo di compilazione del 50%

### <a name="301"></a>3.0.1
#### <a name="new-features"></a>Nuove funzionalità
#### <a name="key-bug-fixes"></a>Correzioni di bug chiave
* Corregge un problema di dipendenza che causa l'esito negativo del trasporto diretto su TCP con RequestTimeoutException

### <a name="300"></a>3.0.0
#### <a name="new-features"></a>Nuove funzionalità
* Migliora la gestione della connessione e il pool di connessioni per ridurre il numero di chiamate di metadati
#### <a name="key-bug-fixes"></a>Correzioni di bug chiave

## <a name="faq"></a>Domande frequenti
[!INCLUDE [cosmos-db-sdk-faq](../../includes/cosmos-db-sdk-faq.md)]

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni su Cosmos DB, vedere la pagina del servizio [Microsoft Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db/).

Per ulteriori informazioni su Apache Spark, vedere [la Home page](https://spark.apache.org/).