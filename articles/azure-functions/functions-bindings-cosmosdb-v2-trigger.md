---
title: Trigger Azure Cosmos DB per functions 2. x e versioni successive
description: Informazioni su come usare il trigger Azure Cosmos DB in funzioni di Azure.
author: craigshoemaker
ms.topic: reference
ms.date: 02/24/2020
ms.author: cshoe
ms.custom: devx-track-csharp, devx-track-python
ms.openlocfilehash: 838d6244127bc1b3609ab5e925e54dbab7fe3a2d
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "88212684"
---
# <a name="azure-cosmos-db-trigger-for-azure-functions-2x-and-higher"></a>Trigger Azure Cosmos DB per funzioni di Azure 2. x e versioni successive

Il trigger di Azure Cosmos DB usa il [feed di modifiche di Azure Cosmos DB](../cosmos-db/change-feed.md) per rimanere in ascolto degli inserimenti e degli aggiornamenti nelle partizioni. Il feed delle modifiche pubblica aggiunte e aggiornamenti, non eliminazioni.

Per informazioni sui dettagli di impostazione e configurazione, vedere la [panoramica](./functions-bindings-cosmosdb-v2.md).

<a id="example" name="example"></a>

# <a name="c"></a>[C#](#tab/csharp)

L'esempio seguente illustra una [funzione C#](functions-dotnet-class-library.md) che viene richiamata in caso di inserimenti o aggiornamenti nel database e nella raccolta specificati.

```cs
using Microsoft.Azure.Documents;
using Microsoft.Azure.WebJobs;
using Microsoft.Azure.WebJobs.Host;
using System.Collections.Generic;
using Microsoft.Extensions.Logging;

namespace CosmosDBSamplesV2
{
    public static class CosmosTrigger
    {
        [FunctionName("CosmosTrigger")]
        public static void Run([CosmosDBTrigger(
            databaseName: "ToDoItems",
            collectionName: "Items",
            ConnectionStringSetting = "CosmosDBConnection",
            LeaseCollectionName = "leases",
            CreateLeaseCollectionIfNotExists = true)]IReadOnlyList<Document> documents,
            ILogger log)
        {
            if (documents != null && documents.Count > 0)
            {
                log.LogInformation($"Documents modified: {documents.Count}");
                log.LogInformation($"First document Id: {documents[0].Id}");
            }
        }
    }
}
```

# <a name="c-script"></a>[Script C#](#tab/csharp-script)

L'esempio seguente mostra un'associazione di trigger di Cosmos DB in un file *function.json* e una [funzione script C#](functions-reference-csharp.md) che usa l'associazione. La funzione scrive messaggi di log quando vengono aggiunti o modificati Cosmos DB record.

Ecco i dati di associazione nel file *function.json*:

```json
{
    "type": "cosmosDBTrigger",
    "name": "documents",
    "direction": "in",
    "leaseCollectionName": "leases",
    "connectionStringSetting": "<connection-app-setting>",
    "databaseName": "Tasks",
    "collectionName": "Items",
    "createLeaseCollectionIfNotExists": true
}
```

Ecco il codice script C#:

```cs
    #r "Microsoft.Azure.DocumentDB.Core"

    using System;
    using Microsoft.Azure.Documents;
    using System.Collections.Generic;
    using Microsoft.Extensions.Logging;

    public static void Run(IReadOnlyList<Document> documents, ILogger log)
    {
      log.LogInformation("Documents modified " + documents.Count);
      log.LogInformation("First document Id " + documents[0].Id);
    }
```

# <a name="javascript"></a>[JavaScript](#tab/javascript)

L'esempio seguente mostra un'associazione di trigger di Cosmos DB in un file *function.json* e una [funzione JavaScript](functions-reference-node.md) che usa l'associazione. La funzione scrive messaggi di log quando vengono aggiunti o modificati Cosmos DB record.

Ecco i dati di associazione nel file *function.json*:

```json
{
    "type": "cosmosDBTrigger",
    "name": "documents",
    "direction": "in",
    "leaseCollectionName": "leases",
    "connectionStringSetting": "<connection-app-setting>",
    "databaseName": "Tasks",
    "collectionName": "Items",
    "createLeaseCollectionIfNotExists": true
}
```

Ecco il codice JavaScript:

```javascript
    module.exports = function (context, documents) {
      context.log('First document Id modified : ', documents[0].id);

      context.done();
    }
```

# <a name="python"></a>[Python](#tab/python)

L'esempio seguente mostra un'associazione di trigger di Cosmos DB in un file *function.json* e una [funzione Python](functions-reference-python.md) che usa l'associazione. La funzione scrive i messaggi di log quando vengono modificati i record di Cosmos DB.

Ecco i dati di associazione nel file *function.json*:

```json
{
    "name": "documents",
    "type": "cosmosDBTrigger",
    "direction": "in",
    "leaseCollectionName": "leases",
    "connectionStringSetting": "<connection-app-setting>",
    "databaseName": "Tasks",
    "collectionName": "Items",
    "createLeaseCollectionIfNotExists": true
}
```

Ecco il codice Python:

```python
    import logging
    import azure.functions as func


    def main(documents: func.DocumentList) -> str:
        if documents:
            logging.info('First document Id modified: %s', documents[0]['id'])
```

# <a name="java"></a>[Java](#tab/java)

Questa funzione viene richiamata quando sono presenti inserimenti o aggiornamenti nel database e nella raccolta specificati.

```java
    @FunctionName("cosmosDBMonitor")
    public void cosmosDbProcessor(
        @CosmosDBTrigger(name = "items",
            databaseName = "ToDoList",
            collectionName = "Items",
            leaseCollectionName = "leases",
            createLeaseCollectionIfNotExists = true,
            connectionStringSetting = "AzureCosmosDBConnection") String[] items,
            final ExecutionContext context ) {
                context.getLogger().info(items.length + "item(s) is/are changed.");
            }
```


Nella [libreria di runtime di funzioni Java](/java/api/overview/azure/functions/runtime), usare l'annotazione `@CosmosDBTrigger` per i parametri il cui valore deriva da Cosmos DB.  Questa annotazione è utilizzabile con i tipi Java nativi, con oggetti POJO o con valori nullable tramite `Optional<T>`.

---

## <a name="attributes-and-annotations"></a>Attributi e annotazioni

# <a name="c"></a>[C#](#tab/csharp)

Nelle [librerie di classi C#](functions-dotnet-class-library.md) usare l'attributo [CosmosDBTrigger](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/master/src/WebJobs.Extensions.CosmosDB/Trigger/CosmosDBTriggerAttribute.cs).

Il costruttore dell'attributo accetta il nome del database e il nome della raccolta. Per informazioni su tali impostazioni e altre proprietà che è possibile configurare, vedere [Trigger - configurazione](#configuration). Di seguito è riportato un esempio dell'attributo `CosmosDBTrigger` in una firma del metodo:

```csharp
    [FunctionName("DocumentUpdates")]
    public static void Run(
        [CosmosDBTrigger("database", "collection", ConnectionStringSetting = "myCosmosDB")]
    IReadOnlyList<Document> documents,
        ILogger log)
    {
        ...
    }
```

Per un esempio completo, vedere [trigger](#example).

# <a name="c-script"></a>[Script C#](#tab/csharp-script)

Gli attributi non sono supportati da Script C#.

# <a name="javascript"></a>[JavaScript](#tab/javascript)

Gli attributi non sono supportati da JavaScript.

# <a name="python"></a>[Python](#tab/python)

Gli attributi non sono supportati da Python.

# <a name="java"></a>[Java](#tab/java)

Dalla [libreria di runtime di funzioni Java](/java/api/overview/azure/functions/runtime), usare l' `@CosmosDBInput` annotazione sui parametri che leggono i dati da Cosmos DB.

---

## <a name="configuration"></a>Configurazione

Nella tabella seguente sono illustrate le proprietà di configurazione dell'associazione impostate nel file *function.json* e nell'attributo `CosmosDBTrigger`.

|Proprietà di function.json | Proprietà dell'attributo |Descrizione|
|---------|---------|----------------------|
|**type** | n/d | Il valore deve essere impostato su `cosmosDBTrigger`. |
|**direction** | n/d | Il valore deve essere impostato su `in`. Questo parametro viene impostato automaticamente quando si crea il trigger nel portale di Azure. |
|**nome** | n/d | Il nome della variabile usato nel codice funzione che rappresenta l'elenco di documenti con le modifiche. |
|**connectionStringSetting**|**ConnectionStringSetting** | Il nome di un'impostazione dell'app che contiene la stringa di connessione usata per connettersi all'account di Azure Cosmos DB monitorato. |
|**databaseName**|**DatabaseName**  | Il nome del database di Azure Cosmos DB con la raccolta monitorata. |
|**collectionName** |**CollectionName** | Il nome della raccolta monitorata. |
|**leaseConnectionStringSetting** | **LeaseConnectionStringSetting** | Opzionale Nome di un'impostazione dell'app che contiene la stringa di connessione all'account Azure Cosmos DB che contiene la raccolta di lease. Se non impostato, viene usato il valore `connectionStringSetting`. Questo parametro viene impostato automaticamente al momento della creazione dell'associazione nel portale. La stringa di connessione per la raccolta di lease deve avere autorizzazioni di scrittura.|
|**leaseDatabaseName** |**LeaseDatabaseName** | (Facoltativo) Il nome del database in cui si trova la raccolta usata per archiviare i lease. Se non impostato, viene usato il valore dell'impostazione `databaseName`. Questo parametro viene impostato automaticamente al momento della creazione dell'associazione nel portale. |
|**leaseCollectionName** | **LeaseCollectionName** | (Facoltativo) Il nome della raccolta usata per archiviare i lease. Se non impostato, viene usato il valore `leases`. |
|**createLeaseCollectionIfNotExists** | **CreateLeaseCollectionIfNotExists** | (Facoltativo) Se impostato su `true`, la raccolta di lease viene creata automaticamente se non esiste già. Il valore predefinito è `false`. |
|**leasesCollectionThroughput**| **LeasesCollectionThroughput**| Opzionale Definisce il numero di unità richiesta da assegnare quando viene creata la raccolta dei lease. Questa impostazione viene utilizzata solo quando `createLeaseCollectionIfNotExists` è impostato su `true` . Questo parametro viene impostato automaticamente al momento della creazione dell'associazione tramite il portale.
|**leaseCollectionPrefix**| **LeaseCollectionPrefix**| Opzionale Se impostato, il valore viene aggiunto come prefisso ai lease creati nella raccolta di lease per questa funzione. L'uso di un prefisso consente a due funzioni di Azure separate di condividere la stessa raccolta di lease usando prefissi diversi.
|**feedPollDelay**| **FeedPollDelay**| Opzionale Tempo (in millisecondi) per il ritardo tra il polling di una partizione per le nuove modifiche nel feed, dopo che tutte le modifiche correnti sono state svuotate. Il valore predefinito è 5.000 millisecondi o 5 secondi.
|**leaseAcquireInterval**| **LeaseAcquireInterval**| (Facoltativo) Se impostato, definisce, in millisecondi, l'intervallo per l'avvio di un'attività che consente di calcolare se le partizioni sono distribuite equamente tra le istanze di host note. Il valore predefinito è 13000 (13 secondi).
|**leaseExpirationInterval**| **LeaseExpirationInterval**| (Facoltativo) Se impostato, definisce, in millisecondi, l'intervallo che indica la durata di un lease su un lease che rappresenta una partizione. Se il lease non viene rinnovato entro questo intervallo, ne comporterà la scadenza e la proprietà della partizione passerà a un'altra istanza. Il valore predefinito è 60000 (60 secondi).
|**leaseRenewInterval**| **LeaseRenewInterval**| (Facoltativo) Se impostato, definisce, in millisecondi, l'intervallo di rinnovo per tutti i lease per le partizioni attualmente occupate da un'istanza. Il valore predefinito è 17000 (17 secondi).
|**checkpointFrequency**| **CheckpointFrequency**| (Facoltativo) Se impostato, definisce, in millisecondi, l'intervallo tra i checkpoint dei lease. Il valore predefinito è sempre dopo ogni chiamata di funzione.
|**maxItemsPerInvocation**| **MaxItemsPerInvocation**| Opzionale Se impostata, questa proprietà imposta il numero massimo di elementi ricevuti per ogni chiamata di funzione. Se le operazioni nella raccolta monitorata vengono eseguite tramite stored procedure, l' [ambito della transazione](../cosmos-db/stored-procedures-triggers-udfs.md#transactions) viene mantenuto durante la lettura di elementi dal feed delle modifiche. Di conseguenza, il numero di elementi ricevuti potrebbe essere superiore al valore specificato, in modo che gli elementi modificati dalla stessa transazione vengano restituiti come parte di un batch atomico.
|**startFromBeginning**| **StartFromBeginning**| Opzionale Questa opzione indica al trigger di leggere le modifiche dall'inizio della cronologia delle modifiche della raccolta anziché partire dall'ora corrente. La lettura dall'inizio funziona solo la prima volta che il trigger viene avviato, come nelle esecuzioni successive, i checkpoint sono già archiviati. L'impostazione di questa opzione su `true` quando sono già stati creati lease non ha alcun effetto. |
|**preferredLocations**| **PreferredLocations**| Opzionale Definisce le posizioni preferite (aree) per gli account di database con replica geografica nel servizio Azure Cosmos DB. I valori devono essere separati da virgole. Ad esempio, "Stati Uniti orientali, Stati Uniti centro-meridionali, Europa settentrionale". |

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

## <a name="usage"></a>Utilizzo

Il trigger richiede una seconda raccolta usata per archiviare i _lease_ nelle partizioni. Sia la raccolta monitorata che la raccolta che contiene i lease deve essere disponibile affinché il trigger funzioni.

>[!IMPORTANT]
> Se più funzioni sono configurate per l'uso di un trigger di Cosmos DB per la stessa raccolta, ogni funzione deve usare una raccolta di lease dedicata oppure è necessario specificare un diverso `LeaseCollectionPrefix` per ogni funzione. In caso contrario verrà attivata solo una delle funzioni. Per informazioni sul prefisso, vedere la [sezione relativa alla configurazione](#configuration).

Il trigger non indica se un documento è stato aggiornato o aggiunto, fornisce solo il documento stesso. Se è necessario gestire aggiornamenti e aggiunte in modo diverso, è possibile implementare campi di timestamp per le aggiunte o gli aggiornamenti.

## <a name="next-steps"></a>Passaggi successivi

- [Leggere un documento di Azure Cosmos DB (associazione di input)](./functions-bindings-cosmosdb-v2-input.md)
- [Salvare le modifiche apportate a un documento Azure Cosmos DB (associazione di output)](./functions-bindings-cosmosdb-v2-output.md)
