---
title: Eseguire la migrazione dalla libreria dell'esecutore bulk al supporto bulk in Azure Cosmos DB .NET V3 SDKMigrate from the bulk executor library to the bulk support in Azure Cosmos DB .NET V3 SDK
description: Informazioni su come eseguire la migrazione dell'applicazione dall'utilizzo della libreria dell'esecutore in blocco al supporto in blocco in Azure Cosmos DB SDK V3
author: ealsur
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 04/06/2020
ms.author: maquaran
ms.openlocfilehash: 820a5398d84122659b1676b7d5722bce08b1837d
ms.sourcegitcommit: 441db70765ff9042db87c60f4aa3c51df2afae2d
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/06/2020
ms.locfileid: "80755966"
---
# <a name="migrate-from-the-bulk-executor-library-to-the-bulk-support-in-azure-cosmos-db-net-v3-sdk"></a>Eseguire la migrazione dalla libreria dell'esecutore bulk al supporto bulk in Azure Cosmos DB .NET V3 SDKMigrate from the bulk executor library to the bulk support in Azure Cosmos DB .NET V3 SDK

In questo articolo vengono descritti i passaggi necessari per eseguire la migrazione del codice di un'applicazione esistente che utilizza la [libreria dell'esecutore bulk .NET](bulk-executor-dot-net.md) alla funzionalità di [supporto in blocco](tutorial-sql-api-dotnet-bulk-import.md) nella versione più recente di .NET SDK.

## <a name="enable-bulk-support"></a>Abilitare il supporto in bloccoEnable bulk support

Abilitare il `CosmosClient` supporto in blocco nell'istanza tramite la configurazione [AllowBulkExecution:Enable](https://docs.microsoft.com/dotnet/api/microsoft.azure.cosmos.cosmosclientoptions.allowbulkexecution) bulk support on the instance through the AllowBulkExecution configuration:

   :::code language="csharp" source="~/samples-cosmosdb-dotnet-v3/Microsoft.Azure.Cosmos.Samples/Usage/BulkExecutorMigration/Program.cs" ID="Initialization":::

## <a name="create-tasks-for-each-operation"></a>Creazione di attività per ogni operazione

Il supporto in blocco in .NET SDK funziona sfruttando [task Parallel Library](https://docs.microsoft.com/dotnet/standard/parallel-programming/task-parallel-library-tpl) e le operazioni di raggruppamento che si verificano contemporaneamente. 

Non esiste un singolo metodo che accetta l'elenco di documenti o operazioni come parametro di input, ma piuttosto, è necessario creare un Task per ogni operazione che si desidera eseguire in blocco.

Ad esempio, se l'input iniziale è un elenco di elementi in cui ogni elemento ha lo schema seguente:For example, if your initial input is a list of items where each item has the following schema:

   :::code language="csharp" source="~/samples-cosmosdb-dotnet-v3/Microsoft.Azure.Cosmos.Samples/Usage/BulkExecutorMigration/Program.cs" ID="Model":::

Se si desidera eseguire l'importazione in blocco (simile all'utilizzo di BulkExecutor.BulkImportAsync), è necessario disporre di chiamate simultanee a `CreateItemAsync` con ogni valore dell'elemento. Ad esempio:

   :::code language="csharp" source="~/samples-cosmosdb-dotnet-v3/Microsoft.Azure.Cosmos.Samples/Usage/BulkExecutorMigration/Program.cs" ID="BulkImport":::

Se si desidera eseguire l'aggiornamento in blocco (simile all'utilizzo di [BulkExecutor.BulkUpdateAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.cosmosdb.bulkexecutor.bulkexecutor.bulkupdateasync)), è necessario disporre di chiamate simultanee al `ReplaceItemAsync` metodo dopo l'aggiornamento del valore dell'elemento. *update* Ad esempio:

   :::code language="csharp" source="~/samples-cosmosdb-dotnet-v3/Microsoft.Azure.Cosmos.Samples/Usage/BulkExecutorMigration/Program.cs" ID="BulkUpdate":::

E se si desidera eseguire *l'eliminazione* in blocco (simile all'utilizzo di [BulkExecutor.BulkDeleteAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.cosmosdb.bulkexecutor.bulkexecutor.bulkdeleteasync)), è necessario disporre di chiamate simultanee a `DeleteItemAsync`, con la `id` chiave e di partizione di ogni elemento. Ad esempio:

   :::code language="csharp" source="~/samples-cosmosdb-dotnet-v3/Microsoft.Azure.Cosmos.Samples/Usage/BulkExecutorMigration/Program.cs" ID="BulkDelete":::

## <a name="capture-task-result-state"></a>Acquisire lo stato dei risultati dell'attivitàCapture task result state

Negli esempi di codice precedenti è stato creato un elenco `CaptureOperationResponse` simultaneo di attività e viene chiamato il metodo in ognuna di queste attività. Questo metodo è un'estensione che consente di mantenere uno schema di *risposta simile* a BulkExecutor, acquisiscendo eventuali errori e tenendo traccia dell'utilizzo delle unità [di richiesta.](request-units.md)

   :::code language="csharp" source="~/samples-cosmosdb-dotnet-v3/Microsoft.Azure.Cosmos.Samples/Usage/BulkExecutorMigration/Program.cs" ID="CaptureOperationResult":::

Se `OperationResponse` il è dichiarato come:

   :::code language="csharp" source="~/samples-cosmosdb-dotnet-v3/Microsoft.Azure.Cosmos.Samples/Usage/BulkExecutorMigration/Program.cs" ID="OperationResult":::

## <a name="execute-operations-concurrently"></a>Esegui operazioni contemporaneamente

Dopo aver definito l'elenco delle attività, attendere che siano tutte completate. È possibile tenere traccia del completamento delle attività definendo l'ambito dell'operazione in blocco, come illustrato nel frammento di codice seguente:

   :::code language="csharp" source="~/samples-cosmosdb-dotnet-v3/Microsoft.Azure.Cosmos.Samples/Usage/BulkExecutorMigration/Program.cs" ID="WhenAll":::

## <a name="capture-statistics"></a>Acquisire statistiche

Il codice precedente attende il completamento di tutte le operazioni e calcola le statistiche richieste. Queste statistiche sono simili a quelle della libreria dell'esecutore bulk [BulkImportResponse](https://docs.microsoft.com/dotnet/api/microsoft.azure.cosmosdb.bulkexecutor.bulkimport.bulkimportresponse).

   :::code language="csharp" source="~/samples-cosmosdb-dotnet-v3/Microsoft.Azure.Cosmos.Samples/Usage/BulkExecutorMigration/Program.cs" ID="ResponseType":::

I `BulkOperationResponse` contiene:

1. Tempo totale impiegato per elaborare l'elenco di operazioni tramite il supporto in blocco.
1. Numero di operazioni riuscite.
1. Totale delle unità richiesta consumate.
1. In caso di errori, viene visualizzato un elenco di tuple che contengono l'eccezione e l'elemento associato per la registrazione e lo scopo di identificazione.

## <a name="retry-configuration"></a>Riprovare la configurazioneRetry configuration

La libreria dell'esecutore `MaxRetryWaitTimeInSeconds` `MaxRetryAttemptsOnThrottledRequests` in blocco disponeva di [indicazioni](bulk-executor-dot-net.md#bulk-import-data-to-an-azure-cosmos-account) che indicavano l'impostazione di e di [RetryOptions](https://docs.microsoft.com/dotnet/api/microsoft.azure.documents.client.connectionpolicy.retryoptions) per `0` delegare il controllo alla libreria.

Per il supporto in blocco in .NET SDK, non esiste alcun comportamento nascosto. È possibile configurare le opzioni di ripetizione dei tentativi direttamente tramite [CosmosClientOptions.MaxRetryAttemptsOnRateLimitedRequests](https://docs.microsoft.com/dotnet/api/microsoft.azure.cosmos.cosmosclientoptions.maxretryattemptsonratelimitedrequests) e [CosmosClientOptions.MaxRetryWaitTimeOnRateLimitedRequests](https://docs.microsoft.com/dotnet/api/microsoft.azure.cosmos.cosmosclientoptions.maxretrywaittimeonratelimitedrequests).

> [!NOTE]
> Nei casi in cui le unità di richiesta di cui è stato eseguito il provisioning sono molto inferiori a quelle previste in base alla quantità di dati, è consigliabile impostarle su valori elevati. L'operazione in blocco richiederà più tempo, ma ha maggiori probabilità di riuscire completamente a causa dei tentativi più elevati.

## <a name="performance-improvements"></a>Miglioramenti delle prestazioni.

Come con altre operazioni con .NET SDK, l'utilizzo delle API di flusso consente di ottenere prestazioni migliori ed evitare la serializzazione non necessaria. 

L'utilizzo delle API di flusso è possibile solo se la natura dei dati utilizzati corrisponde a quella di un flusso di byte (ad esempio, i flussi di file). In questi casi, `CreateItemStreamAsync` `ReplaceItemStreamAsync`l'utilizzo dei metodi , o `DeleteItemStreamAsync` e l'utilizzo di `ResponseMessage` `ItemResponse`(anziché ) aumentano la velocità effettiva che è possibile ottenere.

## <a name="next-steps"></a>Passaggi successivi

* Per altre informazioni sulle versioni di .NET SDK, vedere l'articolo [azure Cosmos DB SDK.](sql-api-sdk-dotnet.md)
* Ottenere il [codice sorgente della migrazione](https://github.com/Azure/azure-cosmos-dotnet-v3/tree/master/Microsoft.Azure.Cosmos.Samples/Usage/BulkExecutorMigration) completo da GitHub.Get the complete migration source code from GitHub.
* [Ulteriori campioni di massa su GitHubAdditional bulk samples on GitHub](https://github.com/Azure/azure-cosmos-dotnet-v3/tree/master/Microsoft.Azure.Cosmos.Samples/Usage/BulkSupport)
