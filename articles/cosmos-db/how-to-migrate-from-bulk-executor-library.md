---
title: Eseguire la migrazione dalla libreria Executor bulk al supporto bulk in Azure Cosmos DB .NET V3 SDK
description: Informazioni su come eseguire la migrazione dell'applicazione usando la libreria di Executor bulk per il supporto in blocco in Azure Cosmos DB SDK V3
author: ealsur
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 04/24/2020
ms.author: maquaran
ms.openlocfilehash: d63b34c118cd719f73abbd6711dcb3ef02a6fb28
ms.sourcegitcommit: f7fb9e7867798f46c80fe052b5ee73b9151b0e0b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/24/2020
ms.locfileid: "82146300"
---
# <a name="migrate-from-the-bulk-executor-library-to-the-bulk-support-in-azure-cosmos-db-net-v3-sdk"></a>Eseguire la migrazione dalla libreria Executor bulk al supporto bulk in Azure Cosmos DB .NET V3 SDK

Questo articolo descrive i passaggi necessari per eseguire la migrazione del codice di un'applicazione esistente che usa la [libreria dell'executor di massa di .NET](bulk-executor-dot-net.md) alla funzionalità di [supporto bulk](tutorial-sql-api-dotnet-bulk-import.md) nella versione più recente di .NET SDK.

## <a name="enable-bulk-support"></a>Abilita supporto bulk

Abilitare il supporto bulk nell' `CosmosClient` istanza tramite la configurazione [AllowBulkExecution](https://docs.microsoft.com/dotnet/api/microsoft.azure.cosmos.cosmosclientoptions.allowbulkexecution) :

   :::code language="csharp" source="~/samples-cosmosdb-dotnet-v3/Microsoft.Azure.Cosmos.Samples/Usage/BulkExecutorMigration/Program.cs" ID="Initialization":::

## <a name="create-tasks-for-each-operation"></a>Crea attività per ogni operazione

Il supporto bulk in .NET SDK funziona sfruttando il [Task Parallel Library](https://docs.microsoft.com/dotnet/standard/parallel-programming/task-parallel-library-tpl) e le operazioni di raggruppamento che si verificano simultaneamente. 

Non esiste un singolo metodo nell'SDK che consentirà l'elenco dei documenti o delle operazioni come parametro di input, ma è necessario creare un'attività per ogni operazione che si desidera eseguire in blocco, quindi è sufficiente attendere che vengano completate.

Ad esempio, se l'input iniziale è un elenco di elementi in cui ogni elemento ha lo schema seguente:

   :::code language="csharp" source="~/samples-cosmosdb-dotnet-v3/Microsoft.Azure.Cosmos.Samples/Usage/BulkExecutorMigration/Program.cs" ID="Model":::

Se si desidera eseguire l'importazione bulk (simile all'utilizzo di BulkExecutor. BulkImportAsync), è necessario disporre di chiamate simultanee a `CreateItemAsync`. Ad esempio:

   :::code language="csharp" source="~/samples-cosmosdb-dotnet-v3/Microsoft.Azure.Cosmos.Samples/Usage/BulkExecutorMigration/Program.cs" ID="BulkImport":::

Se si desidera eseguire l' *aggiornamento* in blocco (simile all'utilizzo di [BulkExecutor. BulkUpdateAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.cosmosdb.bulkexecutor.bulkexecutor.bulkupdateasync)), è necessario disporre di chiamate `ReplaceItemAsync` simultanee al metodo dopo l'aggiornamento del valore dell'elemento. Ad esempio:

   :::code language="csharp" source="~/samples-cosmosdb-dotnet-v3/Microsoft.Azure.Cosmos.Samples/Usage/BulkExecutorMigration/Program.cs" ID="BulkUpdate":::

Se si desidera eseguire l' *eliminazione* in blocco (simile all'utilizzo di [BulkExecutor. BulkDeleteAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.cosmosdb.bulkexecutor.bulkexecutor.bulkdeleteasync)), è necessario disporre di chiamate simultanee a `DeleteItemAsync`, `id` con la chiave di partizione e di ogni elemento. Ad esempio:

   :::code language="csharp" source="~/samples-cosmosdb-dotnet-v3/Microsoft.Azure.Cosmos.Samples/Usage/BulkExecutorMigration/Program.cs" ID="BulkDelete":::

## <a name="capture-task-result-state"></a>Stato del risultato dell'attività di acquisizione

Negli esempi di codice precedenti è stato creato un elenco simultaneo di attività e il `CaptureOperationResponse` metodo è stato chiamato su ognuna di queste attività. Questo metodo è un'estensione che consente di mantenere uno *schema di risposta simile* a BulkExecutor, mediante l'acquisizione di eventuali errori e la verifica dell' [utilizzo delle unità richiesta](request-units.md).

   :::code language="csharp" source="~/samples-cosmosdb-dotnet-v3/Microsoft.Azure.Cosmos.Samples/Usage/BulkExecutorMigration/Program.cs" ID="CaptureOperationResult":::

Dove `OperationResponse` viene dichiarata come:

   :::code language="csharp" source="~/samples-cosmosdb-dotnet-v3/Microsoft.Azure.Cosmos.Samples/Usage/BulkExecutorMigration/Program.cs" ID="OperationResult":::

## <a name="execute-operations-concurrently"></a>Esecuzione simultanea di operazioni

Per tenere traccia dell'intero elenco di attività, si usa questa classe helper:

   :::code language="csharp" source="~/samples-cosmosdb-dotnet-v3/Microsoft.Azure.Cosmos.Samples/Usage/BulkExecutorMigration/Program.cs" ID="BulkOperationsHelper":::

Il `ExecuteAsync` metodo resterà in attesa fino al completamento di tutte le operazioni e sarà possibile utilizzarlo nel modo seguente:

   :::code language="csharp" source="~/samples-cosmosdb-dotnet-v3/Microsoft.Azure.Cosmos.Samples/Usage/BulkExecutorMigration/Program.cs" ID="WhenAll":::

## <a name="capture-statistics"></a>Acquisisci statistiche

Il codice precedente attende il completamento di tutte le operazioni e calcola le statistiche richieste. Queste statistiche sono simili a quelle del [BulkImportResponse](https://docs.microsoft.com/dotnet/api/microsoft.azure.cosmosdb.bulkexecutor.bulkimport.bulkimportresponse)della libreria dell'executor di massa.

   :::code language="csharp" source="~/samples-cosmosdb-dotnet-v3/Microsoft.Azure.Cosmos.Samples/Usage/BulkExecutorMigration/Program.cs" ID="ResponseType":::

`BulkOperationResponse` Contiene:

1. Tempo totale impiegato per elaborare l'elenco di operazioni tramite il supporto bulk.
1. Numero di operazioni completate correttamente.
1. Totale delle unità richiesta utilizzate.
1. Se si verificano errori, viene visualizzato un elenco di tuple che contengono l'eccezione e l'elemento associato a scopo di registrazione e identificazione.

## <a name="retry-configuration"></a>Riprova configurazione

Per la libreria dell'esecutore bulk sono `MaxRetryWaitTimeInSeconds` state `MaxRetryAttemptsOnThrottledRequests` [fornite indicazioni](bulk-executor-dot-net.md#bulk-import-data-to-an-azure-cosmos-account) per impostare e `0` di [RetryOptions](https://docs.microsoft.com/dotnet/api/microsoft.azure.documents.client.connectionpolicy.retryoptions) su per delegare il controllo alla libreria.

Per il supporto bulk in .NET SDK, non esiste alcun comportamento nascosto. È possibile configurare le opzioni di ripetizione dei tentativi direttamente tramite [CosmosClientOptions. MaxRetryAttemptsOnRateLimitedRequests](https://docs.microsoft.com/dotnet/api/microsoft.azure.cosmos.cosmosclientoptions.maxretryattemptsonratelimitedrequests) e [CosmosClientOptions. MaxRetryWaitTimeOnRateLimitedRequests](https://docs.microsoft.com/dotnet/api/microsoft.azure.cosmos.cosmosclientoptions.maxretrywaittimeonratelimitedrequests).

> [!NOTE]
> Nei casi in cui le unità richiesta di cui è stato effettuato il provisioning sono molto inferiori a quelle previste in base alla quantità di dati, è consigliabile impostare tali unità su valori elevati. L'operazione bulk avrà più tempo, ma avrà una maggiore probabilità di essere completata a causa dei tentativi più elevati.

## <a name="performance-improvements"></a>Miglioramenti delle prestazioni.

Come per le altre operazioni con .NET SDK, l'uso delle API di flusso comporta prestazioni migliori ed evita la serializzazione non necessaria. 

L'uso delle API di flusso è possibile solo se la natura dei dati usati corrisponde a quella di un flusso di byte (ad esempio, i flussi di file). In questi casi, l'utilizzo `CreateItemStreamAsync`dei `ReplaceItemStreamAsync`metodi, `DeleteItemStreamAsync` o e l'utilizzo `ResponseMessage` di `ItemResponse`(anziché) aumentano la velocità effettiva che è possibile ottenere.

## <a name="next-steps"></a>Passaggi successivi

* Per ulteriori informazioni sulle versioni di .NET SDK, vedere l'articolo [Azure Cosmos DB SDK](sql-api-sdk-dotnet.md) .
* Ottenere il [codice sorgente](https://github.com/Azure/azure-cosmos-dotnet-v3/tree/master/Microsoft.Azure.Cosmos.Samples/Usage/BulkExecutorMigration) completo della migrazione da GitHub.
* [Esempi bulk aggiuntivi su GitHub](https://github.com/Azure/azure-cosmos-dotnet-v3/tree/master/Microsoft.Azure.Cosmos.Samples/Usage/BulkSupport)
