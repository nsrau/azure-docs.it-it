---
title: "Azure Cosmos DB: API Java, SDK e risorse per l'esecuzione bulk"
description: Informazioni complete sull'SDK e sull'API Java per BulkExecutor, incluse le date di rilascio e di ritiro e le modifiche apportate tra le singole versioni di Azure Cosmos DB BulkExecutor Java SDK.
author: anfeldma-ms
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: java
ms.topic: reference
ms.date: 08/05/2020
ms.author: anfeldma
ms.custom: devx-track-java
ms.openlocfilehash: 1e389450562fe731ac750c779da236f379429423
ms.sourcegitcommit: 7fe8df79526a0067be4651ce6fa96fa9d4f21355
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/06/2020
ms.locfileid: "87852651"
---
# <a name="java-bulk-executor-library-download-information"></a>Libreria BulkExecutor per Java: informazioni sul download

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

| |  |
|---|---|
|**Descrizione**|La libreria dell'executor bulk consente alle applicazioni client di eseguire operazioni in blocco negli account di Azure Cosmos DB. La libreria dell'executor bulk fornisce gli spazi dei nomi per BulkImport e BulkUpdate. Il modulo BulkImport può inserire in blocco i documenti in maniera ottimizzata, in modo che la velocità effettiva di cui è stato eseguito il provisioning per una raccolta venga utilizzata nella misura massima consentita. Il modulo BulkUpdate può aggiornare in blocco i dati esistenti nei contenitori di Azure Cosmos come patch.|
|**Download dell'SDK**|[Maven](https://search.maven.org/#search%7Cga%7C1%7Cdocumentdb-bulkexecutor)|
|**Libreria di esecuzione bulk in GitHub**|[GitHub](https://github.com/Azure/azure-cosmosdb-bulkexecutor-java-getting-started)|
| **Documentazione sull'API**| [Documentazione di riferimento API Java](https://docs.microsoft.com/java/api/com.microsoft.azure.documentdb.bulkexecutor)|
|**Operazioni preliminari**|[Introduzione a Java SDK per la libreria BulkExecutor](bulk-executor-java.md)|
|**Runtime minimo supportato**|[Java Development Kit (JDK) 7+](/java/azure/jdk/?view=azure-java-stable)|

## <a name="release-notes"></a>Note sulla versione

### <a name="2100"></a><a name="2.10.0"></a>2.10.0

* Aggiornamento di DocumentAnalyzer.java per la corretta estrazione dei valori delle chiavi di partizione annidate da json.

### <a name="294"></a><a name="2.9.4"></a>2.9.4

* Aggiunta funzionalità alle operazioni BulkDelete per riprovare in caso di errori specifici e restituire all'utente anche un elenco di errori per i quali può essere eseguito un secondo tentativo.

### <a name="293"></a><a name="2.9.3"></a>2.9.3

* Aggiornamento per Cosmos SDK versione 2.4.7.

### <a name="292"></a><a name="2.9.2"></a>2.9.2

* Aggiornamento di "mergeAll" per il proseguimento in corrispondenza di "id" e del valore della chiave di partizione in modo che eventuali proprietà del documento con patch posizionate dopo "id" e il valore della chiave di partizione siano aggiunte all'elenco di elementi aggiornati.

### <a name="291"></a><a name="2.9.1"></a>2.9.1

* Aggiornato il grado di concorrenza iniziale a 1 e aggiunti i log di debug per il mini-batch.


