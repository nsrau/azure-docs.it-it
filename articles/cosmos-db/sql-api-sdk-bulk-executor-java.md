---
title: "Azure Cosmos DB: API Java per esecuzioni bulk, risorse dell'SDK &"
description: Informazioni complete sull'SDK e sull'API Java per BulkExecutor, incluse le date di rilascio e di ritiro e le modifiche apportate tra le singole versioni di Azure Cosmos DB BulkExecutor Java SDK.
author: milismsft
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: java
ms.topic: reference
ms.date: 11/21/2018
ms.author: adrianmi
ms.openlocfilehash: 0030f974a36dc80dc8c4112000aa5934126a2482
ms.sourcegitcommit: 11572a869ef8dbec8e7c721bc7744e2859b79962
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/05/2020
ms.locfileid: "82836445"
---
# <a name="java-bulk-executor-library-download-information"></a>Libreria Java BulkExecutor: informazioni sul download

> [!div class="op_single_selector"]
> * [.NET](sql-api-sdk-dotnet.md)
> * [Feed delle modifiche .NET](sql-api-sdk-dotnet-changefeed.md)
> * [.NET Core](sql-api-sdk-dotnet-core.md)
> * [Node.js](sql-api-sdk-node.md)
> * [Async Java](sql-api-sdk-async-java.md)
> * [Java](sql-api-sdk-java.md)
> * [Python](sql-api-sdk-python.md)
> * [REST](https://docs.microsoft.com/rest/api/cosmos-db/)
> * [Provider di risorse REST](https://docs.microsoft.com/rest/api/cosmos-db-resource-provider/)
> * [SQL](sql-api-query-reference.md)
> * [Executor in blocco-.NET](sql-api-sdk-bulk-executor-dot-net.md)
> * [Executor in blocco-Java](sql-api-sdk-bulk-executor-java.md)

| |  |
|---|---|
|**Descrizione**|La libreria dell'executor bulk consente alle applicazioni client di eseguire operazioni in blocco negli account di Azure Cosmos DB. La libreria dell'executor bulk fornisce gli spazi dei nomi per BulkImport e BulkUpdate. Il modulo BulkImport può inserire in blocco i documenti in maniera ottimizzata, in modo che la velocità effettiva di cui è stato eseguito il provisioning per una raccolta venga utilizzata nella misura massima consentita. Il modulo BulkUpdate può aggiornare in blocco i dati esistenti in Azure Cosmos Containers come patch.|
|**Download dell'SDK**|[Maven](https://search.maven.org/#search%7Cga%7C1%7Cdocumentdb-bulkexecutor)|
|**Libreria Executor in blocco in GitHub**|[GitHub](https://github.com/Azure/azure-cosmosdb-bulkexecutor-java-getting-started)|
| **Documentazione API**| [Documentazione di riferimento API Java](https://docs.microsoft.com/java/api/com.microsoft.azure.documentdb.bulkexecutor)|
|**Operazioni preliminari**|[Introduzione a Java SDK per la libreria BulkExecutor](bulk-executor-java.md)|
|**Runtime minimo supportato**|[Java Development Kit (JDK) 7 +](/java/azure/jdk/?view=azure-java-stable)|

## <a name="release-notes"></a>Note sulla versione

### <a name="2100"></a><a name="2.10.0"/>2.10.0

* Correzione per DocumentAnalyzer. Java per estrarre correttamente i valori delle chiavi di partizione annidate da JSON.

### <a name="294"></a><a name="2.9.4"/>2.9.4

* Aggiungere la funzionalità nelle operazioni BulkDelete per riprovare in caso di errori specifici e restituire anche un elenco di errori all'utente che potrebbero essere ripetuti.

### <a name="293"></a><a name="2.9.3"/>2.9.3

* Aggiornamento per Cosmos SDK versione 2.4.7.

### <a name="292"></a><a name="2.9.2"/>2.9.2

* Correzione per "mergeAll" per continuare con "ID" e il valore della chiave di partizione in modo che tutte le proprietà del documento con patch inserite dopo "ID" e il valore della chiave di partizione vengano aggiunte all'elenco di elementi aggiornati.

### <a name="291"></a><a name="2.9.1"/>2.9.1

* Aggiornare il grado di concorrenza iniziale a 1 e aggiungere i log di debug per minibatch.


